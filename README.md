# Command Line Video Splitter

Simple command line Python script that splits video into multi chunks. Under the hood script uses [FFMpeg] so you will need to have that installed. No transcoding or modification of video happens, it just get's split properly.

Run `python ffmpeg-split.py -h` to see the options. Here are few samples of how it could be used:

## Spliting video into equal chunks

`python ffmpeg-split.py -f big_video_file.mp4 -s 10`

This splits `big_video_file.mp4` into chunks, and the size of chunk is 10 seconds. Each chunk will be suffixed with numeric index, for example `big_video_file-0.mp4`, `big_video_file-1.mp4`, etc.

Note: When using the above command, the videos chunks might have an issue where the first half of the video only has audio and no video, and only the second half has video. To fix this, run:

`python ffmpeg-split.py -f big_video_file.mp4 -v libx264 -s 10`

## Spliting video into equal chunks with some extra options

`python ffmpeg-split.py -f input.mp4 -s 600 -v libx264 -e '-vf "scale=320:240" -threads 8'`

This splits `input.mp4` into chunks, and the size of chunk is 600 seconds. With extra option to scale output to `320:240` and use 8 threads to speed up.

## Splitting videos into unequal chunks

In order to create unequal chunks of a video, you'll need to create ***manifest.json***.


***manifest.json***

```json

[
    {
        "start_time": 0,
        "length": 34,
        "rename_to": "video1"
    },
    {
        "start_time": 35,
        "length": 22,
        "rename_to": "video2.mp4"
    }
]

```

Afterwards run:

`python ffmpeg-split.py -f big_video_file.mp4 -m manifest.json`

This splits `big_video_file.mp4` into 2 video files, video1.mp4 and video2.mp4. The video1.mp4 is a 34 seconds
clip, starting from 0:00 to 0:34 of the `big_video_file.mp4`.


Alternatively, you can use a ***manifest.csv*** file to accomplish the task above.

***manifest.csv***:

```CSV

start_time,length,rename_to
0,34,video1
35,22,video2

```


#### Manifest Options

* start_time      - number of seconds into the video or start time
* length          - length of the video in seconds. The end time of the video is calculated by the start_time plus the length of the video.
* rename_to       - name of the video clip to be saved
* end_time        - end time of the video

NOTE: When using manifest.json to split the video, it's been modified so that when the end_time is provided, that is the TIME POSITION for the video to be split, and not the LENGTH of time for the clip to be split (e.g. if end_time is "00:09:00") it would now cut at the 9th minute of the clip instead of cutting at 9 minutes after the start_time.

## Additional Arguments

* -v or --vcodec        ffmpeg video codec to be used.
* -a or --acodec        ffmpeg audio codec to be used.
* -m or --manifest      manifest file to control the splitting of videos.
* -f or --file          video file to split.
* -s or --split-size    seconds to evenly split the videos
* -e or --extra         extra optional options for ffmpeg, e.g. '-e -threads 8' to use 8 threads to speed up.

#### Notes:

The -s and -m options should not be used together. If they are, -m option takes
precedent over the -s option


## Known Issues with ffmpeg

* There might be some videos that aren't showing properly after splitting the source video with ffmpeg. To resolve
this, use the -v option and pass in the associated video codec for the source video or video format. For example, mp4 videos
use h264 video codec. Therefore, running the command
`python ffmpeg-split.py -f example.mp4 -s -v h264`, may resolve this issue.


## Installing ffmpeg

See [FFmpeg installation guide](https://www.ffmpeg.org/download.html) for details.




[FFMpeg]: https://www.ffmpeg.org/


