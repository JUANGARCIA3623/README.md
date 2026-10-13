<?php
// =============================================================================
// SBE-GAEJ Suite — Euniprincez Digital v2026
// Módulo de Sistema / Seguridad Perimétrica (Backend LicenciaAlpha)
//
// Copyright (c) 2026 Juan Valentín García Espinoza
// ID: GAEJ940310HSPRSN02
// Licencia: MIT (Consulte el archivo LICENSE para más detalles)
// =============================================================================

header("Content-Type: application/json; charset=UTF-8");
header("Access-Control-Allow-Methods: POST");

// 1. Validación Perimétrica (Protección contra Timing Attacks)
$headers = getallheaders();
$x_internal_token = $headers['x-internal-token'] ?? '';
$secret_api_key = getenv('INTERNAL_API_SECRET'); // Cargar desde el entorno

// Validación de tiempo constante (Blindaje Euniprincez)
if (empty($x_internal_token) || !hash_equals($secret_api_key, $x_internal_token)) {
    http_response_code(403);
    echo json_encode(["status" => "error", "message" => "Auditoría Fallida: Acceso denegado al perímetro de seguridad."]);
    exit();
}

// 2. Configuración de conexión a Base de Datos (Segura)
define('DB_HOST', getenv('DB_HOST') ?: 'localhost');
define('DB_USER', getenv('DB_USER') ?: 'euniprincez_admin');
define('DB_PASS', getenv('DB_PASS')); // NUNCA hardcodear en producción
define('DB_NAME', 'digital_v2026');
define('SECRET_SALT', getenv('SECRET_SALT')); 

// Nota: Configurado para MySQL (mysqli) según tu script original
$conn = new mysqli(DB_HOST, DB_USER, DB_PASS, DB_NAME);

if ($conn->connect_error) {
    http_response_code(500);
    echo json_encode(["status" => "error", "message" => "Fallo de conexión interna."]);
    exit();
}

// 3. Captura y decodificación de datos JSON provenientes de Streamlit
$inputData = file_get_contents("php://input");
$data = json_decode($inputData, true);

if (!isset($data['rfc']) || !isset($data['serial_number']) || !isset($data['es_valido'])) {
    http_response_code(400);
    echo json_encode(["status" => "error", "message" => "Parámetros de auditoría incompletos."]);
    exit();
}

// Sanitización estricta por expresiones regulares
$rfc = preg_replace("/[^A-Z0-9]/", "", strtoupper($data['rfc'])); 
$serial = preg_replace("/[^0-9]/", "", $data['serial_number']);   
$esValido = filter_var($data['es_valido'], FILTER_VALIDATE_BOOLEAN);

// 4. Generación del Hash de Integridad Criptográfica (Factor 2x -> 64 chars)
$estadoTexto = $esValido ? 'ACTIVO_VIGENTE' : 'BLOQUEADO_EXPIRADO';
$hashControl = hash('sha256', $rfc . $serial . $estadoTexto . SECRET_SALT);

// 5. Actualización en la BD de la LicenciaAlpha mediante Sentencias Preparadas
$stmt = $conn->prepare("UPDATE licencias_alpha SET estatus = ?, csd_serial = ?, hash_auditoria = ?, ultima_verificacion = NOW() WHERE rfc_titular = ?");
$stmt->bind_param("ssss", $estadoTexto, $serial, $hashControl, $rfc);

if ($stmt->execute()) {
    if ($stmt->affected_rows > 0) {
        echo json_encode([
            "status" => "success",
            "message" => "LicenciaAlpha actualizada correctamente para el RFC {$rfc}.",
            "codigo_hash" => substr($hashControl, 0, 16) . "..." // Enmascaramiento para UI
        ]);
    } else {
        echo json_encode([
            "status" => "warning", 
            "message" => "No se encontró una licencia registrada o activa para el RFC proporcionado."
        ]);
    }
} else {
    http_response_code(500);
    echo json_encode(["status" => "error", "message" => "Error crítico al registrar la firma del sello."]);
}

$stmt->close();
$conn->close();
?>
<div align="center">
    <a href="https://www.php.net">
        <img
            alt="PHP"
            src="https://www.php.net/images/logos/new-php-logo.svg"
            width="150">
    </a>
</div>

# The PHP Interpreter

PHP is a popular general-purpose scripting language that is especially suited to
web development. Fast, flexible and pragmatic, PHP powers everything from your
blog to the most popular websites in the world.

PHP is distributed under the [Modified BSD License](LICENSE)
(SPDX-License-Identifier: `BSD-3-Clause`).

[![Test](https://github.com/php/php-src/actions/workflows/test.yml/badge.svg)](https://github.com/php/php-src/actions/workflows/test.yml)
[![Fuzzing Status](https://oss-fuzz-build-logs.storage.googleapis.com/badges/php.svg)](https://issues.oss-fuzz.com/issues?q=project:php)

## Documentation

The PHP manual is available at [php.net/docs](https://www.php.net/docs).

## Installation

### Prebuilt packages and binaries

Prebuilt packages and binaries can be used to get up and running fast with PHP.

For Windows, the PHP binaries can be obtained from
[windows.php.net](https://windows.php.net). After extracting the archive the
`*.exe` files are ready to use.

For other systems, see the [installation chapter](https://www.php.net/install).

### Building PHP source code

*For Windows, see [Build your own PHP on Windows](https://wiki.php.net/internals/windows/stepbystepbuild_sdk_2).*

For a minimal PHP build from Git, you will need autoconf, bison, and re2c. For
a default build, you will additionally need libxml2 and libsqlite3.

On Ubuntu, you can install these using:

```shell
sudo apt install -y pkg-config build-essential autoconf bison re2c libxml2-dev libsqlite3-dev
```

On Fedora, you can install these using:

```shell
sudo dnf install re2c bison autoconf make ccache libxml2-devel sqlite-devel
```

On MacOS, you can install these using `brew`:

```shell
brew install autoconf bison re2c libiconv libxml2 sqlite
```

or with `MacPorts`:

```shell
sudo port install autoconf bison re2c libiconv libxml2 sqlite3
```

Generate configure:

```shell
./buildconf
```

Configure your build. `--enable-debug` is recommended for development, see
`./configure --help` for a full list of options.

```shell
# For development
./configure --enable-debug
# For production
./configure
```

Build PHP. To speed up the build, specify the maximum number of jobs using the
`-j` argument:

```shell
make -j4
```

The number of jobs should usually match the number of available cores, which
can be determined using `nproc`.

## Testing PHP source code

PHP ships with an extensive test suite, the command `make test` is used after
successful compilation of the sources to run this test suite.

Tests run in parallel by default, using up to 10 detected logical processors.
Set `-jN` in `TEST_PHP_ARGS` or `TESTS` to override the worker count:

```shell
make TEST_PHP_ARGS=-j4 test
```

This runs `make test` with a maximum of 4 concurrent jobs. Alternatively,
use `-j1` to run tests sequentially.

Use the `TEST_PHP_ARGS` or `TESTS` variable to test only specific directories:

```shell
make TESTS=tests/lang/ test
```

The [qa.php.net](https://qa.php.net) site provides more detailed info about
testing and quality assurance.

## Installing PHP built from source

After a successful build (and test), PHP may be installed with:

```shell
make install
```

Depending on your permissions and prefix, `make install` may need superuser
permissions.

## PHP extensions

Extensions provide additional functionality on top of PHP. PHP consists of many
essential bundled extensions. Additional extensions can be found on the
[PIE Extensions](https://packagist.org/extensions) list, and installed with
[🥧 PIE, the PHP Installer for Extensions](https://github.com/php/pie) or the
deprecated PHP Extension Community Library - [PECL](https://pecl.php.net).

## Contributing

The PHP source code is located in the Git repository at
[github.com/php/php-src](https://github.com/php/php-src). Contributions are most
welcome by forking the repository and sending a pull request.

Discussions are done on GitHub, but depending on the topic can also be relayed
to the official PHP developer mailing list internals@lists.php.net.

New features require an RFC and must be accepted by the developers. See
[Request for comments - RFC](https://wiki.php.net/rfc) and
[Voting on PHP features](https://wiki.php.net/rfc/voting) for more information
on the process.

Bug fixes don't require an RFC. If the bug has a GitHub issue, reference it in
the commit message using `GH-NNNNNN`. Use `#NNNNNN` for tickets in the old
[bugs.php.net](https://bugs.php.net) bug tracker.

    Fix GH-7815: php_uname doesn't recognise latest Windows versions
    Fix #55371: get_magic_quotes_gpc() throws deprecation warning

See [Git workflow](https://wiki.php.net/vcs/gitworkflow) for details on how pull
requests are merged.

### Guidelines for contributors

See further documents in the repository for more information on how to
contribute:

- [Contributing to PHP](/CONTRIBUTING.md)
- [PHP coding standards](/CODING_STANDARDS.md)
- [Internal documentation](https://php.github.io/php-src/)
- [Mailing list rules](/docs/mailinglist-rules.md)
- [PHP release process](/docs/release-process.md)

## Credits

For the list of people who've put work into PHP, please see the
[PHP credits page](https://www.php.net/credits.php).
