# TrishAI
A fully self-hosted conversational AI assistant using Mistral 7B, ChromaDB for memory, Faster-Whisper transcription, XTTSv2, and FastAPI accesible through a WebGUI

This is a personal passion project aimed at creating a smooth experience on local hardware with privacy and security in mind.
It can be used remotely in a browser if you setup a VPN on the host machine.

Current Tested Setup:
- GPU: RTX 3060 (12GB VRAM)
- Approx VRAM usage: ~9.5-10GB
  - Mistral-7B-Instruct-v0.2-Q4_K_M.gguf (using llama-cpp-python)
  - XTTSv2
  - Faster-Whisper
  - ChromaDB

TO BE USED IN A PYTHON3.10 VENV---------------------

```python3.10 main.py```

You can then point to http://localhost:5000/static/index.html

NOTE ABOUT CHROMADB DEPENDENCIES--------------------

TTS uses numpy 1.22.0, chromadb wants numpy 1.22.5.

I could not get around this conflict in Python3.10,

so I reccommend in your venv, doing "pip install chromadb --no-deps"

Unless someone has a better workaround and I'm an idiot. We'll find out.

If you would like to use it across the LAN on other systems or even via VPN, you need to create self signed certs with OpenSSL 

```
mkdir ssl
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout ssl/key.pem -out sll/cert.pem \
  -subj "/CN=localhost"
```
Then run:
```
uvicorn main:app --host 0.0.0.0 port 5000 --ssl-keyfile ssl/key.pem --ssl-certfile ssle/cert.pem
```

And if you want a remote interaction with the WebGUI:
  - Install wireguard VPN on the host machine as a server
  - Add your phone/tablet as a client
  - In your phone/tablet vpn app config set "Allowed Ips" to 192.168.x.x/32 to split tunnel
