### 1. Environment Setup

First, ensure you have the **Emscripten SDK** installed and activated in your terminal.
Clone https://github.com/chocolate-doom/chocolate-doom for source code

```bash
cd ~/emsdk
source ./emsdk_env.sh

```

### 2. Configure the Source

Navigate to your Chocolate Doom directory and run the configuration tool using `emconfigure`. This prepares the Makefiles for WebAssembly instead of native Linux.

```bash
cd ~/work/doom/chocolate-doom
emconfigure ./configure --without-python --without-libsamplerate

```

### 3. Compile the Engine

Use `emmake` to run the build process. This generates the `.o` (object) files and the initial `.wasm` and `.js` binaries inside the `src/` directory.

```bash
emmake make -j$(nproc)

```

### 4. Package the Game Data (WAD)

Since browsers cannot access your local hard drive directly, you must package the `DOOM1.WAD` into an Emscripten `.data` file.

```bash
python3 $EMSDK/upstream/emscripten/tools/file_packager.py \
    src/chocolate-doom.data --preload DOOM1.WAD@/doom1.wad --js-output=src/chocolate-doom.js.append

# Append the loading logic to the main javascript file
cat src/chocolate-doom.js.append >> src/chocolate-doom.js

```

### 5. Final Deployment

Collect all the generated artifacts into a single folder to serve them via HTTP.

```bash
mkdir -p dist
cp src/chocolate-doom.html dist/index.html
cp src/chocolate-doom.js dist/index.js
cp src/chocolate-doom.wasm dist/index.wasm
cp src/chocolate-doom.data dist/index.data

```

### 6. Run the Server

Browsers block WebAssembly from running via `file://` protocols for security, so you must use a local server.

```bash
cd dist
python3 -m http.server 8080

```

**Access the game at:** `http://localhost:8080`

