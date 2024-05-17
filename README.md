*Smart vibrance shader*

This PotPlayer shader is designed to enhance the vibrance of color in a selective manner, it works in this way :

1) Boost the saturation of every color if its below a certain threshold.
2) If the color have an amount of saturation already over the threshold, it will receive less and lesser boost until nothing will be boosted, this method prevent clipping, burning and glowing.
3) If the color is literally a grayscale (or near grayscale) the boost will also be decreased to preserve clarity and intelligibility on certain situation were the scene is dark and otherwise details will be lost.

This shader is the equivalent of the NVIDIA "RTX Dynamic Vibrance" new feature adapted for video playback witouth the need of AI cores, it works beautifully with animes and movies especially if you don't already have other gamma, brightness, contrast and digital filter alterations applied to it.

The shader is fully commented and configurable, there's should no need for further explanation, in case, just ask.
Click on the file and then click on download button on the right or copy paste the code into a .txt and put it inside the "pxshader" folder of potplayer root.

VideoHelp forum link for discussion : https://forum.videohelp.com/threads/414563-PotPlayer-shader-Smart-Vibrance

UPDATE (V2 released): 
This shader was initially created and tuned for FullHD's video but after further testing i've noticed that in some HD/SD anime (and probably some movies) the detection of grayscale was abrupt and caused artifacts, i've updated the blending of the saturation scaling factor for grayscales in order to maintain smooth shades transition.

Preview:
![alt text](https://github.com/aston89/Smart-Vibrance-for-PotPlayer/blob/main/smart_vibrance_preview.jpg)


Difference from the original and the V2 update:
![alt text](https://github.com/aston89/Smart-Vibrance-for-PotPlayer/blob/main/smart_vibrance_v2_preview.jpg)


