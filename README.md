# open-vcdiff README

open-vcdiff is an encoder and decoder for the VCDIFF format, as described in
[RFC 3284](http://www.ietf.org/rfc/rfc3284.txt): The VCDIFF Generic Differencing
and Compression Data Format.

A library with a simple API is included, as well as a command-line executable
that can apply the encoder and decoder to source, target, and delta files.
For further details, please refer to
[this link](https://github.com/google/open-vcdiff/wiki/How-to-use-openvcdiff).

open-vcdiff comes with a CMake build script (
[CMakeLists.txt](CMakeLists.txt)) that can be used on a wide range of platforms ("C" stands for
cross-platform.). If you don't have CMake installed already, you can
download it for free from <http://www.cmake.org/>.

CMake works by generating native makefiles or build projects that can
be used in the compiler environment of your choice.  The typical
workflow starts with:
```bash
mkdir mybuild       # Create a directory to hold the build output.
cd mybuild
cmake ${OPEN_VCDIFF_DIR}  # Generate native build scripts.
```

By default, CMake will generate a Makefile to build the static libraries
`libvcdcom.a` (common), `libvcddec.a` (decoder) and `libvcdenc.a` (encoder), as
well as the `vcdiff` binary. To also build unit tests, run `cmake` as follows:

```bash
cmake -DBUILD_TESTING=ON ${OPEN_VCDIFF_DIR}
```

The git repository contains two submodules: gflags (used by the `vcdiff` binary)
and gtest (used by the unit tests). There are a few ways to handle these:

  1. To include the source code of these submodules, clone the open-vcdiff
     repository with  `git clone --recurse-submodules`. If you have already
     cloned the repository, you can initialize submodules after the fact by
     running: `git submodule update --init --recursive`.
  2. If your system has the gflags and/or gtest libraries installed, you can opt
     to use these instead, by passing `-Dvcdiff_use_system_gflags=ON` and/or
     `-Dvcdiff_use_system_gtest=ON` to `cmake`.
  3. Finally, if you only want to build the vcdiff libraries, you won't need
     the gflags and gtest libraries. Pass `-Dvcdiff_build_exec=OFF` to `cmake`
     to disable building the `vcdiff` binary. Note that tests are already
     disabled by default.

If you use Windows and have Visual Studio installed, a `gtest.sln` file
and several `.vcproj` files will be created.  You can then build them
using Visual Studio.

On Mac OS X with Xcode installed, a `.xcodeproj` file will be generated.

After compilation you should have the unit tests as well as `vcdiff`, a simple
command-line utility to run the encoder and decoder.  Typical usage of vcdiff is
as follows (the `<` and `>` are file redirect operations, not optional
arguments):
```bash
vcdiff encode -dictionary file.dict < target_file > delta_file
vcdiff decode -dictionary file.dict < delta_file  > target_file
```

To see the command-line syntax of vcdiff, use `vcdiff --help` or just `vcdiff`.

To run tests just use `make test` inside build directory.

To call the encoder from C++ code, assuming that dictionary, target, and delta
are all `std::string` objects:
```c++
#include <google/vcencoder.h>  // Read this file for interface details

// [...]

open_vcdiff::VCDiffEncoder encoder(dictionary.data(), dictionary.size());
encoder.SetFormatFlags(open_vcdiff::VCD_FORMAT_INTERLEAVED);
encoder.Encode(target.data(), target.size(), &delta);
```

Calling the decoder is just as simple:
```c++
#include <google/vcdecoder.h>  // Read this file for interface details

// [...]

open_vcdiff::VCDiffDecoder decoder;
decoder.Decode(dictionary.data(), dictionary.size(), delta, &target);
```

When using the encoder, the C++ application must be linked with the library
options `-lvcdcom` and `-lvcdenc`; when using the decoder, it must be linked
with `-lvcdcom` and `-lvcddec`.

To verify that the package works on your system, especially after making
modifications to the source code, please run the unit tests using `make test`.

For further details, please refer to
[this link](https://github.com/google/open-vcdiff/wiki/How-to-use-openvcdiff).
