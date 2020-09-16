# Note-of-exe2py
This note records an off-the-shelf way to decompile executable files produced by pyinstaller.

## Introduction

After using pyinstaller to generate the executable file serval days before, I deleted my python source code by mistake. I had tried ways to retrieve this,  mainly searched about how to transform the .exe file to my original python source file. Here I record this solution that worked for me.

## Process

### Extract .pyc files

> .pyc files are created by the Python interpreter when a .py file is imported. They contain the "compiled bytecode" of the imported module/program so that the "translation" from source code to bytecode (which only needs to be done once) can be skipped on subsequent imports if the .pyc is newer than the corresponding .py file, thus speeding startup a little. But it's still interpreted. Once the *.pyc file is generated, there is no need of *.py file, unless you edit it.	-- [Rajendra Dharmkar](https://www.tutorialspoint.com/answers/Rajendra-Dharmkar)

First, we need to extract .pyc files from the .exe file utilize [archive_viewer.py](https://github.com/pyinstaller/pyinstaller/blob/develop/PyInstaller/utils/cliutils/archive_viewer.py), which is the original tool provided by pyinstaller.

You can download this python file and save it under the same directory of your executable file, or you can alternatively built a new directory which contains both [archive_viewer.py](https://github.com/pyinstaller/pyinstaller/blob/develop/PyInstaller/utils/cliutils/archive_viewer.py) and your executable file, for me it's `Clipboard-Translator.exe`

Then simply type `python archive_viewer.py Clipboard-Translator.exe` in command line, press Enter.

It shows like this. ![image-20200916215439192](C:\Users\86024\AppData\Roaming\Typora\typora-user-images\image-20200916215439192.png)

It shows a bunch of .pyc /.pyz files that can be extracted from the executable file. 

The only file we care about is the compiled form of the source file, for me that's `start`. So to save the .pyc file, type  `X start`, press Enter, then it will ask you for the name of the output file, which is `start.pyc`, type it, press Enter. Then you can see an .pyc file generated under the same directory of the .exe file.

After that, we need to apply this again to the first file listed in the output, which we'll need later. Let's say it's `pyimod01_os_path`. Save it as any name you like, suppose it's `hhh.pyc`.

### Decompile

You may need to install [HxD](https://mh-nexus.de/en/hxd/) and [python-decompile3](https://github.com/rocky/python-decompile3) to continue to this section.

Open `hhh.pyc` with [HxD](https://mh-nexus.de/en/hxd/), then copy the first 4 bytes of it.

![image-20200916222238610](C:\Users\86024\AppData\Roaming\Typora\typora-user-images\image-20200916222238610.png)

That number called Magic numbers, which varies from versions of python, is supposed to appear at the beginning of every .pyc file. Pyinstaller, somehow, removes this number from the .pyc file generated from your source code.

Then you can insert this 4 bytes at the beginning of `start.pyc`. ![image-20200916222852328](C:\Users\86024\AppData\Roaming\Typora\typora-user-images\image-20200916222852328.png)

Save the files.

Type `decompyle3 start.pyc` in command line under the same directory of `start.pyc` to decompile it, your source code will be back in the output.

## Note

At the stage of decompile, you may need to choose the correct version of decompiler according to the version of python, which you used to generate the executable file.

List of decompiler:

- https://github.com/andrew-tavera/unpyc37/ : indirect fork of https://code.google.com/archive/p/unpyc3/ The above projects use a different decompiling technique than what is used here. Instructions are walked. Some instructions use the stack to generate strings, while others don't. Because control flow isn't dealt with directly, it too suffers the same problems as the various uncompyle and decompyle programs.
- https://github.com/rocky/python-xdis : Cross Python version disassembler
- https://github.com/rocky/python-xasm : Cross Python version assembler
- https://github.com/rocky/python-decompile3/wiki : Wiki Documents which describe the code and aspects of it in more detail

## Acknowledge

http://o1o1o1o1o.blogspot.com/2016/11/python-pyinstaller-reverse-engineer.html

