# Get sound from your laptop 
I want a minimal app that retrieves some sound from the mike of my laptop and does something with it. 

## Getting the sound volume
```python
# client.py
import pyaudio
import numpy as np
import asyncio
import websockets

FORMAT = pyaudio.paInt16
CHANNELS = 1
RATE = 44100
CHUNK = 1024
AUDIO_DEVICE_INDEX = None  # Set this to your microphone index if not default

async def send_volume_data():
    async with websockets.connect("ws://localhost:8000/ws") as websocket:
        p = pyaudio.PyAudio()
        stream = p.open(format=FORMAT, channels=CHANNELS, rate=RATE, input=True,
                        frames_per_buffer=CHUNK, input_device_index=AUDIO_DEVICE_INDEX)

        try:
            while True:
                data = np.frombuffer(stream.read(CHUNK), dtype=np.int16)
                volume = np.linalg.norm(data) * 2 / float(32768)
                await websocket.send(str(volume))
        finally:
            stream.stop_stream()
            stream.close()
            p.terminate()

asyncio.run(send_volume_data())
```

## Doing something with it

```python
# server.py
from fastapi import FastAPI, WebSocket

app = FastAPI()

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:
        data = await websocket.receive_text()
        print(f"Volume: {data}") 
```

## Running it

```
uvicorn server:app --reload
```
to finally get a stream of data:
```python
Volume: 1.7154853159421402
Volume: 0.883174336810771
Volume: 0.4824582136421814
Volume: 0.35587147546623377
```

