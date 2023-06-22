﻿# SD Webui Vectorscope CC
This is an Extension for the [Automatic1111 Webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui), which performs *some kind of* **Offset Noise**[*](#actual-offset-noise-tldr) natively.

**Note:** This Extension is the result of my numerous trial and error[*](#what-is-actually-under-the-hood). I have little idea on how or why this works. 💀

## How to Use
After installing this Extension, you will a new section in both **txt2img** and **img2img** tabs. 
Refer to the parameters and sample images below, and play around with the values.

**Note:** Since this modifies the underlying latent noise, the composition may change drastically.

**Note:** Due to some scaling issues, low steps *(`< 10`)* makes the effects stronger 

#### Parameters
- **Enable:** Turn on & off this Extension
- **Alt:** Modify an alternative Tensor. The effects are significantly stronger when this is **on**.
- **Brightness:** Adjust the overall brightness of the image
  - **Important:** Values too **high** cause the image to be blurry; Values too **low** cause extra noises to appear.
- **Contrast:** Adjust the overall contrast of the image
- **Saturation:** Adjust the overall saturation of the image
- **R:** Cyan <---> Red
- **G:** Magenta <---> Green
- **B:** Yellow <---> Blue
- **Skip:** Only process the first few steps and skip the rest, to combat the issues mentioned in **Brightness**

<p align="center"><img src="samples/Skip.jpg" width=384></p>

> Notice the noises on the faces

<h5><u>Advanced</u></h5>

- **Process Hires. fix:** By default, this Extension only functions during the **txt2img** phase, so that **Hires. fix** can *fix* the noises introduced during **txt2img**. Enable this to process all the way.
  - **Note:** This option does not affect **img2img**
  - **Important:** Since I scale the effect by the `steps`, make sure your **txt2img** has higher `steps` than **Hires. fix** if you enable this
- **Noise Settings:** Currently does **nothing** *(To be implemented)*

## Sample Images
- **Checkpoint:** [UHD-23](https://civitai.com/models/22371/uhd-23)
- **Pos. Prompt:** `(masterpiece, best quality), 1girl, solo, night, street, city, neon_lights`
- **Neg. Prompt:** `(low quality, worst quality:1.2)`, [`EasyNegative`](https://huggingface.co/datasets/gsdf/EasyNegative/tree/main), [`EasyNegativeV2`](https://huggingface.co/gsdf/Counterfeit-V3.0/tree/main/embedding)
- `Euler a`; `20 steps`; `7.5 CFG`; `Hires. fix`; `Latent (nearest)`; `16 H.steps`; `0.6 D.Str.`; `Seed:`**`3814649974`**
- No offset noise Checkpoints/LoRAs were used

<p align="center"><b>Base</b><br>
Extension <code>Disabled</code><br>
<img src="samples/00.png" width=512></p>

<p align="center"><b>Dark</b><br>
<b>Brightness:</b><code>-2</code>; <b>Contrast:</b><code>1</code><br>
<img src="samples/01.png" width=512></p>

<p align="center"><b>Bright</b><br>
<b>Brightness:</b><code>1</code>; <b>Contrast:</b><code>-0.5</code>; <b>Alt:</b><code>Enabled</code><br>
<img src="samples/02.png" width=512></p>

<p align="center"><b>Chill</b><br>
<b>Brightness:</b><code>-2.5</code>; <b>Contrast:</b><code>1.5</code><br>
<b>R:</b><code>-0.5</code>; <b>B:</b><code>1</code><br>
<img src="samples/03.png" width=512></p>

<p align="center"><b><s>Mexican Movie</s></b><br>
<b>Brightness:</b><code>3</code>; <b>Contrast:</b><code>-1.5</code>; <b>Saturation:</b><code>1</code><br>
<b>R:</b><code>1</code>; <b>G:</b><code>0.5</code>; <b>B:</b><code>-2</code><br>
<img src="samples/04.png" width=512></p>

> Notice the significant differences even when using the same seed!

## Roadmap
- [X] Extension Released
- [X] Add Support for **X/Y/Z Plot**
    - I didn't add a value cap, you can experiment with some really wild values with this
- [ ] Add Support for **Inpaint**
- [ ] Append Parameters onto Metadata

## Known Issues
- Does not work with `DDIM` sampler
- Has little effect when used with certain **LoRAs**

<hr>

#### (Actual) Offset Noise TL;DR
It was [discovered](https://www.crosslabs.org/blog/diffusion-with-offset-noise) that, the noise functions used during training were flawed, causing `Stable Diffusion` to always generate images with an average brightness of `0.5`.

> **ie.** Even if you prompt for dark/night or bright/snow, the overall image still looks "grey" on average

> [Technical Explanations](https://youtu.be/cVxQmbf3q7Q)

<hr>

#### What is Actually Under the Hood
After reading through and messing around with the code, 
I found out that it is possible to modify the Tensors 
representing the latent noise used by the Stable Diffusion process.

The dimentions of the Tensors is `(1, 4, 64, 64)`, which can be thought of like this:

> **1** noise image, with **4** channels, each with **64x64** values

So I tried to play around with the values of each channel, 
and ended up discovering these relationships between channels and colors.
Thus, I wrote it into an Extension.

*~~And why does it work this way? IDK~~*

<hr>

#### Vectorscope?
The Extension is named this way because the color interactions remind me of the `Vectorscope` in **Premiere Pro** 's **Lumetri Color**.
Those who are experienced in Color Correction should be rather familiar with this Extension.

<p align="center"><img src="samples/Vectorscope.png" width=256></p>

<sup>~~Yes. I'm aware that it's just how **RGB** works in general.~~</sup>