```bash
# COMANDI FFMPEG ULTRA-OTTIMIZZATI per Ryzen 7 2700 + RX 580
# ACCELERAZIONE HARDWARE AMD VAAPI - MASSIME PRESTAZIONI

# OPZIONE 1: 4K ULTRA (se supportato dalla webcam)
ffmpeg -f v4l2 -video_size 3840x2160 -framerate 30 -i /dev/video0 \
  -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 \
  -hwaccel_output_format vaapi \
  -c:v h264_vaapi -profile:v high -level 5.1 \
  -qp 18 -bf 0 -g 30 -keyint_min 30 \
  -rc_mode VBR -b:v 15M -maxrate 20M \
  -f rtsp -rtsp_transport tcp rtsp://localhost:8554/camera

# OPZIONE 2: 1440p ULTRA PERFORMANCE (raccomandato)
ffmpeg -f v4l2 -video_size 2560x1440 -framerate 60 -i /dev/video0 \
  -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 \
  -hwaccel_output_format vaapi \
  -c:v h264_vaapi -profile:v high -level 5.1 \
  -qp 16 -bf 0 -g 60 -keyint_min 60 \
  -rc_mode VBR -b:v 10M -maxrate 15M \
  -f rtsp -rtsp_transport tcp rtsp://localhost:8554/camera

# OPZIONE 3: 1080p 120FPS ULTRA SMOOTH
ffmpeg -f v4l2 -video_size 1920x1080 -framerate 120 -i /dev/video0 \
  -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 \
  -hwaccel_output_format vaapi \
  -c:v h264_vaapi -profile:v high -level 4.2 \
  -qp 14 -bf 0 -g 120 -keyint_min 120 \
  -rc_mode VBR -b:v 8M -maxrate 12M \
  -f rtsp -rtsp_transport tcp rtsp://localhost:8554/camera

# OPZIONE 4: 1080p 60FPS QUALITÀ MASSIMA
ffmpeg -f v4l2 -video_size 1920x1080 -framerate 60 -i /dev/video0 \
  -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 \
  -hwaccel_output_format vaapi \
  -c:v h264_vaapi -profile:v high -level 4.2 \
  -qp 12 -bf 0 -g 60 -keyint_min 60 \
  -rc_mode VBR -b:v 6M -maxrate 10M \
  -f rtsp -rtsp_transport tcp rtsp://localhost:8554/camera

# OPZIONE 5: DUAL STREAM (se hai 2 webcam)
# Terminal 1:
ffmpeg -f v4l2 -video_size 1920x1080 -framerate 30 -i /dev/video0 \
  -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 \
  -hwaccel_output_format vaapi \
  -c:v h264_vaapi -qp 16 -bf 0 -g 30 \
  -rc_mode VBR -b:v 5M -maxrate 8M \
  -f rtsp -rtsp_transport tcp rtsp://localhost:8554/camera1 &

# Terminal 2:
ffmpeg -f v4l2 -video_size 1920x1080 -framerate 30 -i /dev/video1 \
  -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 \
  -hwaccel_output_format vaapi \
  -c:v h264_vaapi -qp 16 -bf 0 -g 30 \
  -rc_mode VBR -b:v 5M -maxrate 8M \
  -f rtsp -rtsp_transport tcp rtsp://localhost:8554/camera2 &

# ALTERNATIVA: ENCODING CPU (se VAAPI dà problemi)
ffmpeg -f v4l2 -video_size 1920x1080 -framerate 60 -i /dev/video0 \
  -c:v libx264 -preset veryslow -tune zerolatency \
  -crf 16 -pix_fmt yuv420p -g 60 -keyint_min 60 \
  -x264opts "ref=6:bframes=0:subme=8:trellis=2:me=umh:merange=32" \
  -threads 16 -b:v 8M -maxrate 12M -bufsize 4M \
  -f rtsp -rtsp_transport tcp rtsp://localhost:8554/camera
```

```bash
# Avvio OTTIMIZZATO del server RTSP
nice -n -10 mediamtx ./mediamtx.yml
```

```bash
# Avvio OTTIMIZZATO di Home Assistant
nice -n -5 ./venv/bin/hass -c /home/none/HomeAssistaint
```

# Browser ottimizzato:
http://localhost:8123

## DIAGNOSTICA HARDWARE AMD RX 580

```bash
# Verifica support VAAPI
ls -la /dev/dri/

# Test capacità webcam
v4l2-ctl --list-formats-ext -d /dev/video0

# Monitor GPU AMD
watch -n 1 "cat /sys/class/drm/card*/device/gpu_busy_percent 2>/dev/null || echo 'GPU monitoring non disponibile'"

# Monitor tutti i core Ryzen
htop

# Test encoding VAAPI
ffmpeg -f lavfi -i testsrc2=duration=10:size=1920x1080:rate=30 \
  -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 \
  -hwaccel_output_format vaapi \
  -c:v h264_vaapi -t 10 test_vaapi.mp4

# Benchmark comparativo CPU vs GPU
time ffmpeg -f lavfi -i testsrc2=duration=30:size=1920x1080:rate=60 \
  -c:v libx264 -preset fast -t 30 -f null -

time ffmpeg -f lavfi -i testsrc2=duration=30:size=1920x1080:rate=60 \
  -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 \
  -hwaccel_output_format vaapi \
  -c:v h264_vaapi -t 30 -f null -
```

## OTTIMIZZAZIONI SISTEMA per MASSIME PRESTAZIONI

```bash
# Impostazioni governor CPU per performance
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Priorità processi realtime
sudo sysctl -w kernel.sched_rt_runtime_us=950000

# Network optimizations
sudo sysctl -w net.core.rmem_max=134217728
sudo sysctl -w net.core.wmem_max=134217728

# GPU power state (per AMD)
echo high | sudo tee /sys/class/drm/card0/device/power_dpm_force_performance_level 2>/dev/null || echo "Comando GPU non supportato"
```

## RISOLUZIONE PROBLEMI con RX 580

1. **VAAPI non funziona**: Usa comando CPU alternativo
2. **Tearing video**: Aggiungi `-vsync 1` al comando FFmpeg  
3. **Overclock instabile**: Riduci framerate da 120 a 60fps
4. **Temperatura alta**: Controlla ventole GPU con `sensors`
5. **Driver AMD**: Verifica con `lspci -k | grep -A 3 VGA`
