<!-- Copyright (c) 2010-2019 K Team. All Rights Reserved. -->
[![Join the chat at https://gitter.im/kframework/k](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/kframework/k?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This is a readme file for the developers.

# Prerequisites

In short:

On Ubuntu:

```
git submodule update --init --recursive
sudo apt-get install build-essential m4 openjdk-8-jdk libgmp-dev libmpfr-dev pkg-config flex z3 libz3-dev maven opam python3 cmake gcc clang-6.0 clang++-6.0 llvm-6.0 llvm-6.0-tools zlib1g-dev bison libboost-test-dev libyaml-cpp-dev libjemalloc-dev
llvm-backend/src/main/native/llvm-backend/install-rust
curl -sSL https://get.haskellstack.org/ | sh
```

On Arch (from source):

```
git submodule update --init --recursive
sudo pacman -S git maven jdk-openjdk cmake boost yaml-cpp jemalloc clang llvm lld zlib gmp mpfr z3 opam curl stack rustup base-devel base python
export PATH=$PATH:/usr/bin/core_perl
llvm-backend/src/main/native/llvm-backend/install-rust
makepkg
sudo pacman -U kframework-5.0.0-1-x86_64.pkg.tar.xz
```

If you install this list of dependencies, continue directly to the Install section.

## Java Development Kit (required JDK8 version 8u45 or higher)

Linux:
*   Download from package manager (e.g. `sudo apt-get install openjdk-8-jdk`)

Mac/Windows:
*   Download the JDK [here](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

To make sure that everything works you should be able to call `java -version` and
`javac -version` from a Terminal.

## Apache Maven

Linux:
*   Download from package manager (e.g. `sudo apt-get install maven`)

Mac:
*   Download it from a package manager or from
    http://maven.apache.org/download.cgi and follow the instructions on the webpage.

Windows:
*   Go to http://maven.apache.org/download.cgi and download the zip with the 
    binary distribution. Unzip it in your desired location and follow the 
    installation instructions on the webpage.

Maven usually requires setting an environment variable `JAVA_HOME` pointing
to the installation directory of the JDK (not to be mistaken with JRE).

You can test if it works by calling `mvn -version` in a Terminal.
This will provide the information about the JDK Maven is using, in case
it is the wrong one.

## Rust 1.33 (and Cargo)

To install if you don't already have rustup installed, run:
```
llvm-backend/src/main/native/llvm-backend/install-rust
```

If you already have rustup installed, you can build rustc 1.33 from source (https://static.rust-lang.org/dist/rustc-1.33.0-src.tar.gz) and install it via:

```
./configure --llvm-root=/usr/lib/llvm-6.0 --enable-llvm-link-shared
./x.py build
rustup toolchain link rust-1.33.0-llvm-6.0 build/x86_64-*/stage2
```

We do not currently support any of the default rustup toolchains. Note that you will need at least cargo 0.32.0 (which comes with rust 1.31.0) as your default toolchain to build our rust code.

## Haskell Stack

To install, go to <https://docs.haskellstack.org/en/stable/README/> and follow the instructions.
You may need to do `stack upgrade` to ensure the latest version of Haskell Stack.

## Miscellaneous

Also required:

*   [gcc](https://gcc.gnu.org/)
*   [flex](https://github.com/westes/flex)
*   [make](https://www.gnu.org/software/make/)
*   [gmp](https://gmplib.org/)
*   [mpfr](http://www.mpfr.org/)
*   [pkg-config](https://www.freedesktop.org/wiki/Software/pkg-config/)
*   [z3](https://github.com/Z3Prover/z3)
*   [opam](https://opam.ocaml.org/doc/Install.html)
*   [python](https://www.python.org)

These can all be installed from your package manager.

# Install
Checkout the directory of this README in your desired location and call `mvn package` from the main
directory to build the distribution. For convenient usage, you can update
your $PATH with <checkout-dir>k-distribution/target/release/k/bin (strongly recommended, but optional).

You are also encouraged to set the environment variable `MAVEN_OPTS` to `-XX:+TieredCompilation`,
which will significantly speed up the incremental build process.

After running `mvn package` for the first time, you should run ``k-distribution/target/release/k/bin/k-configure-opam; eval `opam config env` ``. This performs first-time setup of the OCAML backend.

## Installing on fresh Windows Subsystem for Linux

1. Install the Ubuntu package from the Windows Store, which as of now is an alias for the Ubuntu LTS 18.04 package. During installation you will be asked to create a new user. 
2. Download the latest K distribution for Ubuntu Bionic from https://github.com/kframework/k/releases 
    to a temporary directory, for example `d:\temp` 

3. Open linux bash. For example by running:
```
ubuntu1804
```

4. Run the following commands:

    `$ sudo apt-get update`
    
    `$ cd <download dir>`. In our example download dir is `/mnt/d/temp`
    
    `$ sudo apt-get install ./kframework_5.0.0_amd64_bionic.deb`
        This will install ~1.4GB of dependencies and will take some time.
        K will be installed to `/usr/lib/kframework`

5. Copy the tutorial to some work directory, for example `/mnt/d/k-tutorial`. Otherwise, you won't be able to run the
examples from default installation dir if you are not `root`:

```    
$ cp -R /usr/lib/kframework/tutorial /mnt/d/k-tutorial
```

6. Now you can try to run some programs:

```bash
$ cd /mnt/d/k-tutorial/2_languages/1_simple/1_untyped
$ make kompile
$ krun tests/diverse/factorial.simple
```

# IDE Setup

## General

You should run K from the k-distribution project, because it is the only project to have the complete
classpath and therefore all backends.

## Eclipse
_N.B. the Eclipse internal compiler may generate false compilation errors (there are bugs in its support of Scala mixed compilation). We recommend using IntelliJ IDEA if at all possible._

To autogenerate an Eclipse project for K, run `mvn install -DskipKTest; mvn eclipse:eclipse` on the
command line, and then go into each of the `kore` and `tiny` directories and run `sbt eclipse`.
Then start eclipse and go to File->Import->General->Existing projects into workspace, and select
the directory of the installation. You should only add the leaves to the workspace, because
eclipse does not support hierarchical projects.

## IntelliJ IDEA

IntelliJ IDEA comes with built-in maven integration. For more information, refer to
the [IntelliJ IDEA wiki](http://wiki.jetbrains.net/intellij/Creating_and_importing_Maven_projects)

# Run test suite
To completely test the current version of the K framework, run `mvn verify`.
This normally takes roughly 30 minutes on a fast machine. If you are interested only
in running the unit tests and checkstyle goals, run `mvn verify -DskipKTest` to
skip the lengthy `ktest` execution.

# Changing the KORE data structures
If you need to change the KORE data structures (unless you are a K core developer, you probably do not), see [Guide-for-changing-the-KORE-data-structures](https://github.com/kframework/k/wiki/Guide-for-changing-the-KORE-data-structures).

# Build the final release directory/archives
Call `mvn install` in the base directory. This will attach an artifact to the local
maven repository containing a zip and tar.gz of the distribution.

The functionality to create a tagged release is currently incomplete.

# Compiling definitions and running programs
Assuming k-distribution/target/release/k/bin is in your path, you can compile definitions using
the `kompile` command.  To execute a program you can use `krun`.

For running either program in the debugger, use the main class `org.kframework.main.Main` with an additional argument `-kompile` or `-krun` added before other command line arguments, and use the classpath from the `k-distribution` module.

# Troubleshooting
Common error messages:

-  `Error: JAVA_HOME not found in your environment.
    Please set the JAVA_HOME variable in your environment to match the
    location of your Java installation.`
    + Make sure `JAVA_HOME` points to the JDK and not the JRE directory.

- `[WARNING] Cannot get the branch information from the git repository:
   Detecting the current branch failed: 'git' is not recognized as an internal or external command,
   operable program or batch file.`
   +  `git` might not be installed on your system. Make sure that you can execute
      `git` from the command line.

- `1) Error injecting constructor, java.lang.Error: Unresolved compilation problems:
        The import org.kframework.parser.outer.Outer cannot be resolved
        Outer cannot be resolved`
   + You may run into this issue if target/generated-sources/javacc is not added to the
     build path of your IDE. Generally this is solved by regenerating your project /
     re-syncing it with the pom.xml.

- `[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.1:compile
   (default-compile) on project k-core: Fatal error compiling: invalid target release: 1.8 -> [Help 1]`
   + You either do not have Java 8 installed, or `$JAVA_HOME` does not point to a Java 8 JDK.

If something unexpected happens and the project fails to build, try `mvn clean` and
rebuild the entire project. Generally speaking, however, the project should build incrementally
without needing to be cleaned first.

If you are doing work with snapshot dependencies, you can update them to the latest version by
running maven with the `-U` flag.

If you are configuring artifacts in a repository and need to purge the local repository's cache
of artifacts, you can run `mvn dependency:purge-local-repository`.

If tests fail but you want to run the build anyway to see what happens, you can use `mvn package -DskipTests`.

If you still cannot build, please contact a K developer.
