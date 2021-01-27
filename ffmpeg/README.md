# Table of Contents

- [Add Border to Movie](#add-border-to-movie)


## Add Border to Movie

Add a horizontal border above and below a video to make a square aspect ratio. The below code does this for a 1080p video. The `y` value corresponds to (1920 - 1080) / 2.
```commandline
ffmpeg -i input_file.mp4 -vf "pad=width=1920:height=1920:x=0:y=420:color=white" output_file.mp4
```
