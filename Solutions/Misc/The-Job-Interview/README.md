## Solution:

We recieve a website document that is likely asking us to fetch the data and draw on a plane using the coordinates, use requests and Beutifulsoup to
get and clean the data, and create a blank image to draw:
```
import requests
from bs4 import BeautifulSoup
from PIL import Image

l = "https://docs.google.com/document/d/e/2PACX-1vRW7X8yMO9cM-b6Ao3FbiZysF3MIjARoeO73z0PlG8O_yeM8xxWAWzt9hdoavlh3HR1IOEwWtJFpczI/pub"

html = requests.get(l).text

soup = BeautifulSoup(html, "html.parser")
rows = soup.select("table tr")[1:]

pixels = []

for row in rows:
    cols = row.find_all("td")
    if len(cols) < 3:
        continue

    try:
        x = int(cols[0].text.strip())
        char = cols[1].text.strip()
        y = int(cols[2].text.strip())
        pixels.append((x, y, char))
    except:
        continue

padding = 5
min_x = min(p[0] for p in pixels) - padding
max_x = max(p[0] for p in pixels) + padding
min_y = min(p[1] for p in pixels) - padding
max_y = max(p[1] for p in pixels) + padding

width = max_x - min_x
height = max_y - min_y

img = Image.new("RGB", (width, height), "orange")
pixels_img = img.load()

for x, y, c in pixels:
    px = x - min_x
    py = y - min_y

    if c == "█":
        pixels_img[px, py] = (0, 0, 0)
    else:
        pixels_img[px, py] = (0, 0, 255)


img.save("out.png")

```

Running it gives the image that we wanted:
![output](The-Job-Interview_1.png)

Answer:
```
PECAN{Y0U_G07_7H3_J08}
```
