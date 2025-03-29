# TrishAI
A fully self-hosted conversational AI assistant using Mistral 7B, 
ChromaDB for memory, 
Faster-Whisper transcription, 
XTTSv2, 
and FastAPI 
accessible through a WebGUI

Named after Trish Keenan from the band Broadcast (duh).

This is a personal passion project aimed at creating a smooth experience on local hardware with privacy and security in mind.
I'm not a very experienced coder, so go easy on me -- thanks in advance.

Still very much in the testing phase, and there are a lot of bugs/known issues.

It can be used remotely in a browser if you setup a VPN on the host machine.

Current Tested Setup:
- GPU: RTX 3060 (12GB VRAM)
- Approx VRAM usage: ~9.5-10GB at n_ctx=16384
  - Mistral-7B-Instruct-v0.2-Q4_K_M.gguf (using llama-cpp-python)
  - XTTSv2
  - Faster-Whisper
  - ChromaDB
 

The easiest way to adjust VRAM usage if you have more or less VRAM is to adjust the Mistral model's context window, or gpu_layers, or both in this code block of the trish-ai.py:
```
llm = Llama(
    model_path=MODEL_NAME,
    n_gpu_layers=75, # Adjust up or down for VRAM usage
    n_thread=8,
    n_batch=512,
    n_ctx=16384, # AND/OR adjust up or down for VRAM Usage (full model=32768)
    use_mmap=True,
    use_mlock=False,
    verbose=False,
    top_p=0.9,
    top_k=80,
    temperature=0.6,
    repeat_penalty=1.5,
    stop=["\n\n"]
)
```
The config above seems to be the best parameters settings so far for accurate inference and minimal hallucination on a 3060 with 12GB VRAM without maxxing it out.

Although, n_ctx=8192 has also yielded fairly good results, and leaves a lot of overhead for regular PC use alongside it.

Again, still in testing, so more to come.

Changing settings on XTTS, or moving Faster-Whisper to CPU, or some other trade-off increases latency by quite a bit.

I haven't tried parallel processing, but maybe soon.

Linux only for right now.

**Linux Install**

An install script is provided.

The dependencies are very finicky so it runs best in a docker container.

The script also creates a cert file so it can be used across your LAN on https://<machine-ip>:5000/static/index.html

This is the best/easiest way I've found to get mic permissions to work on any browser.

Runs best with python3.10 (which the script will also install).

**GitHub Clone:**

```
git clone https://github.com/nlemay1/trish-ai
cd trish-ai
chmod +x install-trish.sh
./install-trish.sh
```

**Tarball Install:**

```
tar -xzvf trish-ai.tar.gz
cd trish-ai
chmod +x install-trish.sh
./install-trish.sh
```

You can then point to https://localhost:5000/static/index.html

It can be accessed remotely while running via VPN, or whatever method of your choosing.

The tested setup is using Wireguard, and then split tunnel on the client IP to the machine hosting the app.


  - Add your phone/tablet as a client
  - In your phone/tablet vpn app config set "Allowed Ips" to 192.168.x.x/32 to split tunnel
