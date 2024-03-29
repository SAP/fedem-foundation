# pFUnit

pFUnit is a unit testing framework enabling JUnit-like testing of
serial and MPI-parallel software written in Fortran. Initial support
for OPENMP has been implemented. pFUnit makes use of modern Fortran
programming techniques, including object oriented programming,
offering a convenient, lightweight mechanism for Fortran developers to
create and run software tests that specify the desired behavior for a
given piece of code. The framework was originally created by
developers from NASA and NGC TASC.

## Installation and basic usage guide

### Table of contents

1. [Prerequisites](#prerequisites)
2. [Obtaining pFUnit](#obtaining-pfunit)
3. [What's in the directory?](#whats-in-the-directory)
4. [Configuration](#configuration)
5. [Building pFUnit](#building-pfunit)
6. [Installation](#installation)
7. [Usage](#usage)
8. [Development](#development)
9. [Feedback and support](#feedback-and-support)
10. [Acknowledgments](#acknowledgments)
11. [Known installations/versions](#known-installationsversions)
12. [Notes](#notes)
13. [TODO](#todo)

## Prerequisites

The development work for pFUnit has mostly been carried out on a
mixture of systems, including high-end computers, Apple Mac OSX, and
linux-based systems. A preliminary Windows/CYGWIN port has been
contributed by a user. Full use of the system depends on the following
being available.

- Fortran 2003+
  - Tested with:
    - Intel 14+,
    - NAG 6.0,
    - GFortran 4.8.3, 4.9.+, 5.0+
    - IBM's XLF
    - PGI 15.7
- The Message Passing Interface (MPI)
- OpenMP
- GNU Make
- Python 2.7+

A CMake build process is also available.

Doxygen is used to generate documentation (see http://www.doxygen.org).

The system is routinely undergoes regression testing, including with GNU,
Intel, and NAG fortran compilers and OpenMPI.

pFUnit makes extensive use of leading edge Fortran language features,
which are generally best supported with by the latest compiler
versions. The capacity to support older compilers is limited.

## Obtaining pFUnit

The best way to obtain pFUnit is to clone the git repository from
GitHub as follows:

    git clone https://github.com/Goddard-Fortran-Ecosystem/pFUnit.git

This will create the directory pFUnit in the current working
directory.

You can download the latest version as a tarball from the [release
page](https://github.com/Goddard-Fortran-Ecosystem/pFUnit/releases/latest),
and extract it like:

    $ tar zxf ./pFUnit-*.tar.gz

which will place the pFUnit files into a directory called
`pFUnit-<version>`, where `<version>` is the version number.

For other ways to acquire the code visit

    https://github.com/Goddard-Fortran-Ecosystem/pFUnit/

or contact the pFUnit team.

## What's in the directory?

In the top level of the pFUnit distribution you will see the following
files.

- `CMakeLists.txt` - Initial support for cmake-based builds.

- `COPYRIGHT` - Contains information pertaining to the use and
  distribution of pFUnit.

- `Examples` - Contains examples of how to use pFUnit once it is
  installed.

- `GNUmakefile` - The top level makefile for building and installing
  pFUnit.

- `bin` - Executables used to construct and perform unit tests.

- `documentation` - Provides information about the pFUnit.

- `include` - Files to be included into makefiles or source, including use
  code.

- `LICENSE` - The NASA Open Source Agreement for GSC-15,137-1 F-UNIT,
  also known as pFUnit.

- `README.md` - This file.

- `source` - Source code and scripts of the pFUnit library and framework.

- `tests` - Source code for unit testing pFUnit itself.

- `tools` - Tools used to help develop, build, and install pFUnit.

- `VERSION` - Contains a string describing the current version of the framework.

## Configuration

Little needs to be done to configure pFUnit for the build, however
there are several environment variables on which the package depends.

- `F90_VENDOR` - is set to include the correct makefile in
  `$(TOP_DIR)/include`, i.e. GNU, Intel, NAG, or PGI. Case insensitive
  file systems may cause some confusion from time-to-time.

- `F90` - is set to the Fortran compiler being used: e.g. ifort for
  Intel, gfortran for GNU.

- `COMPILER` - is set according to F90_VENDOR and is automatically set in
  the top level makefile.

For MPI-based unit testing, your setup may require the following as well.

    MPIF90
    $ # e.g.
    $ export MPIF90=mpif90

As a convenience for working with multiple MPI configurations, you may
also set the following.

    MPIRUN
    $ # e.g.
    $ export MPIRUN=/some.path/mpirun

- `pFUnit_MAX_ARRAY_RANK` - controls the maximum number of (Fortran)
  dimensions of the arrays asserts are defined over. If
  `pFUnit_MAX_ARRAY_RANK` is not set, the default is 5 and pFUnit's
  assertions will be able to handle arrays up to rank 5,
  i.e. `A(:,:,:,:,:)`. `pFUnit_MAX_ARRAY_RANK` and `MAX_RANK` do not
  refer to MPI ranks (process id within a group). Example:

        $ export pFUnit_MAX_ARRAY_RANK=8

  `pFUnit_MAX_RANK` is a deprecated way to set maximum rank and is to
  be removed in version 4.

- `DOXYGEN` - To generate documentation, set DOXYGEN to the desired
  executable. NOTE: Doxygen Version 1.8.5 does not respect CamelCase
  names from Fortran source code by currently converting all to
  lowercase. It does this to get HTML links correct for references in
  the source code that also do not respect the CamelCase convention.
  The Fortran standard specifies case insensitivity. Doxygen 1.7.x
  seems to better respect CamelCase.

        $ #.e.g.
        $ export DOXYGEN=/opt/local/share/doxygen/doxygen-1.7.6/bin/doxygen

## Building pFUnit

### Testing serial codes (Non-MPI)

1. Change to the directory into which pFUnit has been placed.
2. Set these environment variables (bash example):

         $ export F90=gfortran-mp-4.8
         $ export F90_VENDOR=GNU

3. To build pFUnit for unit testing of serial codes, execute make.
   The unit tests for pFUnit itself will run automatically.

         $ make tests
   1. Occasionally on the first run through, one will get a spurious
      (runtime) error, for example in the unix process component.
      Re-execute "make tests" to check again.
4. At this point the pFUnit object library is in the source directory,
   along with a large number of Fortran module files.

### Testing parallel codes (MPI)

To build pFUnit for unit testing MPI-based codes, be sure that the
environment is properly set up for the MPI implementation you are
using. Depending on your local environment, you may need execute the
build within a batch or other job queing system, e.g. an interactive
batch job under PBS. The steps for building pFUnit start out the same
as for the serial case above, but add `MPI=YES` to the environment to
switch on MPI support. The MPI-based unit tests for pFUnit itself
will run automatically. Again, occasionally a spurious (runtime)
error may appear on the first execution.

3\. Execute make as follows.

    $ make tests MPI=YES 

4\. At this point an MPI-enabled pFUnit object library is in the source
directory, along with a large number of Fortran module files.

Also, one may get some harmless "no symbols" warnings when the pFUnit
library is constructed.

### Testing parallel codes (OpenMP)

Initial (limited) support for OPENMP has been implemented. At this
writing, a basic functionality is available.

The process for building pFUnit for testing OPENMP-based codes is
similar to that for other paradigms.

3\. To compile for OPENMP support execute make as follows.

    $ make tests OPENMP=YES 

4\. At this point the OPENMP-enabled pFUnit is ready to be installed.

### Cleaning

To clean the pFUnit build directory for the space or to rebuild there
are two options.

1. Make clean to remove object files and other intermediate products.

        $ make clean

2. Make distclean to remove libraries and other more final products.

        $ make distclean

3. Some directories support a `make src_clean` to remove intermediate
   products in subdirectories.

### Building the documentation

A start at documentation for pFUnit is in the documentation directory.
Doxygen is our primary documentation tool. To make the documentation,
which will be generated in the documentation directory, please invoke
the following from the top level of the pFUnit distribution.

    $ make documentation

Or to make a reference manual:

    $ make documentation/pFUnit2-ReferenceManual.pdf

To select a specific version of Doxygen, please set the DOXYGEN
environment variable as in the Configuration section above. You 
may wish to do this if your code uses CamelCase names as current
versions of Doxygen (1.8.5) do not respect this convention for
Fortran.

### Building pFUnit using CMake

Initial support for CMake has been implemented. At this
writing, a basic functionality is available.

1. The process for building pFUnit using cmake is as follows. In the
   top directory of the distribution make a new directory to support the
   build, then change to that directory and run cmake (pointing back to
   the source) to generate the required makefiles.

        $ mkdir build
        $ cd build
        $ # e.g. cmake -DMPI=YES -DOPENMP=NO <path to source>
        $ cmake -DMPI=NO ..
        $ make tests

   Don't forget you can use the standard `-DCMAKE_INSTALL_PREFIX` to
   define where the resulting tool will be installed.

   If your MPI installation does not provide mpirun, you may try to set
   `-DMPI_USE_MPIEXEC=YES` to tell CMake to use its `FindMPI` function to
   find out how to execute the tests.

4. If the build is successful, then at this point `make install` should work.

## INSTALLATION

Installations 6.1-6.5 are based on GNU make and the project
makefiles. If you use CMake then `make install` will install to the
expected place. That is, to `/usr/local` if you specify nothing,
otherwise to wherever `CMAKE_INSTALL_PREFIX` points.

## Serial

To install pFUnit for regular use, set `INSTALL_DIR` to the location
in which to place pFUnit. This can be done on the make command line.
For example, after compiling pFUnit for serial use (MPI absent or
`MPI=NO`), please try.

    $ # In the top of the pFUnit build directory.
    $ make install INSTALL_DIR=/opt/pfunit/pfunit-serial

Note: you may need special privileges to install in some locations,
e.g. via sudo.

To test the installation set pFUnit to `INSTALL_DIR`, then change the
working directory to Examples in pFUnit distribution and execute
`make` which will run a number of examples. These include some
expected (intentional) failures.

    $ # In the top pFUnit build directory...
    $ export pFUnit=/opt/pfunit/pfunit-serial
    $ cd Examples
    $ make

### MPI

For installing an MPI-enabled pFUnit library, change to the top of the
distribution and execute make with `MPI=YES`. You may need to `make
distclean` first. After compilation and pFUnit passes its self-tests,
then installation proceeds as for the serial case above.

    $ make install INSTALL_DIR=/opt/pfunit/pfunit-parallel

To test, set pFUnit and go into Examples/MPI_Halo directory.

    $ # In the top pFUnit build directory...
    $ export pFUnit=/opt/pfunit/pfunit-parallel
    $ # The variable MPIF90 must be set to the appropriate build script.
    $ export MPIF90=mpif90
    $ cd Examples/MPI_Halo/
    $ make

This will compile and run a set of parallel examples that includes
intentional failures. To run all of the examples try executing
`make MPI=YES` in the Examples directory.

### OpenMP

At this time the OPENMP version of pFUnit can be installed in the same
way as for the serial or MPI-parallel codes. OpenMP support, tests,
and examples are limited as of this writing.

## Default directory

If `INSTALL_DIR` is not set, `make install` will attempt to install
pFUnit into the top build directory. This will create directories
such as lib and mod in the top level of the build directory and will
overwrite the include/base.mk with include/base-install.mk. If this
is not desired, then "make develop" will put back the original
base.mk, which is the file to be used for development and building
pFUnit. In general, we recommend installing to a directory that is
not also the build directory.

## Usage
### Configuration

For regular use, after installation, the same compiler/MPI development
configuration that was used to build pFUnit should be used. Once the
environment variables and paths associated with the environment are
set, to configure pFUnit, please set the following.

- `pFUnit` - set to the directory into which pFUnit was installed.
- `F90_VENDOR` - set to Intel, GNU, NAG, or PGI accordingly.

### Preprocessor - Hello World

An example of how to use the preprocessor can be found in
Examples/Simple. The GNU makefile shows how to construct an F90 file
from a preprocessor input file. For example, the GNU make rule can be:

```make
# GNU makefile rule
%.F90: %.pf
    $(pFUnit)/bin/pFUnitParser.py $< $@
```

The file testSuites.inc is included in the include/driver.F90 file
during the build process. To include tests, one must add the test
suite module to testSuites.inc, as follows.

    ! Add a test suite to the build.
    ADD_TESTS_SUITE(helloWorld_suite)

A preprocessor input file contains tests and is a 
Fortran free-format file with directives, for example:

```fortran
! helloWorld.pf - with a successful test...
@test
subroutine testHelloWorld()
   use pfunit_mod
   implicit none
   @assertEqual("Hello World!","Hello World!")
end subroutine testHelloWorld
```

### Compiling and Executing the Tests (SERIAL)

An example of a GNU make rule for for the final step of compiling a test follows.

```make
# This step presumes "include $(pFUnit)/include/base.mk" earlier in the makefile.
tests.x: testSuites.inc myTests.pf
         $(F90) -o $@ -I$(pFUnit)/mod -I$(pFUnit)/include \
                $(pFUnit)/include/driver.F90 \
                ./*$(OBJ_EXT) $(LIBS) $(FFLAGS)
```

To execute the tests, one invokes `./tests.x` with the appropriate command line options (see below).

In some cases, since include/driver.F90 is `implicit none` it may be
necessary to insert a `use` clause to identify external suite-wide
fixture code to the compiler. As a convenience, the CPP macro
`pFUnit_EXTRA_USAGE` can be set to a module of fixture code via a
compiler command line argument turning on a `use pFUnit_EXTRA_USAGE`
line at the beginning of include/driver.F90.

### Compiling and Executing the Tests (MPI PARALLEL)

One invokes MPI-based parallel tests according to the MPI framework
being used. For example:

    $ mpirun -np 4 tests.x


### Command Line Options

The executable test program provides several command line options,
when "include/driver.F90" is used, as it is automatically when using
the pFUnit preprocessor.

    -v or -verbose                  Verbose execution.
    -d or -debug                    Provide debugging information.
    -h                              Print help message.
    -o <outputfile>                 Direct pFUnit messages to a file.
    -robust                         Use the robust runner. Runs tests as processes so failures do not halt testing.
    -max-timeout-duration <duration> Limit detection time for robust runner.
    -max-launch-duration  <duration> Limit detection time for robust runner.
    -skip <number of tests to skip> Use the subset runner, which runs a subset of the tests in a suite.

An example from Examples/Robust:

    $ ./tests.x -robust

## Development

Generally pFUnit development is performed in the build directory
structure. Care should be taken to make clean or distclean in between
configuration changes. As stated above, it is best to set `INSTALL_DIR`
and `make install` pFUnit to another directory that can be placed in a
user's paths. 

## Feedback and bugs

### Support

### Tips

1. Environment Modules - Though not strictly required, the Environment
   Modules package can be a convenient way to package, maintain, and
   switch between environments. This can be particularly important for
   pFUnit, which must be built using the same tool suite being used for
   development, e.g. compilers, linkers, etc. [To do:  A sample pFUnit
   modulefile is provided in the OTHER directory.] Environment Modules

2. Compile time errors like `"include [...]include/.mk" not found`
   likely signify that you not executing make in the top level
   directory during a build. Alternatively, during regular usage after
   installation, pFUnit has not been set.

   During building, if you wish to compile in a subdirectory of within the
   pFUnit heriarchy, please try setting the COMPILER environment variable
   on the make command line. For example:

        $ make all COMPILER=Intel

3. If you wish to see the intermediate files, use the target .PRECIOUS
   in the makefile to keep them from being deleted. For example:

```make
# In GNUmakefile
.PRECIOUS: %_cpp.F90
```

### Platform specific notes

#### Mac OSX

The MacPorts package management system is a convenient way to install
and maintain many packages, including gcc which includes gfortran.

#### Windows/CYGWIN

User contributed code for Windows/CYGWIN has been added, but is
currently not tested and supported by the pFUnit team. At this
writing, 2013-1031, serial Examples and MPI are not known to be
supported. Please contact us if you wish to either contribute or
otherwise discuss this port.

#### Intel Fortran Version 13: `-DINTEL_13`

Using version 13 is deprecated. We have encountered problems using
version 13, which we believe may be due to subtle compiler bugs. We
strongly recommend upgrading to the latest version possible.

To make pFUnit work with Intel Fortran Version 13, please ensure that
`-DINTEL_13` is passed to the compiler when building or using
pFUnit. In the build process for pFUnit, this is added to the make
variables CPPFLAGS and FPPFLAGS. 

## Acknowledgments

Thanks to the follwing for their review and comments: B. Van Aartsen, T. Clune.

- Windows/CYGWIN contributions from E. Lezar.

- PGI port contributions from M. Leair (PG Group).

- Other acknowledgments: S.P. Santos (NCAR), M. Hambley (UK Met Office),
  J. Krishna (ANL), J. Ebo David.

## Known installations/versions

(git cognizant from "sourceforge.net/projects/pfunit")

- `master` - The current release.
- `development` - The cutting edge of pFUnit development.
- `mock_services` - Experimental support for mocking.
- `pfunit_2.1.0` - A feature freeze prior to a major upgrade of the preprocessor.
- `cray` - An intermediate port to Cray CCE.

## Notes

* For modifications and feature requests please see https://github.com/Goddard-Fortran-Ecosystem/pFUnit

## TODO

- Make other directory.
- Make Environment Modules example in other directory.
- Other build systems, e.g. CMake.

## REVISIONS TO THIS DOCUMENT

- 2015-1210 Minor changes to documentation. MLR
- 2015-0608 Added note about PFUNIT_EXTRA_USAGE (from MH). MLR
- 2015-0508 Some PGI workarounds removed for PGI 15.4. MLR
- 2015-0420 Clarified PFUNIT_MAX_ARRAY_RANK note. MLR
- 2015-0320 PGI port workarounds, including examples. 3.1. MLR
- 2014-1211 Minor updates for 3.0.2. MLR
- 2014-1110, 2014-1031 Minor edits. MLR
- 2014-0915 Minor updates for 3.0.1. MLR
- 2014-0404 Updated for release of 3.0. TLC
- 2014-0131, 2014-0205. Updated. MLR
- 2013-1107. Minor edits. MLR
- 2013-1031. Added user contributed code for Windows/CYGWIN & IBM's XLF. MLR
- 2013-0830-1359. Minor corrections and added MPIF90 to 6.2. MLR
- 2013-0806-1345. Corrected git reference. Was using old URL. MLR
- 2013-0805. Initial draft. MLR
