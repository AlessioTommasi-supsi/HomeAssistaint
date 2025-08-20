


ffmpeg -f v4l2 -i /dev/video0 -c:v libx264 -preset ultrafast -tune zerolatency -b:v 1M -maxrate 1M -bufsize 2M -f rtsp rtsp://localhost:8554/camera

mediamtx ./mediamtx.yml


./venv/bin/hass -c /home/none/HomeAssistaint


http://localhost:8123
