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

We will need to build binutils and a cross-compiled gcc, and we will put them into `/usr/local/i386elfgcc`, so
let's export some paths now. Feel free to change them to your liking.

```sh
export PREFIX="/usr/local/i386elfgcc"
export TARGET=i386-elf
export PATH="$PREFIX/bin:$PATH"
```

binutils
--------

Remember: always be careful before pasting walls of text from the internet. I recommend copying line by line.

```sh
mkdir $HOME/src
cd $HOME/src

curl -O https://ftp.gnu.org/gnu/binutils/binutils-2.31.tar.gz   # If the link 404's, look for a more recent version
tar xf binutils-2.31.tar.gz

mkdir binutils-build
cd binutils-build

../binutils-2.31/configure --target=$TARGET --enable-interwork --enable-multilib --disable-nls --disable-werror --prefix=$PREFIX 2>&1 | tee configure.log
make all install 2>&1 | tee make.log
```

gcc
---
```sh
cd $HOME/src

curl -O https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2
tar xf gcc-5.4.0.tar.bz2

mkdir gcc-build
cd gcc-build

../gcc-5.4.0/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --disable-libssp --enable-languages=c --without-headers
make all-gcc 
make all-target-libgcc 
make install-gcc 
make install-target-libgcc 
```

That's it! You should have all the GNU binutils and the compiler at `/usr/local/i386elfgcc/bin`, prefixed by `i386-elf-` to avoid
collisions with your system's compiler and binutils.

You may want to add the `$PATH` to your `.bashrc`. From now on, on this tutorial, we will explicitly use the prefixes when using
the cross-compiled gcc.
