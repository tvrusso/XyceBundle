# A project repo for building the Xyce&trade; Parallel Electronic Simulator

The purpose of this repo is to simplify the process of building Xyce
from source.  It provides Trilinos, Xyce, and the Xyce regression
suite as submodules, and contains some scripts that implement the
process described in the Xyce&trade; building guide at
https://xyce.sandia.gov/documentation/BuildingGuide.html.

This is not ready to go out of the box for every operating system.  I am
trying to collect up system-specific configurations as much as possible, but
provide the means for you to add your own.  Sample configurations are
provided with instructions on how to customize them.

This is not a replacement for the building guide, but should provide a
framework for following the directions in that guide more easily.

At the moment, I'm providing only example serial builds, but this will
change eventually as I iron out the wrinkles in the process.

## What this repo will NOT do for you

This repo just takes care of building Xyce and the Trilinos library it
requires.  It does NOT install any of the prerequisite packages that
are needed to build these tools, all of which may be obtained from the
system package management repositories on all of the systems we have
tried to use.

Please see the
[Xyce Building Guide](https://xyce.sandia.gov/documentation/BuildingGuide.html)
for system-specific guidance on what packages to install before trying to run
anything in this repo.

## Using this repo

  * Clone the repo from GitHub using `git clone --recurse-submodules` and
    change to its directory.  Note that Trilinos is enormous and cloning this
    submodule will take time and disk space.


  * If you forgot to use the `--recurse-submodules` when you cloned
    this repo, get the submodules now with `git submodule update
    --init --recursive`.

  * If I haven't provided a system-specific configuration for your system,
    you'll have to customize the samples I have provided.

  * Run the "Build_And_Test_Xyce" script, which should build Trilinos and Xyce
    for your system and test out the build with the Xyce regression suite.

## Customizing
If your system is not one of the few that I have and have been able to add,
you can add it yourself by copying the sample files I've provided.

### Creating a Trilinos script for your system

 * Find out how your system identifies itself by typing `uname`.

 * Copy the file `trilinos_reconfigures/reconfigure.trilinos.sample`
   to `trilinos_reconfigures/reconfigure.trilinos.UNAME_serial`, replacing
   "UNAME" with the exact string that `uname` emitted above.

 * Customize the contents of your new file, and remove the set of "echo"
   commands at the top and the "exit 1" line that follows them.

 * Important things to customize:
   - FLAGS
   - CMAKE_C_COMPILER
   - CMAKE_CXX_COMPILER
   - CMAKE_Fortran_COMPILER
   - AMD_LIBRARY_DIRS
   - TPL_AMD_INCLUDE_DIRS

 * You may need to add additional options for BLAS and LAPACK if you are
   using a compiler or library suite that provides these in an unusual way.
   The examples here are correct for typical open source builds with standard
   free compilers.

### Creating a Xyce script for your system

 * Find out how your system identifies itself by typing `uname`.

 * Copy the file `xyce_reconfigures/reconfigure.xyce.sample`
   to `xyce_reconfigures/reconfigure.xyce.UNAME_serial`, replacing
   "UNAME" with the exact string that `uname` emitted above.

 * Customize the contents of your new file, and remove the set of "echo"
   commands at the top and the "exit 1" line that follows them.

 * Important things to customize (ideally, these should match what you have
   in your Trilinos script):
   - CXXFLAGS
   - CC
   - CXX
   - F77

 * Some systems may require additional CPPFLAGS, LDFLAGS, or LIBS variables
   to find headers and specialized libraries.  Consult the building guide.

 * The provided scripts set an install prefix to a directory name under
   the XyceBundle top directory.  You may wish to change this default.

## Building Trilinos alone

Once a correct "reconfigure.trilinos." file is available, you can
build Trilinos and install it by running the
"Build_And_Install_Trilinos" script in the top level directory of this
repo.  I recommend you do so simply by executing
"./Build_And_Install_Trilinos" from the top level.

   - This script will create a build directory for Trilinos, run the
     appropriate reconfigure script, build Trilinos, and install it into
     a directory XyceLibs/UNAME_serial under the top XyceBundle directory.

   - This script is set up to use 8 processors by default, because that's
     what my main personal development machine has.  If you wish to run it
     on a computer with fewer (or more) cores, you may either edit the script
     and change "NUMPROC=8" to something else, or may invoke it with a
     numeric argument to use a different number.

   - The script is set up correctly to work with no arguments (or just one
     argument with a processor count) if it is invoked in the top level
     XyceBundle directory.

     You may execute it elsewhere if you wish, but must then provide it
     a full path to the top level of the XyceBundle clone as the second
     command line argument, in which case you must also specify the processor
     count as the first argument.

## Building Xyce

If a correct reconfigure exists for both Xyce and Trilinos, you can
build everything in one fell swoop by running the script
"Build_And_Test_Xyce".  I recommend running this from the top level of the
XyceBundle directory as "./Build_And_Test_Xyce".

This script checks to see if there is already a usable Trilinos
install for your system in the XyceLibs directory.  If there is not,
it runs Build_And_Install_Trilinos.  It then creates a build directory
in which it configures and builds Xyce, and if that is successful it
runs the test suite.

The reconfigures I provide will install Xyce into a subdirectory of
the XyceBundle tree called Xyce_Installs/UNAME_Serial.  The
Build_And_Test_Xyce script does not do the install.  If you wish to
install it, run `make install` from the build directory, which will be
called Xyce_Builddir_UNAME_serial.  (Again, "UNAME" will be replaced
by the value returned by running `uname` on your system.)

As with Buld_and_Test_Trilinos:

   - This script will create a build directory for Xyce, run the
     appropriate reconfigure script, build Xyce, and run the full
     Xyce regression suite.

   - This script is set up to use 8 processors by default, because that's
     what my main personal development machine has.  If you wish to run it
     on a computer with fewer (or more) cores, you may either edit the script
     and change "NUMPROC=8" to something else, or may invoke it with a
     numeric argument to use a different number.

   - The script is set up correctly to work with no arguments (or just one
     argument with a processor count) if it is invoked in the top level
     XyceBundle directory.

     You may execute it elsewhere if you wish, but must then provide it
     a full path to the top level of the XyceBundle clone as the second
     command line argument, in which case you must also specify the processor
     count as the first argument.


## Updating your bundle

Xyce and Xyce_Regression are generally updated on GitHub once a week,
and you can use this repo to stay current.

Simply run the command `git pull --recurse-submodules` in the top
level directory of your clone, and all submodules should be fetched
and updated.  Once they are, simply rerun `Build_And_Test_Xyce` and
the program will be rebuilt and tested.  If you have not deleted your
previous build directory, only those files that have changed will be
recompiled and the build should be faster than last time.  There are
likely more tests than there were last time, because that's how the
Xyce team rolls.

### Updating when git doesn't cooperate

We have found that some "long term support" operating systems (notably
RHEL7) have versions of git that will download the commits from the
submodules but will fail to update the checked out version to the
correct state.  We are not entirely sure whether this is because the
version of git is too old, but regardless it means that issuing the
`git pull --recurse-submodles` command does NOT put the XyceBundle
checkout into the correct state to build the latest Xyce.  Should you
find that your system is not updating the HEAD state of the submodules
even though it is downloading all the updates, you can correct the
matter with `git submodule update --force --checkout --recursive`.

### Updating if I've spaced out

While it is my intent to update the submodule information every time
the Xyce project pushes a new batch of code and tests to GitHub, I
might forget some week.  If that happens, `git pull
--recurse-submodules` will not update HEAD *and* neither will the
submodule update command above.  Should I forget some week and you
really want the lastest version anyway, you can force the issue
yourself.  Just do:

```
for i in Xyce Xyce_Regression
do
  pushd $i
  git merge origin/master
  popd
done
```

This will assure that you will get the latest Xyce and Xyce_Regression
even if I've neglected to push the submodule update myself.  (This is
precisely what I do to update the XyceBundle project, only after I've
done it I push the resulting changes of submodule state to the
XyceBundle repo.)

At this time, the Xyce team recommends a specific, somewhat old
version of Trilinos, and this repo is set up to check that specific
branch out and stay there.  While updating the submodules will pull down all
the new changes in Trilinos on its other branches, the version used by this
repo will stay at Trilinos 12.12.1 unless you change it.

The Xyce team does not guarantee that Xyce will build with any version
of Trilinos other than 12.12.1 at this time, buy you might find that
it does in some cases.  If you know what you're doing, you can test
this out by fiddling with the trilinos clone that is included here as
a submodule.  But if you break anything by doing so, you own both
pieces.
