# Downloading YouTube videos and playlists with yt-dlp
[yt-dlp](https://github.com/yt-dlp/yt-dlp) is a fork from the now troubled [youtube-dl](https://youtube-dl.org/), the best part about yt-dlp aside from being actively maintained is that it follows similar commands to youtube-dl.

## Installing yt-dlp
You can install [yt-dlp](https://github.com/yt-dlp/yt-dlp/wiki/Installation) either using one of the official releases, or with your favorite package manager
Installing yt-dlp can simply be done with:
```shell
sudo wget https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -O /usr/local/bin/yt-dlp
sudo chmod a+rx /usr/local/bin/yt-dlp  # Make executable
```

If you ever need to update yt-dlp use:
```shell
yt-dlp -U
```

---

Some common usage examples for yt-dlp:
Print out the available formats and information with:
```shell
yt-dlp --list-formats  https://www.youtube.com/watch?v=1La4QzGeaaQ
```

Print JSON information for the video and audio streams:
```shell
yt-dlp --dump-json https://www.youtube.com/watch?v=1La4QzGeaaQ
```

## Single video or audio downloads
Download the best format (video + audio) that is equal to or greater than 720p width. Save this file as video_id.extension (1La4QzGeaaQ.mp4):
```shell
yt-dlp -f "best[height>=720]" https://www.youtube.com/watch?v=1La4QzGeaaQ -o '%(id)s.%(ext)s'
```

Download and merge the best video stream with the best audio stream:
```shell
yt-dlp -f 'bv*+ba' https://www.youtube.com/watch?v=1La4QzGeaaQ -o '%(id)s.%(ext)s'
```

Download 1080p video and merge with best audio stream:
```shell
yt-dlp -f 'bv*[height=1080]+ba' https://www.youtube.com/watch?v=1La4QzGeaaQ -o '%(id)s.%(ext)s'
```

Download 1080p video that is mp4 format and merge with best m4a audio format:
```shell
yt-dlp -f 'bv[height=1080][ext=mp4]+ba[ext=m4a]' --merge-output-format mp4 https://www.youtube.com/watch?v=1La4QzGeaaQ -o '%(id)s.mp4'
```

Embed video thumbnail into video file use `--embed-thumbnail`:
```shell
yt-dlp -f 'bv[height=1080][ext=mp4]+ba[ext=m4a]' --embed-thumbnail --merge-output-format mp4 https://www.youtube.com/watch?v=1La4QzGeaaQ -o '%(id)s.mp4'
```

Embed subtitles to video file (if they exist) `--embed-subs`:
```shell
yt-dlp -f 'bv[height=1080][ext=mp4]+ba[ext=m4a]' --embed-subs --merge-output-format mp4 https://www.youtube.com/watch?v=1La4QzGeaaQ -o '%(id)s.mp4'
```

Embed metadata about the video `--embed-metadata`:
```shell
yt-dlp -f 'bv[height=1080][ext=mp4]+ba[ext=m4a]' --embed-metadata --merge-output-format mp4 https://www.youtube.com/watch?v=1La4QzGeaaQ -o '%(id)s.mp4'
```

Get the best audio into mp3 file:
```shell
yt-dlp -f 'ba' -x --audio-format mp3 https://www.youtube.com/watch?v=1La4QzGeaaQ -o '%(id)s.mp3'
```

All the options for format selection and filtering can be found [here](https://github.com/yt-dlp/yt-dlp#format-selection) and [output template](https://github.com/yt-dlp/yt-dlp#output-template), There are a lot.

## Playlists
Download a YouTube playlist with the videos being 1080p and the best audio. Save into *channel_id/playlist_id* directory with the video added to an archive text file:
```shell
yt-dlp -f 'bv*[height=1080]+ba' --download-archive videos.txt  https://www.youtube.com/playlist?list=PLlVlyGVtvuVnUjA4d6gHKCSrLAAm2n1e6 -o '%(channel_id)s/%(playlist_id)s/%(id)s.%(ext)s'
```

## Channels
Download an entire YouTube channel as 720p video with best audio. Save into a folder named after the channel name with the video files being the title of the video (*Foo the Flowerhorn/5 Months Update – Flowerhorn Foods.webm*).
```shell
yt-dlp -f 'bv*[height=720]+ba' --download-archive videos.txt https://www.youtube.com/c/FootheFlowerhorn/videos -o '%(channel)s/%(title)s.%(ext)s'
```

---

# Downloading YouTube videos as audio with yt-dlp
As you will more than likely be wanting the best quality audio the format (`-f`) selector will be for best audio (`ba`).
```shell
yt-dlp -f 'ba' https://www.youtube.com/watch?v=dQw4w9WgXcQ -o '%(id)s.%(ext)s'
```

To save as the video title change `%(id)s.%(ext)s` to `'%(title)s.%(ext)s'` to save. For a custom filename `songname.%(ext)s`.

With [YouTubes VP9](https://en.wikipedia.org/wiki/VP9) codec this file will most likely be `.webm` or `.opus` extension.

## Saving video as an MP3 file
To save the highest quality audio as an mp3 file you need to define `--audio-format mp3` with `-x` which is extract audio
```shell
yt-dlp -f 'ba' -x --audio-format mp3 https://www.youtube.com/watch?v=dQw4w9WgXcQ  -o '%(id)s.%(ext)s'
```

To save the highest quality audio as an playlist and `--audio-quality 320K`
```shell
yt-dlp -f 'ba' -x --audio-format mp3 --audio-quality 320K "https://www.youtube.com/playlist?list=OLAK5uy_lgd3HFHWOWPcJDfrEi2q_Eh1aLOHXN8HI"
```

---

# Downloading a YouTube channel with yt-dlp
Downloading whole YouTube channels using the yt-dlp tool, with specifying video quality and format types.

## Downloaded video format
Go to the bottom for the full yt-dlp command, this just breaks down each part.

Start by calling yt-dlp and then giving the format options for the videos
```bash
yt-dlp -f 'bv*[height=720]+ba'
```
`'bv*[height=720]+ba'` this is 720p best video format with the best audio combined.

*Other option examples:*
`'bv[height=1080][ext=mp4]+ba[ext=m4a]'` 1080p mp4 video combined with best m4a audio.

`best` select the best format that contains both video and audio.

`'bv*[height<720]+ba'` Best video format under 720p combined with the best audio.

View yt-dlp the formating docs [here](https://github.com/yt-dlp/yt-dlp#format-selection).

## The archive list
When downloading a channel (or playlist) you want to list what videos you already have downloaded to avoid repetitively downloading them.

An archive list can be done with the download archive parameter where you state the file to save the downloaded  video id’s in
```shell
--download-archive the_list.txt
```

## Saving the videos
Now for the save folder and filename options with string formatting. In the example below it will be *channel name/video title.extension*
```shell
-o '%(channel)s/%(title)s.%(ext)s'
```

Other handy options include `%(channel_id)s` for the channel id string and `%(id)s` the video id string.

By using `%(ext)s` makes yt-dlp choose the extension based on the format parameters provided at the start.

## Thumbnail and metadata
If you wish you can save the thumbnail image and the video metadata to the file. Note .webm cannot have the thumbnail embedded, instead .mkv will be used.
```shell
--embed-thumbnail --embed-metadata
```

## The final command
Here is the full command using the parts above:
```shell
yt-dlp -f 'bv*[height>=720]+ba' --embed-thumbnail --embed-metadata --download-archive FootheFlowerhorn.txt https://www.youtube.com/c/FootheFlowerhorn/videos -o '%(channel)s/%(title)s.%(ext)s'
```

It will download FootheFlowerhorn YouTube channel into FootheFlowerhorn/ with the filename being the title of the video. The videos will be the best quality equal to or greater than 720p combined with the best audio.

---

# Searching YouTube videos with yt-dlp
How to search and get YouTube videos using the yt-dlp command line tool.

You can either list the video id’s and title or instantly download the video.

Searching YouTube videos with yt-dlp is done using the `ytsearch` flag. You can pass in the number of videos to process form the search by adding this value at the end `ytseach:8` is for 8 videos.

You can also pass in all e.g `ytsearchall`:

## Searching YouTube videos
This will list out 10 video id’s and the titles for the term "lebron james":
```shell
yt-dlp ytsearch10:lebron james --get-id --get-title
```

Alternatively, you can download the videos directly without passing any –get-* flag values.
```shell
yt-dlp ytsearch4:lebron james
```
Or
```shell
yt-dlp ytsearch20:lebron james --max-downloads 5
```
To get desired media formats pass in the format flag first:
```shell
yt-dlp -f 'bv*+ba' ytsearch2:lebron james
```

---

# Download a YouTube video subtitles file with yt-dlp
Download a YouTube videos subtitle file and choose a specific subtitle file format using yt-dlp.

To list the available subtitles on a YouTube video with yt-dlp:
```shell
yt-dlp --list-subs VIDEOURL
```

If the video has subtitles it will print them out including their available formats:
```
Language Name    Formats
en       English vtt, ttml, srv3, srv2, srv1, json3
```

Choosing the language and saving it as a subtitle format file:
```shell
yt-dlp --write-subs en --sub-format json3 VIDEOURL
```

Or as default will be a .vtt file
```shell
yt-dlp --write-subs en https://www.youtube.com/watch?v=HOBDzc1ME5M
```

---

# Download a YouTube video comments with yt-dlp
Another seemingly hidden feature with yt-dlp is the ability to save the YouTube video comments into a JSON file.

With yt-dlp downloading the video comments into a JSON file is done with the `--write-comments` flag.
```shell
yt-dlp --write-comments VIDEO_URL
```

For popular videos with a lot of comments, this can take several minutes.

An example of a comment in the JSON file:
```json
{
   "id": "UgzewWuFZvI0FsncvKR4AaABAg",
   "text": "These double ads are really getting on my nerves",
   "timestamp": 1614816000,
   "time_text": "1 year ago",
   "like_count": 1,
   "is_favorited": false,
   "author": "Michael M",
   "author_id": "UCKYW7m35n1kBassjpjakd7g",
   "author_thumbnail": "https://yt3.ggpht.com/ytc/AKedOLRr_ZwtCbdnb_qiqsKkwnTT4Y0eSW9SpgklicNt=s176-c-k-c0x00ffffff-no-rj",
   "author_is_uploader": false,
   "parent": "root"
 }
```
This flag is a part of the `--write-info-json` section where video metadata gets saved to the JSON file too. If you only want the video comments add `--no-write-info-json`.

[yt-dlp original readme](https://github.com/yt-dlp/yt-dlp/blob/master/README.md)