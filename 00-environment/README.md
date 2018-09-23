*Concepts you may want to Google beforehand: linux, mac, terminal, compiler, emulator, nasm, qemu*

**Goal: Install the software required to run this tutorial**

I'm working on Windows, though Linux is better because it will have all the standard tools already
available for you.

On Windows,
First to make using NASM and QEMU easy to use we will register them in Windows. 

This is done by simply adding each path to the Path Environment Variable. 
This is found by going to System Properties (Right Click on My Computer and Click Properties) then go to Advanced System Settings and Click the Environment Variables Button. 

The the path field is a list of paths separated by semi-colons. 

We simply need to add the paths that the QEMU and NASM executable files are in to this field and click OK.  You will need to restart you computer for this change to take effect.

Compiling with NASM
To compile basic assembly code in NASM simply open a command window and navigate to the folder the code is located in and call nasm.exe with the following parameters.
`nasm -f bin "<assembly file name>.asm" -o "<output file name>.bin"`

The -f parameter specifies the file type, we are using binary output.  We then specify the name of the file we put the assembly code and, finally the -o parameter specifies the output file path including the name.

Running with QEMU
Once your have your code compiled you can run it in QEMU with the following command.
`qemu-system-x86_64 "<file name>.bin"`

This specifies that we are going to emulate x86 processor and we are setting the Hard Drive to our binary file.

On a mac, install Homebrew and then `brew install qemu nasm`

Don't use the Xcode developer tools `nasm` if you have them installed, they won't work for the most cases. Always use `/usr/local/bin/nasm`


On some systems qemu is split into multiple binaries. You may want
to call `qemu-system-x86_64 binfile`
