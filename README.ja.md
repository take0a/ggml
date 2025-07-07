# ggml

[ロードマップ](https://github.com/users/ggerganov/projects/7) / [マニフェスト](https://github.com/ggerganov/llama.cpp/discussions/205)

機械学習用テンソルライブラリ

***このプロジェクトは現在開発中です。\
開発の一部は現在、[llama.cpp](https://github.com/ggerganov/llama.cpp) および [whisper.cpp](https://github.com/ggerganov/whisper.cpp) リポジトリで行われています***

## 機能

- 低レベルのクロスプラットフォーム実装
- 整数量子化のサポート
- 幅広いハードウェアサポート
- 自動微分化
- ADAMおよびL-BFGSオプティマイザ
- サードパーティへの依存なし
- 実行時のメモリ割り当てゼロ

## Build

```bash
git clone https://github.com/ggml-org/ggml
cd ggml

# install python dependencies in a virtual environment
python3.10 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# build the examples
mkdir build && cd build
cmake ..
cmake --build . --config Release -j 8
```

## GPT inference (example)

```bash
# run the GPT-2 small 117M model
../examples/gpt-2/download-ggml-model.sh 117M
./bin/gpt-2-backend -m models/gpt-2-117M/ggml-model.bin -p "This is an example"
```

詳細については、[examples](examples) フォルダ内の対応するプログラムを参照してください。

## Using CUDA

```bash
# fix the path to point to your CUDA compiler
cmake -DGGML_CUDA=ON -DCMAKE_CUDA_COMPILER=/usr/local/cuda-12.1/bin/nvcc ..
```

## Using hipBLAS

```bash
cmake -DCMAKE_C_COMPILER="$(hipconfig -l)/clang" -DCMAKE_CXX_COMPILER="$(hipconfig -l)/clang++" -DGGML_HIP=ON
```

## Using SYCL

```bash
# linux
source /opt/intel/oneapi/setvars.sh
cmake -G "Ninja" -DCMAKE_C_COMPILER=icx -DCMAKE_CXX_COMPILER=icpx -DGGML_SYCL=ON ..

# windows
"C:\Program Files (x86)\Intel\oneAPI\setvars.bat"
cmake -G "Ninja" -DCMAKE_C_COMPILER=cl -DCMAKE_CXX_COMPILER=icx -DGGML_SYCL=ON ..
```

## Android 向けのコンパイル

こちらのダウンロード [ページ](https://developer.android.com/ndk/downloads) から NDK をダウンロードし、解凍してください。NDK_ROOT_PATH 環境変数を設定するか、以下のコマンドで CMAKE_ANDROID_NDK への絶対パスを指定してください。

```bash
cmake .. \
   -DCMAKE_SYSTEM_NAME=Android \
   -DCMAKE_SYSTEM_VERSION=33 \
   -DCMAKE_ANDROID_ARCH_ABI=arm64-v8a \
   -DCMAKE_ANDROID_NDK=$NDK_ROOT_PATH
   -DCMAKE_ANDROID_STL_TYPE=c++_shared
```

```bash
# create directories
adb shell 'mkdir /data/local/tmp/bin'
adb shell 'mkdir /data/local/tmp/models'

# push the compiled binaries to the folder
adb push bin/* /data/local/tmp/bin/

# push the ggml library
adb push src/libggml.so /data/local/tmp/

# push model files
adb push models/gpt-2-117M/ggml-model.bin /data/local/tmp/models/

adb shell
cd /data/local/tmp
export LD_LIBRARY_PATH=/data/local/tmp
./bin/gpt-2-backend -m models/ggml-model.bin -p "this is an example"
```

## リソース

- [ggml入門](https://huggingface.co/blog/introduction-to-ggml)
- [GGUFファイル形式](https://github.com/ggerganov/ggml/blob/master/docs/gguf.md)
