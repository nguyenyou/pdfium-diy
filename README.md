# PDFium DIY

Build PDFium on MacOS M3 Max

## Setup

Step 1: Install XCode

https://developer.apple.com/xcode/

Step 2: Install

```sh
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
```

Assuming you cloned depot_tools to /path/to/depot_tools (note: you must use the absolute path or Python will not be able to find infra tools):

```sh
$ export PATH="$PATH:/path/to/depot_tools"
```

## Commands

```sh
gclient config --unmanaged https://pdfium.googlesource.com/pdfium.git
gclient sync -r origin/chromium/6815 --no-history --shallow
cp patch/BUILD.gn pdfium/BUILD.gn
cp patch/build/config/BUILDCONFIG.gn pdfium/build/config/BUILDCONFIG.gn
mkdir -p pdfium/build/toolchain/wasm/
cp patch/build/toolchain/wasm/BUILD.gn pdfium/build/toolchain/wasm/BUILD.gn
cp patch/core/fxge/BUILD.gn pdfium/core/fxge/BUILD.gn
cd pdfium && rm -rf out/prod
gn gen out/prod --args='is_debug=false treat_warnings_as_errors=false pdf_use_skia=false pdf_enable_xfa=false pdf_enable_v8=false is_component_build=false clang_use_chrome_plugins=false pdf_is_standalone=true use_debug_fission=false use_custom_libcxx=false use_sysroot=false pdf_is_complete_lib=true pdf_use_partition_alloc=false is_clang=false symbol_level=0'
echo 'target_os="wasm"' >> out/prod/args.gn
echo 'target_cpu="wasm"' >> out/prod/args.gn
ninja -C out/prod pdfium -v
cd ..
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk && ./emsdk install 3.1.70 && ./emsdk activate 3.1.70
source ./emsdk_env.sh
which emcc && emcc --version
cd .. && mkdir -p wasm
cp code/cpp/*.h wasm/ && cp code/cpp/*.cpp wasm/
cp compile.sh wasm/
cp exported-runtime-methods.txt wasm/exported-runtime-methods.txt
cp exported-functions.txt wasm/exported-functions.txt
chmod +x wasm/compile.sh
cd wasm
source ../emsdk/emsdk_env.sh && ./compile.sh
echo "Size of pdfium.wasm:"
ls -lh pdfium.wasm | awk '{print $5}'
echo "Size of pdfium.js:"
ls -lh pdfium.js | awk '{print $5}'

# Optional
cd .. && mkdir -p dist
cp wasm/pdfium.js dist/pdfium.js
cp wasm/pdfium.wasm dist/pdfium.wasm
```