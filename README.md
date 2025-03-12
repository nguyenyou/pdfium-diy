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
```