AEO Light 2
===========

<img src="docs/aeolight.png" alt="Logo" width="100px">

AEO-Light 2 is a new generation of optical sound extraction software developed by the University of South Carolina in close cooperation with Tommy Aschenbach and extended by the open source community.

The original project was made possible by the generous support of the National Endowment for the Humanities.

How it works
------------

Cinematic film scanners traditionally use a two pass approach: once for the image using traditional scanning methods and once for the audio track using specialized audio scanners. AEO-Light allows extracting an audio track from raw image data taken from a single zoomed-out pass that includes both the image and the sound data.

![Film frame with both image and sound](docs/WIS66-179_MLK-lighter-thumb.png)

Usage
-----

TODO

Compiling for Linux/MacOS/Windows-MSYS2
---------------------------------------

1. Install required dependencies (TODO)
2. Get the code onto your local machine: `git clone --recursive <this page> && cd aeo-light`
3. Make an output directory: `mkdir build`
4. Set up the output directory: `cmake -S. -Bbuild`
5. Compile: `cmake --build build -j$(nproc)`

Compiling for Windows (Visual Studio)
-------------------------------------

Getting required dependencies for native windows is generally hard. To make native windows binaries, it's recommended to install [vcpkg](https://vcpkg.io/en/getting-started.html) and use that to get them.

`vcpkg install qt5:x64-windows ffmpeg:x64-windows tiff:x64-windows openexr:x64-windows dirent:x64-windows`

From there, instructions are identical to every other platform, except that you must tell cmake to look for requirements installed by vcpkg when first setting up the output directory, like so:

`cmake -S. -Bbuild -DCMAKE_TOOLCHAIN_FILE=[path to vcpkg]/scripts/buildsystems/vcpkg.cmake`

Also note that the steps must be done on the 'Developer Command Prompt' that gets installed with visual studio.

License
-------

This software seems to be licensed under a combination of the GPL V2+ and some strange custom copyleft license as provided in LICENSE.txt. All independent contributions are licensed under GPL v2+.
