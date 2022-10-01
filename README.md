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
3. Set up the output directory: `cmake -S. -Bbuild`
4. Compile: `cmake --build build -j$(nproc)`

Compiling for Windows (Visual Studio)
-------------------------------------

 1. Ensure the following system tools are installed:
    - Visual Studio
    - CMake
    - Git
    - [Vcpkg](https://vcpkg.io/en/getting-started.html)


 2. Open a "Developer Command Prompt" for your version of Visual Studio

 3. Get the code onto your local machine:
 
    ```
    git clone --recursive <this page>
    cd aeo-light
    ```

 4. Build required system libraries: `vcpkg install`

    > Note that this will compile some large video and GUI libraries from scratch and will take a very long time (sometimes over an hour), but it only needs to be done once.

 5. Set up a build directory for cmake, specifying that you want to use packages from vcpkg:
    
    `cmake -S. -Bbuild -DCMAKE_TOOLCHAIN_FILE=[path to vcpkg]/scripts/buildsystems/vcpkg.cmake`

    > If you're using VS Code with the 'CMake' plugin, you can set the following instead:
    > 
    > ```
    > "cmake.configureSettings": {
    >    "CMAKE_TOOLCHAIN_FILE": "[path to vcpkg]/scripts/buildsystems/vcpkg.cmake"
    > }
    > ```

 6. Open the `AEOLight.sln` file in the build directory to open the project in Visual Studio. From there, build or debug as desired.

    > On the command line, you can run `cmake --build build -j [number of cores]` to just build it.

License
-------

This software seems to be licensed under a combination of the GPL V2+ and some strange custom copyleft license as provided in LICENSE.txt. All independent contributions are licensed under GPL v2+.
