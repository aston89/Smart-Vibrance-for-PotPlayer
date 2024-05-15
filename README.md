*Smart vibrance shader*

This PotPlayer shader is designed to enhance the vibrance of color in a selective manner, it works in this way :

1) Boost the saturation of every color if its below a certain threshold.
2) If the color have an amount of saturation already over the threshold, it will receive less and lesser boost until nothing will be boosted, this method prevent clipping, burning and glowing.
3) If the color is literally a grayscale (or near grayscale) the boost will also be decreased to preserve clarity and intelligibility on certain situation were the scene is dark and otherwise details will be lost.

This shader works beautifully with anime, especially if you don't already have other gamma, brightness, contrast and digital filter alterations applied to it, it works equivalently to RTX dynamic vibration but for video playback!

The shader is fully commented and configurable, there's should no need for further explanation, in case, just ask.
Click on the file and then click on download button on the right or copy paste the code into a .txt and put it inside the "pxshader" folder of potplayer root.
