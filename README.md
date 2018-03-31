dReal: An SMT Solver for Nonlinear Theories of Reals

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Build Status](https://travis-ci.org/dreal/dreal4.svg?branch=master)](https://travis-ci.org/dreal/dreal4)
[![codecov](https://codecov.io/gh/dreal/dreal4/branch/master/graph/badge.svg)](https://codecov.io/gh/dreal/dreal4)

Required Packages
=================

The following packages are required to build dReal:

 - C++14-compatible compiler
   - Ubuntu:
   [g++-7](https://gcc.gnu.org/gcc-7),
   [g++-6](https://gcc.gnu.org/gcc-6),
   [g++-5](https://gcc.gnu.org/gcc-5),
   [clang++-6.0](http://releases.llvm.org/6.0.0/tools/clang/docs),
   [clang++-5.0](http://releases.llvm.org/5.0.0/tools/clang/docs),
   [clang++-4.0](http://releases.llvm.org/4.0.0/tools/clang/docs),
   [clang++-3.9](http://releases.llvm.org/3.9.0/tools/clang/docs)
   - macOS: [Apple clang++](https://developer.apple.com/library/content/documentation/CompilerTools/Conceptual/LLVMCompilerOverview/index.html)
 - [Bazel](https://bazel.build)
 - [Flex](https://www.gnu.org/software/flex) and [Bison](https://www.gnu.org/software/bison)
 - [Clp](https://projects.coin-or.org/Clp)
 - [IBEX](https://github.com/ibex-team/ibex-lib)
 - [nlopt](http://nlopt.readthedocs.io)
 - [python2.7](https://www.python.org/downloads/release/python-2714/)

dReal is using the following external packages. It checks out the sources of them and builds internally. No installation required for them:

 - [Drake](http://drake.mit.edu)'s symbolic library - [BSD 3-Clause](https://raw.githubusercontent.com/RobotLocomotion/drake/master/LICENSE.TXT)
 - [Google Test](https://github.com/google/googletest) - [BSD 3-Clause](https://raw.githubusercontent.com/google/googletest/master/googletest/LICENSE)
 - [PicoSat SAT solver](http://fmv.jku.at/picosat) - [MIT](http://fmv.jku.at/picosat/LICENSE)
 - [ezOptionParser](http://ezoptionparser.sourceforge.net) - [MIT](https://raw.githubusercontent.com/dreal-deps/ezoptionparser/master/MIT-LICENSE)
 - [fmtlib](http://fmtlib.net/latest/index.html) - [BSD 2-Clause](https://raw.githubusercontent.com/fmtlib/fmt/master/LICENSE.rst)
 - [spdlog](https://github.com/gabime/spdlog) - [MIT](https://raw.githubusercontent.com/gabime/spdlog/master/LICENSE)
 - [pybind11](http://pybind11.readthedocs.io/en/master) - [BSD 3-Clause](https://raw.githubusercontent.com/pybind/pybind11/master/LICENSE)

How to Install dReal
====================

macOS 10.13 / 10.12 / 10.11:

```bash
./setup/mac/install.sh
dreal
```

Ubuntu 16.04 / Ubuntu 14.04:

```bash
sudo ./setup/ubuntu/`lsb_release -r -s`/install.sh
/opt/dreal/4.18.03.3/bin/dreal
```


How to Build dReal
==================

Install Prerequsites
--------------------

macOS 10.13 / 10.12 / 10.11:

```bash
./setup/mac/install_prereqs.sh
```

Ubuntu 16.04 / 14.04

```bash
sudo ./setup/ubuntu/`lsb_release -r -s`/install_prereqs.sh
```

Build and Test
--------------

```bash
bazel build //...
bazel test //...                     # Run all tests
./bazel-bin/dreal/dreal <smt2_file>  # Run .smt2 file
```

By default, it builds a release build. To build a debug-build, run
`bazel build //... -c dbg`. In macOS, pass `--config=apple_debug` to
allow lldb/gdb to show symbols.

In Ubuntu, we use `g++-5` as a default compiler. To use other
compilers, pass `--compiler` option to bazel (for example `--compiler
clang-4.0`). See
[tools/BUILD](https://github.com/dreal/dreal4/blob/master/tools/BUILD#L50-L68)
file for more information.

C++ Documentation
-----------------

Please check https://dreal.github.io/dreal4.

Build Debian Package
--------------------

Run `bazel build //:package_debian` and find `.deb` file in `bazel-bin` directory.


How to Build Compilation Database
-----------------------------------

To build a [Compilation
Database](https://clang.llvm.org/docs/JSONCompilationDatabase.html),
run:

```bash
curl -L https://github.com/grailbio/bazel-compilation-database/archive/0.2.tar.gz | tar -xz
bazel-compilation-database-0.2/generate.sh
```

How to Use dReal as a Library
=============================

We have prepared the following example projects using dReal as a
library:

 - [dreal-cmake-example-project](https://github.com/dreal/dreal-cmake-example-project)
 - [dreal-bazel-example-project](https://github.com/dreal/dreal-bazel-example-project)

If you want to use
[pkg-config](https://www.freedesktop.org/wiki/Software/pkg-config),
you need to set up `PKG_CONFIG_PATH` as follows:

macOS 10.13 / 10.12 / 10.11:

```bash
export PKG_CONFIG_PATH=/usr/local/opt/ibex@2.6.5/share/pkgconfig:${PKG_CONFIG_PATH}
```

Ubuntu 16.04 / 14.04:

```bash
export PKG_CONFIG_PATH=/opt/dreal/4.18.03.3/lib/pkgconfig:/opt/libibex/2.6.5/share/pkgconfig:${PKG_CONFIG_PATH}
```

Then, `pkg-config dreal --cflags` and `pkg-config dreal --libs` should
provide necessary information to use dReal. Note that setting up
`PKG_CONFIG_PATH` is necessary to avoid possible conflicts (i.e. with
`ibex` formula in Mac).


Python Binding
==============

Some of the functionality of dReal is accessible through Python2. On
Ubuntu 16.04 / 14.04, you need to set up the `PYTHONPATH` environment
variable. On macOS, you do not need this step.

```bash
# Only on Ubuntu 16.04 / 14.04:
export PYTHONPATH=/opt/dreal/4.18.03.3/lib/python2.7/site-packages:${PYTHONPATH}
```

To test it, run `python2` in a terminal and type the followings:

```python
from dreal.symbolic import Variable, logical_and, sin, cos
from dreal.api import CheckSatisfiability, Minimize

x = Variable("x")
y = Variable("y")
z = Variable("z")

f_sat = logical_and(0 <= x, x <= 10,
					0 <= y, y <= 10,
					0 <= z, z <= 10,
					sin(x) + cos(y) == z)

result = CheckSatisfiability(f_sat, 0.001)
print(result)
```

The last `print` statement should give:

```
x : [1.247234518484574339, 1.247580203674002686]
y : [8.929064928123818135, 8.929756298502674383]
z : [0.06815055407334302817, 0.06858905276351445757]
```

Python3 support is experimental for now. Please read [this
comment](https://github.com/dreal/dreal4/issues/69#issuecomment-377085510)
for instructions.
