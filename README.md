# llama.cpp Snapdragon Nightly Builds

Automated nightly builds of [llama.cpp](https://github.com/ggml-org/llama.cpp) with Qualcomm Snapdragon Hexagon NPU and OpenCL GPU backends enabled.

## What's Included

Each release contains prebuilt Android arm64-v8a binaries:

| File | Description |
|------|-------------|
| `bin/libllama-server.so` | HTTP inference server (renamed from llama-server for Android packaging) |
| `lib/libggml-hexagon.so` | Hexagon NPU backend (CPU-side) |
| `lib/libggml-opencl.so` | OpenCL GPU backend |
| `lib/libggml-htp-v68.so` | HTP kernels for Hexagon v68 |
| `lib/libggml-htp-v69.so` | HTP kernels for Hexagon v69 |
| `lib/libggml-htp-v73.so` | HTP kernels for Hexagon v73 |
| `lib/libggml-htp-v75.so` | HTP kernels for Hexagon v75 |
| `lib/libggml-htp-v79.so` | HTP kernels for Hexagon v79 |
| `lib/libggml-htp-v81.so` | HTP kernels for Hexagon v81 |
| `lib/libllama.so` | Core llama library |
| `lib/libggml.so` | GGML tensor library |

## Build Schedule

- **Nightly**: Every day at 04:00 UTC from `master`
- **Manual**: Trigger with a custom ref (branch/tag/SHA) via workflow dispatch

## Usage

### With android-gui-wrapper

Download the latest `.tar.gz` from [Releases](../../releases) and import it into the [android-gui-wrapper](https://github.com/TriDefender/android-gui-wrapper) app using the "Import" button in the Kernel Manager.

### Manual ADB Push

```bash
# Extract
tar -xzf llama-cpp-snapdragon-*.tar.gz

# Push to device
adb push llama.cpp/ /data/local/tmp/llama.cpp/

# Run (rename back to llama-server on device, or use the .so name directly)
adb shell "cd /data/local/tmp/llama.cpp && \
  LD_LIBRARY_PATH=./lib \
  ADSP_LIBRARY_PATH=./lib \
  GGML_HEXAGON_EXPERIMENTAL=1 \
  ./bin/libllama-server.so \
  --model /path/to/model.gguf \
  --device htp0 \
  --ctx-size 4096"
```

## Build Details

- **Container**: `ghcr.io/snapdragon-toolchain/arm64-android:v0.7`
- **CMake Preset**: `arm64-android-snapdragon-release`
- **Platform**: Android API 31, arm64-v8a
- **Backends**: Hexagon (GGML_HEXAGON=ON), OpenCL (GGML_OPENCL=ON)
- **Optimizations**: `-O3`, LTO, `armv8.7a+fp16+dotprod+i8mm`
