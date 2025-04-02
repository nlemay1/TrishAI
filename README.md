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
 

The easiest way to adjust VRAM usage if you have more or less VRAM is to adjust the Mistral model's context window, or gpu_layers, or both in this code block inside of trish.py before build:
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

<h2><b>Linux Install</b></h2>



You can then point to https://localhost:5000/static/index.html

<h2><b>Usage</b></h2>

To stop it:
```
docker-compose down
```

To start it:
```
docker-compose up -d
```

The WebGUI is pretty straightforrward, enter text/send and Trish responds in the dialog box.

Press "Talk" to record voice. It will record until you hit "Stop", and send the voice data to generate a response.

Latency can be anywhere between 3s-12s depending on the response length (tokens generated).


It can be accessed remotely while running via VPN, or whatever method of your choosing.

The tested setup uses Wireguard, and then split tunnel on the client IP under "Allowed IPs" to the machine hosting the app.


<h2><b>ALTERNATIVE INSTALL:</b></h2>

You can make your own virtual environment with Python3.10 and use the requirements.txt to run "trish.py".

<b>Note:</b> there is a dependency conflict that I haven't found a workaround to between TTS, numpy and chromadb.

TTS needs numpy 1.22.0, chromadb needs numpy 1.22.5 or up. 

I've just been running it by installing chromadb with the --no-deps flag.

There's probably a better way to resolve this, but I will reference the statement aforementioned: I'm not a very experienced coder.

<h2><b>Known Issues</b></h2>

- Still sometimes hallucinates responses occasionally. System prompt and memory context/recall could be structured much better than current.
- Latency with voice is pretty high, especially beyond ~100 tokens. Possible fixes could be parallel processing, or "chunking"/buffering sentences that stream.
- Security in general.....could just be better.
- Memory context and how it's currently being used doesn't really allow for a "real" conversation, or at least not super consistently. It's good at very structured prompts but anything beyond that and it can go off the rails.

<h2><b>Features/Fixes on the Wish List</b></h2>

- The whole crux of the project is to get it to a point where it can "configure" itself on startup and help the user find a clear direction for "use" with the AI. For example, if the user says "I'm going to use this for research" it can adjut accordingly in it's config, memory, etc. Or if the user says "I'm not sure yet." it takes a different path. Or if the user says "I'd like this for conversation and therapy". 
- Better conversational (per session context) and long term memory -- allowing for a more fluid and complex response flow.
- More search options on the pipeline, currently it's just Wikipedia, but the idea is to add more and allow user input to guide it (e.g. "Can you check that information from the Associated Press website?"), and also NewsAPI integration along with Weather.
- Add in file upload -- reading/summary/inference for PDFs, images and other documents
- Possibly adding in tasks on a basic level, perhaps reminders. I'm wary of giving it functionality to have OS level access as I'm both not experienced enough, and it opens up a much larger security threat
- A standalone mobile app
- A more solid GUI or at least a more involved WebGUI with settings to adjust different parameters within the model (i.e. max_tokens, gpu_layers, memory wipe, n_ctx, etc.)
- It used to have a "signal" endpoint where you could chat with it via the Signal App, with it's own number. I have used it by giving it a GoogleVoice number coupled with Signal-CLI, and it works well. Adding that back in might be nice.

Thanks for checking it out!
