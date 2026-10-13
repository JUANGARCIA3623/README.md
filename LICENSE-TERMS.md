# Repository Licensing & Usage Terms

Copyright (C) 2026 Juan Valentín García Espinoza (ID: gaej940310hsprsn02). All rights reserved.

This repository uses a split-licensing structure. Please review the terms below to determine how specific files may be used.

---

### 1. Default Open Source License (GPL-3.0)
Except as listed in Section 2, all source code and documentation in this repository are licensed under the **GNU General Public License v3.0 or later** (GPL-3.0-or-later).

* Full license text: https://www.gnu.org/licenses/gpl-3.0.en.html
* SPDX Identifier: `GPL-3.0-or-later`

You may copy, modify, and redistribute these GPL-covered files, provided derivative works remain under GPL-3.0 and retain copyright notices.

---

### 2. Proprietary Files (Exceptions)
Files explicitly listed in [`PROPRIETARY_FILES.md`](./PROPRIETARY_FILES.md) (or [`PROPRIETARY_README.md`](./PROPRIETARY_README.md)) are **not** licensed under the GPL-3.0. They remain **All Rights Reserved** by Juan Valentín García Espinoza (ID: gaej940310hsprsn02).

* These files may not be copied, modified, or redistributed without explicit written permission.
* In the event of a conflict between a file's location and `PROPRIETARY_FILES.md`, the proprietary designation in `PROPRIETARY_FILES.md` takes precedence.

---

### 3. Third-Party Components
Third-party modules or libraries included here carry their own original licenses (found in their respective headers or a [`THIRD_PARTY_LICENSES.md`](./THIRD_PARTY_LICENSES.md) file). Their original terms apply.

---

### 4. Commercial Enquiries & Permissions
To request permission for proprietary components, reach out via the contacts listed in [`SECURITY.md`](./SECURITY.md).

---

### 5. Contributions
By submitting pull requests or patches, you agree that your contributions will be licensed under GPL-3.0 unless explicitly agreed otherwise in writing prior to merging.
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
