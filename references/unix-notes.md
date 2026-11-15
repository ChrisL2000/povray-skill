## POV-Ray for Unix 3.7 (install, run, notes)

Unix Table of Contents

| | Home | POV-Ray for Unix | POV-Ray Tutorial | POV-Ray Reference |

| Introduction |
| What is POV-Ray? |
| What is POV-Ray for Unix? |
| ... And What Is It Not? |
| Who Can Use POV-Ray for Unix? |
| Spelling |
| Reporting Bugs |
| Trademarks |
| Features |
| New Features |
| Display Options |
| Benchmark Mode |
| Sample scene render scripts |
| KDE Integration |
| Getting Started |
| Available distributions |
| Configuration |
| The I/O Restrictions configuration file |
| The main POV-Ray INI file |
| Starting a Render Job |
| X Window display |
| SVGAlib display |
| Output file formats |
| Interrupting POV-Ray |
| Tutorials |
| Rendering the Sample Scenes |
| Automated execution |
| Post-processing Images |
| I/O Restrictions |
| Configuration file format |
| File I/O Security |
| Shellout Security |
| Permitted Paths |
| Examples for path settings |
| Example configuration file |
| Understanding File Types |
| POV Files |
| INI Files |
| INI File Sections |
| Understanding POV-Ray Options |
| Special Command-Line Options |
| Benchmark Rendering Mode |
| Bug Reports |
| Known Bugs |
| Bug Reporting via Email |
| Bug Reporting via our Bug Tracking System |
| Speed Considerations |
| Render Priority |
| Render Window |
| Memory |
| Scene Features |
| Render Quality |

window.__CF$cv$params={r:'a5aa2f73ed846a59',t:'MTc5NDcwMzAyNQ==',u:'01a1dca2e4c0768b8db8441f80a53816',ut:'IKRTMtiMyE0AffzMjMMuEbXR3x7qVxUWAGDUbR5fdXI-1794703025-1.2.1.1-DZFvtDRCpxUTvHva52BQ.OSAKpBBjkLbqCebaG9yWQt28C1omhRwcpvwZvdJHzBtCjl8V33UGvlmP.VRwwohSsqtzD_uik_OemXR8Hcdzug',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa2f73ed846a59',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();

Unix Section 1

| | Home | POV-Ray for Unix | POV-Ray Tutorial | POV-Ray Reference |

# 1.1 Introduction

This section provides information on how to use the Unix version
of the Persistence of Vision Raytracer™.

Before we start we'll have a brief discussion of just what this program is, and
what it does.

## 1.1.1 What is POV-Ray?

POV-Ray™ is short for the Persistence of Vision Raytracer™, a tool for producing
high-quality computer graphics. POV-Ray is Free Software (as per the definition of the Free
Software Foundation) as its source code is licensed under the
Affero General Public License (AGPL).

Without a doubt, POV-Ray is the worlds most popular raytracer. From our website alone we see
several hundred thousand downloads per year, and this of course doesn't count the copies that are
obtained from our mirror sites, other internet sites, on CD-ROM, or from persons sharing their
copies.

The fact that it is free helps a lot in this area, of course, but there's more to it than that.
There are quite a few other free raytracers and renderers available. What makes this program
different ?

The answers are too numerous to detail in full here. Suffice it to say that POV-Ray has the
right balance of power and versatility to satisfy extremely experienced and competent users,
while at the same time not being so intimidating as to completely scare new users off.

Of course, the most important factor is image quality, and in the right hands, POV-Ray has it.
We, the developers, have seen images that were rendered using our software that we at first
thought were photographs - they were that realistic.

Note: Photo-realism is an advanced skill; one that takes some practice to develop.

## 1.1.2 What is POV-Ray for Unix?

POV-Ray for Unix is essentially a version of the POV-Ray rendering engine prepared
for running on a Unix or Unix-like operating system (such as GNU/Linux). It contains
all the features of POV-Ray described in chapters 2 and 3 of the documentation, plus
a few others specific to Unix and GNU/Linux systems. These additional features do not
affect the core rendering code. They only make the program suitable for running
under an Unix-based system, and provide the user with Unix-specific displaying
capabilities. For instance, POV-Ray for Unix can use the X Window System to display
the image it is rendering. On GNU/Linux machines, it can also display the image
directly on the console screen using the SVGA library.

POV-Ray for Unix uses the same scheme as the other supported platforms to create
ray-traced images. The POV-Ray input is platform-independant, as it is using
text files (POV-Ray scripts) to describe the scene: camera, lights, and various
objects. An extensive discussion of the
POV-Ray Scene Description Language
can be found elsewhere in this documentation.

## 1.1.3 ... And What Is It Not?

POV-Ray for Unix is not a modeler. It will not let you design scenes graphically
on-screen. There are several shareware and freeware programs available for this purpose. To
write or modify POV-Ray scenes you just edit the actual text file containing the commands.

New users might be surprised to learn that, although this sounds primitive, it is in
fact one of the things that gives POV-Ray so much power and flexibility. There are many other
rendering programs that give you a point-and-click interface, but when it comes down to absolute
control of your scene, it's hard to beat a text-based scene description language (even though
it's harder for some to learn).

## 1.1.4 Who Can Use POV-Ray for Unix?

Almost anyone can use POV-Ray for Unix. If you have never seen a ray tracing program
before, you can have great fun just rendering the sample scenes and animations installed
together with POV-Ray for Unix. Once you have studied some scene files and spent a little
bit of time reading this documentation, you can start putting basic scene files together.

Remember, though, that you do need to invest some time getting to know the program. For
instance, you do not have to worry about most of the POV-Ray settings at first, but some of
them are critical. Ignore them and POV-Ray for Unix may not do what you expect. The default
settings have been selected so as to be satisfactory in the large majority of cases, but
sometimes they will not give you what you want. Don't give up. The solution is bound to be a
simple one, as long as you take the time to read the documentation. Investing a little bit of
time getting to know the program will pay off - guaranteed!

As for experienced POV-Ray users, you will find that running POV-Ray in a Unix environment
makes life much easier. Working under Unix means you can use editors and other powerful tools
with which you may already be quite familiar. POV-Ray modes exist for popular text editors
such as Emacs, and these provide many of the features found in the POV-Ray GUI interface on
other platforms.

## 1.1.5 Spelling

Some US Residents have commented on the spelling of some words such as colour, initialisation,
and minimised (they expect color, initialization, or minimized). POV-Ray is an international
effort and as such our spelling sometimes conforms to British, European, or Australian, rather
than American, English. You'll find examples of this both in this document and in POV-Ray
itself (for example, POV accepts both 'color' and 'colour' as valid keywords).

## 1.1.6 Reporting Bugs

There is a section in this help file for bug reports. Make sure that you read it
before sending us a complaint/bug report !

## 1.1.7 Trademarks

The terms 'POV-Ray', 'Persistence of Vision Ray Tracer', and 'POV-Team'
are trademarks of Persistence of Vision Raytracer Pty. Ltd.

UNIX (also commonly referred to as 'Unix') is a registered trademark of The Open Group.
'Linux' is a trademark of Linus Torvalds.
X Window System is a trademark of X Consortium, Inc. All other trademarks are acknowledged
as being the property of their respective owners.

window.__CF$cv$params={r:'a5aa2f75eed37564',t:'MTc5NDcwMzAyNQ==',u:'01a1dca2e5ee7342862fabe13178ec3b',ut:'2GCDw6whLPm.juk0IXgwVGIgXWA7FF1iDEpuFDgkN5c-1794703025-1.2.1.1-G_JdHb85VcZNliTravLTlMzTG3b6O4ALDNKlM5qQsYVhiBbEd1jXb2Qi5iuTEPMAT4UiMU1kNLmPOqZ.EOYnNzllClnMcFpDj9GGcpIr3cA',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa2f75eed37564',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();

Unix Section 2

| | Home | POV-Ray for Unix | POV-Ray Tutorial | POV-Ray Reference |

# 1.2 Features

This section covers version 3.7 changes and new features that apply to POV-Ray for Unix. See the section Changes and New Features Summary for information about non-platform specific changes.

## 1.2.1 New Features

Reworked the built-in `--benchmark`

The built-in benchmark now accepts the `+L<path>` command-line option
and now ONLY reads the built-in INI file.
It also accepts `+wt` to set the number of work threads
and now also accepts the `--version` parameter

 Added preliminary Linux support for these two features:

 CPU timer; might return incorrect results depending on the platform.
 signal catching when aborting a render by hitting Ctrl+C

 Added support for the `+P` option to allow pause when done.
 Unix default file gamma changed from from 1.0 to 2.2.

## 1.2.2 Display Options

The Unix version now has a single binary which supports display using the X Window System, or using the SVGA library with the console on GNU/Linux systems only, or via text graphics otherwise. If the command-line options or INI files turn on display, the following heuristic is used to determine what kind of display should be used. If the DISPLAY environment variable is set, the X Window display is used. Otherwise, if the user is on the console, and has permission to do so, SVGAlib is used. Otherwise, text graphics are used.

Text graphics display a crude 75x24 ASCII version of the image which can be useful none the less to monitor the render progress on a text terminal. Using text graphics and the Verbose `+v` option simultaneously will not work very well.

Note: See the section Known Bugs for more information about a problem with the X11 windows system.

## 1.2.3 Benchmark Mode

POV-Ray 3.7 for Unix provides a new mode to render a built-in version of the standard POV-Ray benchmark scene. See the Benchmark Rendering Mode documentation for details.

## 1.2.4 Sample scene render scripts

POV-Ray 3.7 includes a set of shell scripts to automatically render all the samples coming with POV-Ray and that generate HTML files to easily browse the sample scenes. See Rendering the Sample Scenes for more information.

## 1.2.5 KDE Integration

The binary package for x86-Linux (`povlinux.tgz`) comes with an install script that generates some useful entries in the KDE panel for convenient access to the POV-Ray documentation and configuration files.

The generic Unix source distribution also comes with the `kde_install.sh` script to perform the same task.

window.__CF$cv$params={r:'a5aa30dc6b672f65',t:'MTc5NDcwMzA4Mg==',u:'01a1dca3c5e57663be760a1f8a042ea7',ut:'Y.fykG5r0wRu53YyliP1FSkCPj.B0Y4AkTZ6FjNisu4-1794703082-1.2.1.1-xHoC9FtZsKsKUW4pcVykO8R1fM3T64tw6m14_8hNH1Vp7HRLJPdPbT.Rky8Z5Xs6qPUQ5WHNi62pCNG1KiIZCD7djGqPkvHIMYHNROZ6LKE',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa30dc6b672f65',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();

Unix Section 3

| | Home | POV-Ray for Unix | POV-Ray Tutorial | POV-Ray Reference |

# 1.3 Getting Started

This section will help new users get started with POV-Ray for Unix.

## 1.3.1 Available distributions

The official distribution for version 3.7 of POV-Ray for Unix is a source code only release. This package contains all the source files and Makefiles required for building POV-Ray. Building the program from source should work on most Unix/Linux systems. The package uses a configuration mechanism to detect the appropriate settings needed to build POV-Ray on your platform. Since all the required support libraries are NOT included in the package, you need to make sure you have an adequate build environment.

The distribution package is available for download from the POV-Ray website. See the INSTALL file in the source package for more details.

## 1.3.2 Configuration

All official versions of POV-Ray for Unix come with procedures for correctly installing and configuring POV-Ray. These explanations are for reference.

### 1.3.2.1 The I/O Restrictions configuration file

When POV-Ray starts it reads the configuration for the I/O Restriction feature from the `povray.conf` files. See the I/O Restrictions Documentation for a description of these files.

### 1.3.2.2 The main POV-Ray INI file

When starting, POV-Ray for UNIX searches for an INI file containing default configuration options. The details can be found in the INI File Documentation.

## 1.3.3 Starting a Render Job

Starting POV-Ray rendering any scene file is as simple as running `povray` from a command-line with the scene file name as an argument. This will work with either a POV file or an INI file (as long as it has an associated POV file). See Understanding File Types. The scene is rendered with the current POV-Ray 3 options (see
Understanding POV-Ray Options).

Note: One of the more common errors new users make is turning off the display option. The Display option `+d` is ON by default. If you turn this OFF in the INI file or on the command
line, POV-Ray will not display the file as you render.

Please also note that POV-Ray for Unix will write the output file to a .png by default. There is no way to 'save the render window' after rendering is completed. If you turned file output off before starting the render, and change your mind, you will have to start the rendering all over again. We recommend that you just leave file output on all the time.

### 1.3.3.1 X Window display

When the X Window display is used, the rendered image is displayed in a graphics window. During rendering, the window will be updated after every scanline has been rendered, or sooner if the rendering is taking a long time. To update it sooner you can click any mouse button in the window or press (almost) any key. Pressing `<CTRL-R>` or `<CTRL-L>` during rendering will refresh the whole screen. If you have the `Exit_Enable` or `+X` flag set, pressing 'q' or 'Q' at any time during the rendering will stop POV-Ray rendering and exit. The rendering will pause when complete if the `Pause_When_Done` or `+P` flag is set. To exit at this point, press the 'q' or 'Q' key or click any mouse button in the window.

POV-Ray 3.7 includes a color icon in the program if it was compiled with libXpm (which is available on most platforms where the X Window System is installed). If this icon is used for the render view window depends on the window manager
being used (KDE, Gnome, fvwm, or ...). POV-Ray also comes with a separate color icon `xpovicon.xpm`
for use with the window managers that can use external icons. For instance, to have fvwm use this icon, copy the icon file to one of the directories pointed to by PixmapPath (or ImagePath) which is defined in your `$HOME/.fvwmrc`. Then, add the following line in `$HOME/.fvwmrc`:

```
Style "Povray" Icon xpovicon.xpm
```

and re-start the X Window server, re-starting fvwm will not be enough. Using this icon with another window manager may use a different procedure.

Documentation of the special command line options to configure the X Window display can be found in Special Command-Line Options.

### 1.3.3.2 SVGAlib display

For GNU/Linux systems that don't have the X Window System installed, or for those Linux users who prefer to run on the console, it is possible to use the SVGA library to display directly to the screen. For SVGAlib display, the `povray` binary must be installed as a setuid root executable. If POV-Ray does not use SVGAlib display, first try as root:

```

chown root.root povray
chmod 4755 povray
```

Note: Doing this may have serious security implications. Running POV-Ray as root or through 'sudo' might be a better idea.

If it still doesn't work then make sure SVGAlib is installed on your machine and works properly. Anything that can at least use the 320x200x256 mode (regular VGA) should be fine, although modes up to 1280x1024x16M are possible. If you do not have root privileges or can't have the system admin install POV-Ray, then you must use the X Window or text display which do not require any special system privileges to run. If you are using a display resolution that is lower than what you are rendering, the display will be scaled to fit as much of the viewing window as possible.

### 1.3.3.3 Output file formats

The default output file format of POV-Ray for Unix is PNG, or `+fn`.

This can be changed at runtime by setting the `Output_File_Type` or `+fx` option.

Alternately the default format can be changed at compile time by changing `DEFAULT_OUTPUT_FORMAT` in the `syspovconfig.h` file located in the `~vfe/unix` directory of the source code distribution.

Other convenient formats on Unix systems might be PPM `+fp` and TGA `+ft`.

For more information about output file formats see File Output Options.

 If you are generating histogram files (See CPU Utilization Histogram) in the CSV format (comma separated values), then the units of time are in tens of microseconds (10 x 10-6 s), and each grid block can store times up to 12 hours.

## 1.3.4 Interrupting POV-Ray

To interrupt a rendering in progress, you can use CTRL-C (SIGINT), which will allow POV-Ray to finish writing out any rendered data before it quits. When graphics display mode is used, you can also press the 'q' or 'Q' keys in the rendering preview window to interrupt the trace if the `Test_Abort` or `+X` flag is set.

## 1.3.5 Tutorials

There is a comprehensive beginner's tutorial included in this help file. It relates to the use of POV-Ray in general, on all platforms, not only Unix, so we recommend that you read the rest of section 1 first before attempting to go any further, as section 1 tells you how to use the interface of POV-Ray for Unix itself. The more ambitious may like to check out the advanced features section.

## 1.3.6 Rendering the Sample Scenes

POV-Ray for Unix comes with a set of shell scripts to automatically render the sample scenes coming with POV-Ray.

These shell scripts are usually installed in `/usr/local/share/povray-3.7/scripts`. They require a bash
compatible shell. There are three scripts that are supposed to be called by the user.

`allscene.sh`:

renders all stills. The syntax is:

```
allscene.sh [log] [all] [-d scene_directory] [-o output_directory] [-h html_file]
```

If `html_file` is specified a HTML listing of the rendered scenes is generated. If ImageMagick is installed the listing will also contain thumbnails of the rendered images.

`allanim.sh`:

renders all animations. The syntax is:

```
allanim.sh [log] [-d scene_directory] [-o output_directory] [-h html_file]
```

If ffmpeg is installed the script will compile mpeg files from the rendered animations.

`portfolio.sh`:

renders the portfolio. The syntax is:

```
portfolio.sh [log] [-d scene_directory] [-o output_directory]
```

The portfolio is a collection of images illustrating the POV-Ray features and include files coming with the package.

If the option `log` is specified, a log file with the complete text output from POV-Ray is written to filename `log.txt`

If `scene_directory` is specified, the sample scenes in this directory are rendered, otherwise the scene directory is determined form the main povray ini file, usually `/usr/local/share/povray-3.7/scenes`.

If `output_directory` is specified, all images are written to this directory; if it is not specified the images are written into the scene file directories. If the directories are not writable, the images are written in the current directory. All other files (html files, thumbnails) are written here as well.

To determine the correct render options the scripts analyze the beginning of the scene files. They search for a comment of the form:

```
// -w320 -h240 +a0.3
```

in the first 50 lines of the scene. The animation script possibly also uses an INI file with the same basename as the scene file. The `allscene.sh` has the additional `all` option which if specified renders also scenes without such an options comment (using default options then).

## 1.3.7 POV-Ray for Unix Tips

### 1.3.7.1 Automated execution

POV-Ray for Unix is well suited for automated execution, for example, for rendering diagrams displaying statistical data on a regular basis or similar things.

POV-Ray can also write its image output directly to stdout. Therefore the image data can be piped in another program for further processing. To do this the special output filename '-' needs to be specified. For instance:

```

povray -iscene.pov +fp -o- | cjpeg > scene.jpg
```

will pass the image data to the `cjpeg` utility which writes the image in the JPEG format.

The text output of POV-Ray is always written to stderr, it can be redirected to a file with (using a Bourne-compatible shell):

```

povray [Options] 2> log.txt
```

See also Directing Text Streams to Files.

For remote execution of POV-Ray, as for example in a rendering service on the web, make sure you read and comply with the [[Documentation:Tutorial Section 5#POV-Ray User License|POV-Ray Legal Document]].

### 1.3.7.2 Post-processing Images

For Unix systems, the PBM utilities (PBMPLUS, NetPBM) and ImageMagick are an excellent choice for post-processing utilities, especially if you only have a command-line interface to Unix.

window.__CF$cv$params={r:'a5aa30dcf9ef6399',t:'MTc5NDcwMzA4Mw==',u:'01a1dca3c63170eb86bb6b8e6f9dcb37',ut:'wHGDFtFPXuZL.M5kDb3WXNvcIsznSTEWa4tP.zl6xH8-1794703083-1.2.1.1-Fr1hYth9Mw55L8CEAyRe7d7aF5Pgwrh7kAc3YqlMY9bEoINjEj4cnHXaK2AaTGse0l2FP7UNJv5v_tZYw2l7oI290gtZMsVEJyHK5llQs.s',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa30dcf9ef6399',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();

Unix Section 4

| | Home | POV-Ray for Unix | POV-Ray Tutorial | POV-Ray Reference |

# 1.4 I/O Restrictions

I/O Restrictions is a feature that was introduced in POV-Ray for Unix 3.5. The purpose of this feature is to attempt to at least partially protect a machine running POV-Ray from having files read or written outside of a given set of directories.

The need for this is related to the fact that the POV-Ray scene language has, over the years, become something more akin to a scripting language combined with a scene-description model. It is now possible to write obfuscated POV-Ray code, and to open, create, read and write arbitrary files anywhere on the target system's hard disk, subject to operating system permission.

The basic idea of I/O Restrictions is to attempt to protect the user from a script that may have been downloaded from an untrusted source, and which may attempt to create or modify files that it should not.

The I/O Restriction facility hooks the file open and creation functions in the core POV-Ray renderer code, and allows the Unix version to allow or deny any particular file operation.

Note: We do not guarantee that the I/O Restriction facility will actually stop anything from happening. There is always the chance that, like almost all software, it could have a bug in it that causes it to malfunction. Therefore, the onus is on the person who chooses to load an INI or scene file into POV-Ray to ensure that it does not do anything that it should not do. Please consider I/O Restrictions just a sometimes-helpful backup for manual checks.

Please read this section in full so that you understand the caveats and conditions of the facility, as some directories are allowed by default.

## 1.4.1 Configuration file format

The I/O Restrictions are configured by two separate configuration files. This can be a system-wide configuration, or a user configuration file located in the following places, on most systems `$PREFIX` is `/usr/local`.

 `$PREFIX/etc/povray/3.7/povray.conf`
 `$HOME/.povray/3.7/povray.conf`

POV-Ray will always use the most strict version of what is specified; user settings can only make security more strict.

The general syntax of these files is:

```

;Comment

[Section]
setting
```

Warning: If neither of these files exists I/O Restrictions are deactivated!

## 1.4.2 File I/O Security

The `[File I/O Security]` section only contains a single setting which is either `none`, `read-only` or `restricted`.

 `none` means that there are no restrictions other than those enforced by the file system.
 `read-only` means that files may be read without restriction.
 `restricted` means that files access is subject to restrictions as specified in the rest of this file. See below for details.

## 1.4.3 Shellout Security

The `[Shellout Security]` section determines whether POV-Ray will be allowed to call scripts.

This section contains a single setting which is either `allowed` or `forbidden`.

 `allowed` means that shellout will work as specified in the documentation.
 `forbidden` means that shellout will be disabled.

See the section Shell-out to Operating System for more details.

## 1.4.4 Permitted Paths

The `[Permitted Paths]` section contains a list of directories which are specifically allowed for either reading or reading and writing. These paths are only used when the setting for `[File I/O Security]` is either `read-only` or `restricted`.

 Directories that are only allowed for reading are added with `read=directory`.
 For allowing reading and writing use `read+write=directory`.
 If `[File I/O Security]` is set to `read-only`, any directory can be used to read in a file, and `read+write` entries must specify which directories are allowed for writing.
 If `[File I/O Security]` is set to `restricted`, reading and writing is allowed only in the directories given by the `read` and `read+write` entries.

If the directory name contains spaces it has to be quoted or doubly-quoted. There can be spaces before and after the equal sign. Read-only and read/write entries can be specified in any order.

If you want the permissions for a specified directory to also extend to all of its subdirectories wildcards are permitted.

For example:

```

read*=directory
read+write*=directory
```

Both relative and absolute paths are permitted, so the dot character can be especially useful. The install directory, typically `/usr/local/share/povray-3.7` or `/usr/share/povray-3.7`, can be specified with `%INSTALLDIR%`, the user home directory with `%HOME%`. The install directory and its
descendents are typically only writable by root; therefore it does not make sense to have `%INSTALLDIR%` in read/write directory paths.

Note: Since user-level permissions are at least as strict as system-level restrictions, any paths specified in the system-wide `povray.conf` will also need to be specified in `~/.povray/3.7/povray.conf` if this file exists.

### 1.4.4.1 Examples for path settings

```
[Permitted Paths]
read=%INSTALLDIR%
```

Would permit reading from the directory where the POV-Ray supplementary files are installed.

Note that the installdir location does not relate to where the binary is run from - it relates to the information defined at compile-time. Relative paths are legal as well, and will be resolved only once at load time (but relative to the current directory, not the installdir). For example, a relative path like the following ...

```
[Permitted Paths]
read+write=../output
```

Would be resolved with relation to the current directory at the time POV-Ray for Unix was started, so if you started povray while in the directory `~/myscenes/newscene`, then the above path would be resolved as `~/myscenes/output`. Please note that the actual location of the povray binary is not relevent here - it is the current directory that matters, which is typically not that of the program.

## 1.4.5 Example configuration file

Here is a complete example for a `povray.conf` file:

```
[File I/O Security]
; none
; read-only
restricted

[Shellout Security]
allowed
; forbidden

[Permitted Paths]
read*=%INSTALLDIR%/include
read*=%INSTALLDIR%/scenes
read=%INSTALLDIR%/../../etc
read+write=.
read+write*=/tmp
```

window.__CF$cv$params={r:'a5aa30dd7c61a31a',t:'MTc5NDcwMzA4Mw==',u:'01a1dca3c69a77cb889e1c627f54e1ac',ut:'ZGwkvQrlrVaVEW1sNXmL6uHhpQkfG2QwNZ71klzwj64-1794703083-1.2.1.1-UAHhwy9cqHXF6Bw4A6Ia0mchuEEqHxTQttfXZ_Y6T5NaJvjPNQHa5Z6MhC61UiCTz3ebvN_x1QeB_ZFU37VMmip1Yf8NNjLiFqtcUU0rrBo',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa30dd7c61a31a',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();

Unix Section 5

| | Home | POV-Ray for Unix | POV-Ray Tutorial | POV-Ray Reference |

# 1.5 Understanding File Types

This section points out the various types of text files that POV-Ray for Unix works with.

## 1.5.1 POV Files

POV-Ray for Unix works with two types of plain text files.

The first is the standard POV-Ray scene description file. Although you may give files of this type any legitimate file name, it is easiest if you give them the `.pov` extension. In this Help file, scene description files are referred to as POV files.

The second type, the initialization file. Initialization files normally have `.ini` extensions and are referred to in this help file as INI files.

## 1.5.2 INI Files

An INI file is a text file containing settings for what used to be called POV-Ray command-line options. It replaces and expands on the functions of the DEF files associated with previous versions of POV-Ray. You can store a default set of options in the main POV-Ray INI file which is searched for at the following locations:

The place defined by the `POVINI` environment variable.
`./povray.ini`
`$HOME/.povray/3.7/povray.ini`
`$PREFIX/etc/povray/3.7/povray.ini` (`$PREFIX` by default is `/usr/local`)

For backwards compatibility with version 3.5, POV-Ray 3.7 also attempts to read the main INI file from the old locations when none is found at the places above:

`$HOME/.povrayrc`
`$PREFIX/etc/povray.ini` (`$PREFIX` by default is `/usr/local`)

Note: Use of these locations is deprecated, they will not be available in future versions.

Any other INI file can be specified by passing the INI file name on the command line.

One of the options you can set in the INI file is the name of an input file. You can specify the name of a POV file here. This way you can customize POV-Ray settings for any individual scene file.

For instance, if you have a file called scene.pov, you can create a file scene.ini to contain settings specific for scene.pov. If you include the option `Input_File_Name=scene.pov` in scene.ini, and then run `povray scene.ini`, POV-Ray will process scene.pov with the options specified in scene.ini.

Remember, though, that any options set at the command line when you activate an INI file override any corresponding options in the INI file, see the section Understanding POV-Ray Options. Any options you do not set in the INI file will be taken as last set by any other INI file or as originally determined in `povray.ini`.

You can instruct POV-Ray to generate an INI file containing all the options active at the time of rendering. This way, you can pass a POV file and its associated INI file on to another person and be confident that they will be able to generate the scene exactly the same way you did. See the section Using INI Files for more information.

### 1.5.2.1 INI File Sections

Sections are not files in themselves; they are portions of INI files. Sections are a means of grouping multiple sets of POV-Ray options together in a single INI file, by introducing them with a section label. Consider the following INI file, taken from the POV-Ray 3 documentation:

```

; RES.INI
; This sample INI file is used to set resolution.

+W120 +H100 ; This section has no label.
; Select it with "RES"

[Low]
+W80 +H60 ; This section has a label.
; Select it with "RES[Low]"

[Med]
+W320 +H200 ; This section has a label.
; Select it with "RES[Med]"

[High]
+W640 +H480 ; Labels are not case sensitive.
; "RES[high]" works

[Really High]
+W800 +H600 ; Labels may contain blanks
```

If you select this INI file, the default resolution setting will be 120 x 100. As soon as you select the `[High]` section, however, the resolution becomes 640 x 480.

For more information on POV-Ray options and INI files consult the section on Setting POV-Ray Options.

window.__CF$cv$params={r:'a5aa30de0f940f7d',t:'MTc5NDcwMzA4Mw==',u:'01a1dca3c6e0704c8acf82ee5b801d92',ut:'hwMeHeFA5s4wcFcoFyT3a2GU3vpiSD8RMkfoHX5UwOQ-1794703083-1.2.1.1-ZKVRt8XJEun_sQoataObvmNK0boQ3W4yohs1B4FIfFlZokI45HrMzVvp77GQY6kxjXxoLUVcEEHCWG_r5V91oRwaC9xY6Ie3bJESSbOiomc',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa30de0f940f7d',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();

Unix Section 6

| | Home | POV-Ray for Unix | POV-Ray Tutorial | POV-Ray Reference |

# 1.6 Understanding POV-Ray Options

Options are what previous versions of POV-Ray used to refer to as command-line options. The term is no longer accurate for POV-Ray 3.x, because the command line is only one way of passing options to the program. The other, more convenient way, is to use INI files.

## 1.6.1 Special Command-Line Options

POV-Ray for Unix supports several special command-line options not recognized by other versions. They follow the standards for programs that run under the X Window System.

Note: See the section Known Bugs for more information about a problem with the X11 windows system.

`-display <display_name>`
Display preview on display_name rather than the default display. This is meant to be used to change the display to a remote host. The normal dispay option `+d` is still valid.

`-geometry [WIDTHxHEIGHT][+XOFF+YOFF]`
Render the image with `WIDTH` and `HEIGHT` as the dimensions, and locate the window `XOFF` from the left edge, and `YOFF` from the top edge of the screen, or if negative the right and bottom edges respectively.
For instance: `-geometry 640x480+10+20` creates a display for a 640x480 image placed at (10,20) pixels from the top-left corner of the screen. The `WIDTH` and `HEIGHT`, if given, override any previous `+Wn` and `+Hn` settings.

`-help`
Display the X Window System-specific options. Use`-H` by itself on the command-line to output the general POV-Ray options.

`-icon`
Start the preview window as an icon.

`-title <window_title>`
Override the default preview window title with `window_title`.

`-visual <visual_type>`
Use the deepest visual of `visual_type`, if available, instead of the automatically selected visual.

Valid `visual_type` values:

StaticGray
GrayScale
StaticColor
PseudoColor
TrueColor
DirectColor

Note: If you are supplying a filename with spaces in it, you will need to enclose the filename itself within quotes.

## 1.6.2 Benchmark Rendering Mode

POV-Ray for Unix has a built-in version of the standard benchmark scene. In version 3.7 the built-in benchmark mode has been further modified to run without accessing any other distribution files, so now benchmarking can now be done with just the compiled executable.

To run this scene use the `--benchmark` command line option:

```

povray --benchmark
```

Note that running the benchmark takes some time, on a 3 GHz AMD x2 250 processor with 4GB ram, about 12 minutes. There will be no image or file output from this render. For more information on the standard POV-Ray benchmark have a look at the Benchmarking with POV-Ray page.

window.__CF$cv$params={r:'a5aa30de7d4209a2',t:'MTc5NDcwMzA4Mw==',u:'01a1dca3c72c70f5849633c2e631e1ff',ut:'XtI1zCOot2NLwI4LZFMUNDODQLl1JckzEilQUI1FNQU-1794703083-1.2.1.1-XwIxnQu725wkiYO5dOoTZvZHvZcOlAMgyxD.toN8ErD_PcsOaRPVrrVuJPuXCgwFfAGKo01YKaiWJrs2ym.jRlo9adui9JOcTSkmthGhtTc',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa30de7d4209a2',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();

Unix Section 7

| | Home | POV-Ray for Unix | POV-Ray Tutorial | POV-Ray Reference |

# 1.7 Bug Reports

Before submitting any bug reports, make sure that the bug is not in the list of known bugs below. Please remember that the POV-Ray users who provide assistance on our news server are volunteers who give their time for free so you can enjoy this program for no charge. Keep this in mind when wording your message.

MAKE ABSOLUTELY SURE that the subject of your report isn't already covered in this document. Questions that are already answered either in this help file, any README's, installation notes, FAQ's, or other files supplied with this program will most likely not get the reply you expect.

We strongly recommend that you check out the online version of the POV-Ray Questions and Tips before submitting a report.

## 1.7.1 Known Bugs

These items are known bugs with the version 3.7 release:

Due to problems with X11 libraries, some special command line parameters are not available. However, a preview display can still be enabled if building from source and the optional SDL library is available to link against.
There are configure phase issues that can cause problems compiling POV-Ray on FreeBSD systems. See this bug report for more information, and ways to mitigate this issue.

## 1.7.2 Bug Reporting via Email

Unfortunately, email bug reporting is no longer available due to the abuse of this facility by a number of users. You may only report bugs in POV-Ray by using the bugtracker mentioned in the next section.

## 1.7.3 Bug Reporting via our Bug Tracking System

Bug reports may be lodged about povray by using the bug tracking system. The bugtracker is monitored regularly by the POV-Ray development team and they will address any properly-lodged bug reports in the most timely manner possible.

You may like to remember, however, that no matter where you end up getting assistance from, users who show that they have looked in the documentation and read the POV-Ray Questions and Tips first, will be much more likely to receive the response they want than someone who obviously has not done their homework. It's also important to check that the bug hasn't already been reported - if it has, and is still open, don't add a new report - if you have relevant information not already in the existing report, consider adding a comment.

Any messages or reports sent to us may (not necessarily will, but may) be re-posted in our newsgroups, on USENET, or on our web site, if we consider it appropriate to do so.

You should not expect any reply to bug reports. If you wish to follow the progress of a bug, it's best to use the bug tracker (be sure to configure your account to receive notifications of replies to your bug). You can also request to be emailed when there are replies to an existing bug report.

The minimum information to include in your bug report is:

The version number of POV-Ray for Unix.
Your operating system or GNU/Linux distribution and version. It's surprising how many people fail to provide this basic piece of information. We need to know what version of Unix you are running.
Your computer; what processor and how much memory.
A complete description of the problem and what led up to it. It will make it a lot easier to
track down if you can include a scene file that will cause the problem. If the file is large,
try removing parts until you have the minimum scene file that will cause the problem.

window.__CF$cv$params={r:'a5aa30def91344ce',t:'MTc5NDcwMzA4Mw==',u:'01a1dca3c77173349ebac365006148e7',ut:'dlfSF0dsf_94y_PU1u9Buks1MFF.sEvfknFbUT2cIC8-1794703083-1.2.1.1-d0ekID8ZwCjgd7YouEGtibOOER6Mz6PKbqW7RAcVNuxVQAKIQr6wYw0pcldCrVk1bOT7xsGZnwtBhk.1fV4JCstNk4GSLDqODhBJKecOyWk',i:60};(function(){if(!document.body)return;var l=window.location,o;try{o=new URL(l.origin).origin}catch(e){try{o=new URL(new URL(l.href).origin).origin}catch(e){o=''}}var u='/cdn-cgi/challenge-platform/scripts/precursor/main.js?ray=a5aa30def91344ce',s=document.createElement('script');s.src=o?new URL(u,o).href:u;document.head.appendChild(s);})();
