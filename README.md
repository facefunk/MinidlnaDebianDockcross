# MiniDLNA Debian Dockcross

A few shell scripts which run on linux on an x86_64/amd64 system, set up a 
Dockcross toolchain and cross-compile MiniDLNA targeting Debian Bullseye on 
armv7a architecture.

This project is an attempt to document a process for cross-compilation. The best
documentation is working code.

## Build
The build script can be run as-is and should produce an APT repository 
containing a working MiniDLNA deb file.

### Prerequisites
Make sure you have the following packages installed before running this script:
- docker

### Configuration
Any configuration that you might foreseeably want to change is in the file
`conf`. Obviously these are just simple scripts and can be easily edited but
some assumptions have been made that would be non-trivial to alter. For
instance, it should be straight-forward to change the target architecture but
targeting a different OS than Debian would take a bit of work.

### Run
```bash
$ ./build
```
That's it.

### What it does
The build script first builds a Docker container based on a dockcross 
cross-compiler image then uses that container to:
1. Clone the MiniDLNA and FFmpeg Git repositories.
2. Download deb files containing the appropriate libraries to satisfy all the 
other dependencies of MiniDLNA from the Debian repositories based on our chosen 
architecture.
3. Build FFmpeg. In this step we compile FFmpeg ourselves because the Debian 
FFmpeg packages have every single bell and whistle installed which translates to
another 131 debs to process. Building for Debian does mean that we are locked to
a previous version of FFmpeg, however.
4. Massage the libraries into a structure that is more easily linked by 
MiniDLNA.
5. Build MiniDLNA.
6. Package MiniDLNA up into a deb file for installation on the target machine.
7. Provide an APT repository that can be hosted locally to allow your MiniDLNA
installation to integrate with APT on the target machine.
8. Profit.

## Upload
A separate upload script is also provided that demonstrates one method of
uploading and installing the generated APT repository.

### Prerequisites
- rsync
- ssh

### Configuration
At the bare minimum, edit the `conf` script variable `servAddr` to reflect your
Debian Bullseye based destination MiniDLNA server.

### Run
```bash
$ ./upload
```

## Why not use a standard Debian toolchain?
Good Question. I like the idea of using a Docker container. I did go some way 
towards setting up a standard Debian toolchain in a Docker container but it 
seemed to be getting overly complex. The presented approach appeared to be the 
path of least resistance, I may have missed something. Please feel free to tell 
me where I went wrong if you feel so disposed.
