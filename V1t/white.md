# Question

### Description
The challenge provided a seemingly blank image named `white.png` with the hint "The image is blank, is it?"

## Solution
Since the image looked completely white, I decided to take a closer look at its pixels to see if anything was hidden. Using Python and Pillow, I pulled out the color data and boosted small differences to make them visible. I also checked the alpha (transparency) layer to see if it revealed anything.

```python
from PIL import Image
import numpy as np

im = Image.open("white.png").convert("RGBA")
pixels = np.array(im)

enhanced = ((pixels[:, :, :3].astype(float) / pixels[:, :, :3].max()) * 255).astype(np.uint8)
diff = np.abs(pixels[:, :, :3].astype(int) - pixels[:, :, :3].mean(axis=(0, 1)).astype(int))
Image.fromarray((diff * 10).clip(0, 255).astype(np.uint8)).save("revealed_diff.png")

alpha = Image.fromarray(pixels[:, :, 3])
alpha.save("alpha_layer.png")
