# <img src="https://raw.githubusercontent.com/LLNL/RAJA/develop/share/raja/logo/RAJA_LOGO_Color.png" width="64" valign="middle" alt="RAJA"/> RAJA Development Environment

The **RAJA Development Environment** effort leverages
[**Spack**](https://github.com/spack/spack) for the creation and support
of [**RAJA**](https://github.com/LLNL/RAJA) development through Spack's
developer workflow features.

Background
---------------------

A Spack developer workflow environment is an independent (i.e., not managed
by Spack) environment specifying potentially constrained packages that the
team actively develops. The environment can be used to create and maintain
a build cache -- source and or binary -- of the root and dependency packages
used by the team.

More information on these concepts and capabilities can be found in the
`Additional Information` section below.

Getting Started
---------------------

You will need to set up an active Spack instance. If you haven't done
that yet, enter the following on the command line from wherever you want
your Spack instance to reside:

    $ git clone https://github.com/spack/spack.git
    $ . spack/share/spack/setup-env.sh

The root directory of the Spack clone will be referred to here as
`$SPACK_ROOT`.

### Activating the Development Environment

Once you have set up your Spack instance, you can activate the RAJA
development environment. Assuming `$DEV_ENV` contains the path to the
development environment provided by this repository, currently in
`./experimental/dev-env`, you can activate the environment with:

    $ cd $DEV_ENV
    $ spacktivate .

Now that the environment is active, you can install the software from
source or, if available, binary cache (as described below) and begin
development.

Before discussing the installation process, let's cover creation and
use of a local build cache.

### Creating a Local Build Cache

Assuming the project doesn't already have one set up and the goal
is to share one on the local file system, you can create one. First
you'll need to:

* activate the development environment; and
* install the software

using the processes described here in other sections.

Now you can create a local build cache on the file system by:

* create a GPG signing key with user id `$USER_ID` and email `$EMAIL`;
* creating a Spack mirror in the shared directory (`$MIRROR_DIR`);
* create the source build cache; and
* create the binary build cache.

The for creating the GPG signing key are:

    $ spack gpg create $USER_ID $EMAIL
    $ mkdir $HOME/private_gpg_backup
    $ cp $SPACK_ROOT/opt/spack/gpg/*.gpg $HOME/private_gpg_backup
    $ cp $SPACK_ROOT/opt/spack/gpg/pubring.* $MIRROR_DIR
    $ chgrp $GROUP $MIRROR_DIR/pubring.*

Note `$MIRROR_DIR` is assumed to belong to and be accessible by all
members of the group `$GROUP`.

Now create the Spack mirror for all installed packages from the active
development environment. You can add the `-D` option to the `spack`
command if you also want dependencies of the development environment
to be cached.

    $ spack mirror create -d $MIRROR_DIR --all
    $ chmod -R g+rws $MIRROR_DIR

This creates a source cache mirror, which can form the basis for the
binary cache mirror.

Now you can create the binary build cache leveraging the results from
our source cache. You can add the `--only=package` option to
`spack buildcache` if you want to exclude dependencies from the build
cache.

    $ spack mirror add raja-dev-env $MIRROR_DIR
    $ spack buildcache keys --install --trust
    $ mkdir -p $MIRROR_DIR/build_cache
    $ spack buildcache create --allow-root --force -d $MIRROR_DIR --all
    $ chmod -R g+rws $MIRROR_DIR/build_cache

You can add/remove packages from the environment and re-create it
with the command above to modify the build cache.

### Using the Local Build Cache

The team will need to run the following command to use the local
build cache:

    $ spack mirror add raja-dev-env $MIRROR_DIR
    $ spack buildcache keys --install --trust --force

### Installing the Software

Once you have activated the development environment, you can
install the software in your local Spack instance.

If your project has established a mirror and cache for the
environment, you'll want to follow the steps for using it
before running the following command:

    $ spack install

to install the software.

### Developing Software in the Environment

With the environment activate, you can begin development on one
or more packages within the environment. You'll need to tell Spack to:

* check out a **specific version** of each package for development;
* re-concretize the development environment; and
* rebuild the affected software.

Suppose you only want to work on one package, `$PACKAGE`, for `$VERSION`.
You would enter the following on the command line:

    $ spack develop $PACKAGE@$VERSION
    $ spack concretize -f
    $ spack install

If you want to develop on multiple packages at the same time you will
can call `spack develop` for each package before re-concretizing
the environment.

You can now make changes to the software, which will be under your
environment directory. 

When you're ready to rebuild the modified software, you simply need
to re-install it:

    $ spack install

### Sharing Common Development Packages

You can give the `spack.yaml` file you are working on to someone else
on the team so they can work on the same packages. They can:

* create an independent development environment directory **or** use
  the clone's;
* change to that directory;
* copy the shared `spack.yaml` into the directory;
* activate the development environment (i.e., `spacktivate .`);
* follow instructions for using the project's local cache, if appropriate; and
* run `spack develop`.

Their development environment directory will now contain subdirectories
with the sources for the packages being developed.

Additional Information
-------------------------

Spack provides documentation and tutorials on the capabilities described
here. For more information:

* [**Build Caches Documentation**](https://spack.readthedocs.io/en/latest/binary_caches.html)
* [**Developer Workflows Tutorial**](https://spack-tutorial.readthedocs.io/en/latest/tutorial_developer_workflows.html)
* [**Environments Documentation**](https://spack.readthedocs.io/en/latest/environments.html)
* [**Environments Tutorial**](https://spack-tutorial.readthedocs.io/en/latest/tutorial_environments.html)
* [**Mirrors Documentation**](https://spack.readthedocs.io/en/latest/mirrors.html)
* [**Mirror Tutorial**](https://spack-tutorial.readthedocs.io/en/latest/tutorial_binary_cache.html)
* [**Package Repositories Documentation**](https://spack.readthedocs.io/en/latest/repositories.html)


Licenses
---------------------

### RAJA

RAJA is an Unlimited Open Source - BSD 3-clause Distribution
`LLNL-CODE-689114`  `OCEC-16-063`

License details for RAJA are provided at:
https://github.com/LLNL/RAJA/blob/develop/README.md#license

### Spack

Spack is distributed under the terms of both the MIT license and the
Apache License (Version 2.0).
`LLNL-CODE-811652`

License details for Spack are provided at:
https://github.com/spack/spack#license.
