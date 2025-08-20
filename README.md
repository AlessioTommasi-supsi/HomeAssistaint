

```bash

# Avvio aquisizione video da webcam e streaming RTSP
ffmpeg -f v4l2 -i /dev/video0 -c:v libx264 -preset ultrafast -tune zerolatency -b:v 1M -maxrate 1M -bufsize 2M -f rtsp rtsp://localhost:8554/camera

```

```bash
# Avvio del server RTSP
mediamtx ./mediamtx.yml
```

```bash
# Avvio di Home Assistant
./venv/bin/hass -c /home/none/HomeAssistaint
```


# Apro brosware con il servizio:
http://localhost:8123
