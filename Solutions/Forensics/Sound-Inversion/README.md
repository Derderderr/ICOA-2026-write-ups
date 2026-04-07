## Solution

we recieve a .wav file, there's no obvious trace in its spectrogram and invert the whole audio does't work

By inspecting:
```
ffprobe Sound_Inversion.wav

-->
...
    comment         : Is it just me or is the right channel different?
...
```

so we would like to invert the right channel, open the file in audacity, select split stereo to mono to seperate the left and right channel, 
then select the bottom track, in the top tool bar, choose Effect -> Special -> Invert.

Now we can hear a person talking, note that the first letter of the word (if it's a word) would be the flag, we can slow the audio down and record the letter

answer:
```
pecan{hidden_audio_only_ononeear}
```
