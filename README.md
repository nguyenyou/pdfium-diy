# PDFium DIY

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
cd emsdk && ./emsdk install latest && ./emsdk activate latest
source ./emsdk_env.sh
which emcc && emcc --version
cd .. && mkdir -p wasm
cp code/cpp/*.h wasm/ && cp code/cpp/*.cpp wasm/
cp compile.sh wasm/
cp exported-runtime-methods.txt wasm/exported-runtime-methods.txt
cp exported-functions.txt wasm/exported-functions.txt
chmod +x wasm/compile.sh
```