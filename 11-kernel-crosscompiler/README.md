*Concepts you may want to Google beforehand: cross-compiler*

**Goal: Create a development environment to build your kernel**

If you're using a Mac, you will need to do this process right away. Otherwise, it could have waited
for a few more lessons. Anyway, you will need a cross-compiler once we jump to developing in a higher
language, that is, C. [Read why](http://wiki.osdev.org/Why_do_I_need_a_Cross_Compiler%3F)

I'll be adapting the instructions [at the OSDev wiki](http://wiki.osdev.org/GCC_Cross-Compiler). 


Required packages
-----------------

First, install the required packages. On linux, use your package distribution. 

Windows users need to set up a Unix-like enviroment such as MinGW or Cygwin. The "Windows Subsystem for Linux", released with the Windows 10 Anniversary update is also an option for using a cross compiler.

Install the following packages: 
- GCC
- G++
- Make
- Flex
- Bison
- Diffutils
- libintl-devel
- libgmp-devel
- libmpfr-devel
- libmpc-devel
- Texinfo

The Build
-----------
We build a toolset running on your host that can turn source code into object files for your target system.

You need to decide where to install your new compiler. It is dangerous and a very bad idea to install it into system directories. You also need to decide whether the new compiler should be installed globally or just for you. If you want to install it just for you (recommended), installing into `$HOME/opt/<folder-name>` is normally a good idea. If you want to install it globally, installing it into `/usr/local/<folder-name>` is normally a good idea.

Preparation:
Remember: always be careful before pasting walls of text from the internet. I recommend copying line by line.
```sh
# install or update all apt-get dependencies
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install gcc                   # not cross comiler
sudo apt-get install g++
sudo apt-get install make
sudo apt-get install bison
sudo apt-get install flex
sudo apt-get install gawk
sudo apt-get install libgmp3-dev
sudo apt-get install libmpfr-dev libmpfr-doc libmpfr4 libmpfr4-dbg
sudo apt-get install mpc
sudo apt-get install texinfo               # optional
sudo apt-get install libcloog-isl-dev      # optional
sudo apt-get install build-essential
sudo apt-get install glibc-devel
sudo apt-get -y install gcc-multilib libc6-i386
```

We will need to build binutils and a cross-compiled gcc, and we will put them into `/usr/local/i386elfgcc`, so
let's export some paths now. Feel free to change them to your liking.

```sh
export PREFIX="/usr/local/i386elfgcc"
export TARGET=i386-elf
export PATH="$PREFIX/bin:$PATH"
```

mpc
---
```sh
cd $HOME/src

curl -O http://ftp.jaist.ac.jp/pub/GNU/mpc/mpc-1.1.0.tar.gz
tar xf mpc-1.1.0.tar.gz

mkdir build-mpc
cd build-mpc

../mpc-1.1.0/configure --prefix="$PREFIX"

sudo make -j2
sudo make -j2 check
sudo make -j2 install
cd ..
```

binutils
--------

```sh
mkdir $HOME/src
cd $HOME/src

curl -O https://ftp.gnu.org/gnu/binutils/binutils-2.31.tar.gz   # If the link 404's, look for a more recent version
tar xf binutils-2.31.tar.gz

mkdir binutils-build
cd binutils-build

../binutils-2.31/configure --target=$TARGET --enable-interwork --enable-multilib --disable-nls --disable-werror --prefix=$PREFIX --with-sysroot 2>&1 | tee configure.log
                            
sudo make -j2
sudo make -j2 install
cd ..
```

gcc
---
```sh
cd $HOME/src

curl -O https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2
tar xf gcc-5.4.0.tar.bz2

mkdir gcc-build
cd gcc-build

../gcc-5.4.0/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --enable-languages=c,c++ --without-headers --with-mpc="$PREFIX" --with-system-zlib --disable-multilib

sudo make -j2 all-gcc
sudo make -j2 all-target-libgcc
sudo make -j2 install-gcc
sudo make -j2 install-target-libgcc
cd ..
```

That's it! You should have all the GNU binutils and the compiler at `/usr/local/i386elfgcc/bin`, prefixed by `i386-elf-` to avoid
collisions with your system's compiler and binutils.

You may want to add the `$PATH` to your `.bashrc`. From now on, on this tutorial, we will explicitly use the prefixes when using
the cross-compiled gcc.
