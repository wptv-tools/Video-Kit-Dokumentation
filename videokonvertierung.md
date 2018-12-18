# Videokonvertierung für WordPress.tv

Für [Uploads auf WordPress.tv](https://wordpress.tv/submit-video/) werden nur eine beschränkte Anzahl von Videoformaten unterstützt. Ausserdem darf ein Videodatei maximal 1 GB groß sein.

Das MP4-Format funktioniert auf allen gängigen Plattformen bzw. Webbrowsern und ist daher gut geeignet für die Uploads. MP4-Videos können mit jeden Mediaplayer abgespielt werden.

Mit [FFmpeg](https://ffmpeg.org/) können Videos über die Kommandozeile konvertiert werden. Wer lieber mit einer grafischen Oberfläche arbeitet, kann [HandBrake](https://handbrake.fr/) nutzen. Im Hintergrund wird hier FFmpeg genutzt. FFmpeg kann fast jedes Format lesen. Daher ist eigentlich egal welches Format ein Aufnahme hat. Sowohl FFmpeg als auch HandBrake sind Open Source und verfügbar für Windows, Mac und Linux.

Um ein Video zu konvertieren kann folgendes Kommando genutzt werden:

```
ffmpeg -i aufnahme.mkv -crf 18 -c:v libx264 -profile:v high -pix_fmt yuv420p -c:a aac -movflags faststart neu.mp4
```

Der `-i` Parameter ist für das Ausgangsvideo.

Falls die Datei zu groß wird, kann das Video stärker komprimiert werden über den `-crf` parameter. Ein höherer Wert bedeutet eine starkere Komprimierung (und eine schlechtere Qualität). Ein Wert höher als 25 führt meistens zu deutlich sichtbaren Qualitätsverlusten.

Der Videocodec wird mit `-c:v` gesetzt.

Der Parameter `-profile:v` setzt ein standarisiertes Videoprofile, damit garantiert wird dass es überall abgespielt werden kann.

Das Pixelformat (`-pix_fmt`) wird auf `yuv420p` gesetzt, damit auch in Webbrowsern abgespielt werden kann.

Der Audiocodec wird mit `-c:a` gesetzt.

Damit MP4-Videos in Webbrowsern abgespielt werden können, bevor die komplette Datei heruntergeladen ist, wird die Metadaten am Anfang der Datei gesetzt mit `-movflags faststart`.

Die Zieldatei kommt immer am Ende des Kommandos.

Für den besten Ton wird empfohlen den [Fraunhofer FDK AAC](https://en.wikipedia.org/wiki/Fraunhofer_FDK_AAC)-Encoder (`libfdk_aac`) zu nutzen, aber dieser Encoder ist nicht standardmäßig verfügbar in FFmpeg.

```
ffmpeg -i aufnahme.mkv -crf 18 -c:v libx264 -profile:v high -pix_fmt yuv420p -c:a libfdk_aac -movflags faststart neu.mp4
```

Um zu sehen welche AAC-Encoders installiert sind, kann folgendes Kommando genutzt werden:

```
ffmpeg -codecs | grep aac
```

Falls in der Aufnahme der Ton schon im AAC-Format ist, kann diese ohne Qualitätsverlust kopiert werden.

```
ffmpeg -i aufnahme.mkv -crf 18 -c:v libx264 -profile:v high -pix_fmt yuv420p -c:a copy -movflags faststart neu.mp4
```

Infos über Videos können ausgeben werden mit `ffprobe`, dass zusammen mit FFmpeg ausgeliefert wird.

```
ffprobe aufnahme.mkv
```

Mit H.265 (auch bekannt als HEVC) können kleinere Dateien generiert werden. Das Encoding dauert aber wesentlich länger (4-5x langsamer als H.264). WordPress.tv unterstützt diese Videos. Auf aktuelle Versionen von Betriebssystemen können H.265-Videos abgespielt werden. Alternativ kann der [VLC](https://www.videolan.org/)- oder [MPV](https://mpv.io/)-Player verwendet werden. Beide sind Open Source und für Windows, Mac und Linux verfügbar.

```
ffmpeg -i aufnahme.mkv -crf 18 -c:v libx265 -profile:v high -pix_fmt yuv420p -c:a aac -movflags faststart neu.mp4
```

Die aktuelllen Videokits werden in FullHD (1920x1080 pixel) aufgenommen. Falls mit einer 4K-Auflösungen aufgenommen wurde, können diese Aufnahmen mit dem Parameter `-s` runtergerechnet werden.

```
ffmpeg -i aufnahme.mkv -crf 18 -c:v libx264 -profile:v high -pix_fmt yuv420p -c:a aac -movflags faststart -s 1920x1080 neu.mp4
```

Auf WordPress.tv werden die Aufnahmen immer automatisch konvertiert und auf HD (1280x720 pixel) runtergerechnet. Trotzdem ist es empfehlungswert Videos in FullHD-Auflösung hochzuladen, damit die Qualität gut bleibt. Die original Uploads können nämlich auch runtergeladen werden. Der Link ist hinter dem Share-Link im Video versteckt.

Im FFmpeg-Wiki gibt es mehr Infos zu Encodings:
- [H.264 Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/H.264)
- [H.265 Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/H.265)
- [AAC Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/AAC)
