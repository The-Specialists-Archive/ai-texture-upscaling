## Automated Detail Texture Creation Guide

### Step #1 - Download chaiNNer

chaiNNer is a node-based image processing GUI that we'll use to upscale and generate normal maps to be used for detail textures.

[![chaiNNer Releases](https://github.com/The-Specialists-Archive/ai-texture-upscaling/blob/main/Guide_Images/banner_chainner.png?raw=true)](https://github.com/chaiNNer-org/chaiNNer/releases)

Clone this repo and download the latest version of chaiNNer.

- Download this repo as a zip
- Download latest version of chaiNNer

### Step #2 - Open Template

Next we'll get chaiNNer ready for processing our textures, which we don't have just yet.

**Note: Download all dependencies that it requires upon opening the template file!*

- Open up `chaiNNer.exe`
- File > Open > `Half-Life_BatchUpscaler.chn`

Here you will see the flow for batch processing all of the textures we're going to prepare in the next step.

### Step #3 - Exporting WAD Textures

For this method I used J.A.C.K.'s built-in texture exporter to generate a full WAD of any BSP, this will gather all used textures into a single WAD making it easier to extract from. There may be other programs that can do this

- Download [J.A.C.K.](https://jack.hlfx.ru/en/download.html) to utilize their texture extraction tool

![jack_guide](https://github.com/The-Specialists-Archive/ai-texture-upscaling/blob/main/Guide_Images/jack_guide.png?raw=true)

- Use **Wally** that's included in this repo to open up your newly created WAD
- Select/highlight all of the textures in Wally

![wally1_guide](https://github.com/The-Specialists-Archive/ai-texture-upscaling/blob/main/Guide_Images/wally1_guide.png?raw=true)

- Package > Export Selected

![wally2_guide](https://github.com/The-Specialists-Archive/ai-texture-upscaling/blob/main/Guide_Images/wally2_guide.png?raw=true)

These are the raw textures that we'll use with **chaiNNer**

### Step #4 - Generation Time

Now back to chaiNNer.

- On the first node `Load Images` select the directory of your exported textures 
- On the `Load Model` select `4xLSDIRplus.pth` in our included `chaiNNer_Files`
- In the very last node `Save Image` select the directory for the output, separate from your original texture directory

With those done, hit the green **Run** button or press `F5` and it will quickly generate for every texture.
We're not quite done yet however, some textures may appear inverted such as brick or tile, to fix this:
- Find the `Normal Map Generator` node, on the `Invert` section, select `G` (This flips the green channel of the normal map)
- Back to the last node `Save Image`, in the `Subdirectory Path` enter: `Inverted`
- **Run** the generation again

### Step #5 - Detail Text File

This part is the more tedious section where each texture requires a line in a text file with their associated file name, thankfully we have more AI to use.

- Go to the directory of your original texture files
- Copy the directory path and open `Command Prompt`
- Use `cd <paste path>` to go to the directory
- Enter `for /f "tokens=1 delims=." %i in ('dir /b /a-d') do @echo %i >> texturenames.txt`
- Open up `texturenames.txt` and copy the full output

Next we will use ChatGPT or similar to format the list.
Below is an example snippet from `FiskCity_XMAS_detail.txt` and how we will want to format it.

```
// [texture name]       [detail texture name]            [hscale] [vscale]

{LilItaly-Romeo		 detail/mapname/LilItaly-Romeo	  1       1
{pkf_shopgrate1		 detail/mapname/pkf_shopgrate1	  1       1
-0silo2_p1               detail/mapname/-0silo2_p1        1       1
-1silo2_p1               detail/mapname/-1silo2_p1        1       1
```

With your texture names copied, prompt ChatGPT with your full list and how it should be formatted for example:
**Note: Before you do this be sure to replace `mapname` in the subdirectory with the map that you're upgrading.*
```
Dearest ChatGPT, please take this list:

{texture1
+abuttonlight-red
+0buttonlight-red
texture4

And format it like this list, where any name starting with { remove in the second column:
// [texture name]       [detail texture name]            [hscale] [vscale]

{texture1		detail/mapname/texture1	          1       1
-texture2		detail/mapname/-texture2	      1       1
+texture3               detail/mapname/+texture3          1       1
texture4                detail/mapname/texture4           1       1
```

This should output correctly, textures with `{` which are for masked textures can't be read from as is in-game, so be sure to remove the `{` of the actual name of the detail texture file.

### Step #6 - Final Step

Lastly if you haven't already, put all of your detail texture files into your game/mod directory, for example The Specialists:
`..\steamapps\common\Half-Life\ts\gfx\detail\mapname\`

- Copy the output from ChatGPT to a new text file called `mapname_detail.txt`
- Place in `steamapps\common\Half-Life\ts\maps\` or use `ts_downloads\maps\`

### Step #7 - Profit
Open up your game and load up the map, make sure you have `r_detailtextures 1` and for a quick bind to see before and after:
```
alias +toggledetail "r_detailtextures 0";alias -toggledetail "r_detailtextures 1";bind i +toggledetail
```

### Final Takeaway
For any textures that are incorrectly embossed, use the inverted texture that you generated prior and replace it in your `gfx\detail\mapname\` directory, restart game.
To make changes to how sharp or powerful the details are, you'll want to mess with the `Normal Map Generator` settings in **chaiNNer** and regenerate them, again with and without inverting the `G` channel.

**Note: You can also add new ones while keeping the map open, just restart the level to see new additions, doesn't work for already loaded detail textures however.*

How this works is that detail textures at 50% gray are invisible, so with the template I've made it so only the bumps from the textures are visible, the beauty of detail textures also is that they can completely replace textures with full color at 512px in any map with no real cost or compiling, turn concrete walls into a jungle, or Mecklenburg into lego world, it's your choice. Just know that if someone already has a detail texture file in their maps folder, it can unfortunately only be overwritten manually.


 
###### Guide and template by @Dementei - Enjoy!
