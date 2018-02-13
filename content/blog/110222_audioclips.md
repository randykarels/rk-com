---
title: "Batch Create Audio Clips with Fades"
subtitle: "Drop the GUI and use FFmpeg and SoX"
date: 2011-02-22
date_updated: 2017-01-06
using: 'FFmpeg 3.2.x, Sox 14.4.x, MacOS&nbsp;10.10'
---

I needed to generate a bunch of audio clips. After seeing how tedious this would be with a GUI audio editor, I did some digging to find a faster solution that I could run as a batch from the command line. Turns out it was quite easy.

I first turned to the excellent [`FFmpeg`](http://www.ffmpeg.org/) library. I knew I would be able to read and convert between a wide variety of formats (from mp3 to aac and Apple Lossless) and specify start and stop points. I was surprised to learn that it lacked the functionality to add fades. 

For the fading, I turned to [`SoX`](http://sox.sourceforge.net/), which has the ability to add audio filters, but as far as I can tell can't read as many formats out of the box.

## Installation

Installation is simple with `homebrew`:

    $ brew install ffmpeg  
    $ brew install sox

## The commands

After everything is installed, the commands are straightforward.

    $ ffmpeg -y -i orig.m4a -ss 0:02:30 one.aiff

This converts `orig.m4a` to the lossless `one.aiff` file. The `-y` option will automatically overwrite the output if it exists and the `-ss` option specifies the start time which (in this example) will trim the first two and a half minutes from `orig.m4a`.

    $ sox one.aiff two.aiff fade 2 0:01:00 5 

This trims the `one.aiff` down to 1 minute in length, adds a two second fade to the beginning, adds a five second fade to the  end, and saves the result as `two.aiff`. 

    $ ffmpeg -y -i two.aiff -ab 128k clip.mp3

Finally, we use ffmpeg agin to transcode `two.aiff` down to our final 128k mp3 clip. The `-ab` flag is used to set the audio bitrate.

This works fine and all, but a better solution is to pipe the results of one command to the next to avoid creating the intermediate files:

    $ ffmpeg -i orig.mp3 -ss 0:02:30 -f aiff pipe: | \ 
      sox -t aiff - -t aiff - fade 2 1:00 5 | \
      ffmpeg -y -i pipe: -ab 128k clip.mp3

There are a few things to note.

* To pipe the output from the first `ffmpeg` command, we need to specify the output format with `-f aiff`.
* We also need to specify the incoming and outgoing formats of the pipes in the `sox` command with `-t aiff -`.
* I initially used `.wav` as the intermediate format, but this didn't work due to issues with the pipes and file headers. 
* The backslash characters are just to break up the command into shorter lines.


