# bloom.cpp

Inference of HuggingFace's [BLOOM-like](https://huggingface.co/docs/transformers/model_doc/bloom) models in pure C/C++.

The repo was built on top of the amazing [llama.cpp](https://github.com/ggerganov/llama.cpp) repo by [@ggerganov](https://github.com/ggerganov) and [bloomz.cpp](https://github.com/NouamaneTazi/bloomz.cpp/tree/main) repo by [@Nouamane Tazi](https://github.com/NouamaneTazi) to support [BLOOM](https://huggingface.co/docs/transformers/model_doc/bloom) models in **GGUF** format. It supports all models that can be loaded using `BloomForCausalLM.from_pretrained()`.

## Usage

First, you need to clone the repo and build it:

```bash
git clone https://github.com/AndrewNgo-ini/bloom.cpp
cd bloom.cpp
make
```

## Download GGUF model format
I have converted these model
```bash
https://huggingface.co/hiieu/bloomz-560m-GGUF
https://huggingface.co/hiieu/bloomz-7b1-GGUF
```

### Convert weights

Then, you must convert the model weights to the ggml format. Any BLOOM model can be converted.

If you prefer, you can manually convert the weights on your machine:

```bash
# install required libraries
python3 -m pip install torch numpy transformers accelerate

# download and convert the 7B1 model to ggml FP16 format
python convert-bloom-hf-to-gguf.py models/bloomz-560m
# Note: you can add --use-f32 to convert to FP32 instead of FP16
```

Optionally, you can quantize the model to 4-bits.

```bash
./quantize ./models/bloomz-560m/ggml-model-f16.gguf ./models/bloomz-560m/ggml-model-q4_0.gguf q4_0 
```

### Run inference

Finally, you can run the inference.

```bash
./main -m models/bloomz-560m/bloomz-560m.gguf -n 128 -p "Translate to English: Je t’aime."
```

### Host the server


```bash
./server -m models/bloomz-560m/ggml-model-f16.gguf -c 2048
```

Example curl
```bash
curl --request POST \
    --url http://localhost:8080/completion \
    --header "Content-Type: application/json" \
    --data '{"prompt": "Building a website can be done in 10 simple steps:","n_predict": 128}'
```
