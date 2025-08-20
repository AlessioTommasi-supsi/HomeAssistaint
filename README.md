# Home Assistant Camera Streaming - Ryzen 7 2700 Ottimizzato

## Avvio Sistema (3 terminali separati)

### Terminal 1: MediaMTX (Server RTSP)
```bash
cd /home/none/HomeAssistaint && mediamtx ./mediamtx.yml
```

### Terminal 2: FFmpeg (Camera Streaming)
```bash
ffmpeg -f v4l2 -video_size 640x360 -framerate 30 -i /dev/video0 -c:v libx264 -preset veryslow -tune zerolatency -crf 16 -pix_fmt yuv420p -g 30 -keyint_min 30 -x264opts "ref=6:bframes=0:subme=8:trellis=2:me=umh:merange=32" -threads 16 -b:v 3M -maxrate 5M -bufsize 2M -f rtsp -rtsp_transport tcp rtsp://localhost:8554/camera
```

### Terminal 3: Home Assistant
```bash
cd /home/none/HomeAssistaint && ./venv/bin/hass -c /home/none/HomeAssistaint
```

## Accesso
Apri browser: http://localhost:8123
