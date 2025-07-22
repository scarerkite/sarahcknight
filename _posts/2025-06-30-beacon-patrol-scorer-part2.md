---
layout: post
title: "Building a Beacon Patrol Scorer pt. 2: Identifying tile orientation with Computer Vision"
date: 2025-06-30
categories: [Web Development, Beacon Patrol Scorer]
tags: [side-project, learning, python, flask, computer-vision, accessibility, boardgames, solo-gaming, image-processing]
---

See [part 1]({% post_url 2025-06-27-beacon-patrol-scorer-part1 %}) for how I set up Flask and got a basic image assessment running. 

I have a basic web app that can assess an uploaded image, and accept or reject it, based on whether it's blue enough to be a Beacon Patrol game board (assembled of multiple tiles). The next step is to be able to analyse a photo of tiles, and identify each individual tile. This will allow us to assess whether a tile counts as "explored".

In this example, only the highlighted tile will score any points because it is surrounded by other tiles on all 4 sides.

![Beacon Patrol game board with one highlighted tile showing lighthouse scoring](assets/images/beacon_patrol/bp_highlighted1.png)

However, the first step is to just able to identify the tiles.

I download Python OpenCV for this, and look for tutorials on the internet.

```bash
pip3 install opencv-python
```

## Identifying tile boundaries
Initially I tried following a tutorial to look for the boundaries of basic shapes.

```python
def identify_tiles():
    image = cv2.imread("test_images/valid_boards/simple_game1.png")
    gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    
    _, thresh_image = cv2.threshold(gray_image, 220, 255, cv2.THRESH_BINARY)

    contours, hierarchy = cv2.findContours(thresh_image, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)

    for i, contour in enumerate(contours):
        if i == 0:
            continue
        
        epsilon = 0.01*cv2.arcLength(contour, True)
        approx = cv2.approxPolyDP(contour, epsilon, True)

        if len(approx) == 4:
            cv2.drawContours(image, contour, 0, (0, 0, 0), 4)

    cv2.imshow("window", image)
    cv2.waitKey(0)
    cv2.destroyAllWindows()

if __name__ == "__main__":
    identify_tiles()

```

However, that didn't really work. Here you can see the result - there are little green boundary dots all over.

![Beacon Patrol game board with lots of small green dots highlighting waves, buoys, grass](assets/images/beacon_patrol/bp_boundary_attempt1.png)

But none on the corners of the tiles.

Next, I try a colour based approach - can we find all the blue tiles instead?

## Identifying blue tiles

```python
def identify_blue_tiles():
    image = cv2.imread("test_images/valid_boards/simple_game1.png")

    lower_blue = np.array([95, 100, 150])
    upper_blue = np.array([115, 255, 255])

    hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
    mask = cv2.inRange(hsv, lower_blue, upper_blue)

    contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    for contour in contours:
        cv2.drawContours(image, [contour], -1, (0, 255, 0), 8)

    cv2.imshow("Blue Tiles", image)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
```
We have a "blue range" with the shades a bit lighter and a bit darker than the photo example blue.

`hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)` - converts the colour image from BGR (the PIL default) to HSV. This is better for colour detection as it can separate the colour (hue) from brightness and saturation.

`mask = cv2.inRange(hsv, lower_blue, upper_blue)` - creates a binary image called a "mask". Every pixel is checked whether the colour falls within the blue range: 
if YES --> the pixel becomes white in the mask
if NO --> the pixel becomes black in the mask

`contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)` - this then finds the outlines (contours) of all the white blobs in the mask.

- `RETR_EXTERNAL` means "only find the outer boundaries, ignore holes inside shapes"
- `CHAIN_APPROX_SIMPLE` means "store only the corner points of straight lines"
- The `_` ignores the hierarchy info (we don't need it)

The result looks a lot more promising, but the white areas are a bit of an issue. Maybe to identify the tiles we can work with approx size?

![Beacon Patrol game board with green highlighting the borders of some tiles, but running along the coastlines of others](assets/images/beacon_patrol/bp_boundary_attempt2_blue.png)

I add some bounding boxes which says "get the smallest rectangle that contains the whole contour".

Result - so close!

![Beacon Patrol game board with green highlighting around the borders of all tiles except between 2](assets/images/beacon_patrol/bp_boundary_merged.png)

Claude.AI suggests a "morphological" operation that is like digital "erosion" and "dilation". I had no idea what this was, but Claude.AI explained it very well: 

> ## The Two Basic Operations
> 
> ### Erosion - "shrinks" white areas:
> 
> - Takes away pixels from the edges of white regions
> - Like waves eating away at a beach
> - Small white areas might disappear completely
> - Larger areas get smaller
> 
> ### Dilation - "grows" white areas:
> 
> - Adds pixels to the edges of white regions
> - Like a flood expanding outward
> - Makes white areas bigger
> - Can fill in small gaps
> 
> ## Opening = Erosion followed by Dilation:
> 
> 1. **First**: Erode to break apart touching regions and remove small noise
> 2. **Then**: Dilate to restore the remaining regions to roughly their original size
> 
> ## For Your Tile Problem:
> 
> - Your blue tiles are probably connected by thin bridges of blue pixels
> - **Erosion** would break those thin connections, separating the tiles
> - **Dilation** would grow each separated region back to normal tile size
> - Result: separate tile regions instead of one big merged region
> 
> ## Kernel Size Controls Aggressiveness:
> 
> - `(3,3)` = gentle separation
> - `(5,5)` = more aggressive separation
> - `(7,7)` = very aggressive (might break apart things you want to keep together)

Well. Claude.AI was right. It solved the issue!

![Beacon Patrol game board with green highlighting around the borders of all tiles](assets/images/beacon_patrol/bp_boundary_success.png)

```python
def identify_blue_tiles():
    image = cv2.imread("test_images/valid_boards/simple_game1.png")

    lower_blue = np.array([95, 100, 150])
    upper_blue = np.array([115, 255, 255])

    hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
    mask = cv2.inRange(hsv, lower_blue, upper_blue)

    # Morphological opening to separate touching blue regions
    # Temporarily takes away pixels to remove noise and find edges
    # Grows them again so the final result is roughly the right size
    kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3))
    mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN, kernel)

    contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    for contour in contours:
        x, y, w, h = cv2.boundingRect(contour)
        cv2.rectangle(image, (x, y), (x+w, y+h), (0, 255, 0), 8)

    cv2.imshow("Blue Tiles", image)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
```

However, when I try this approach with a more complex board, with more white tiles, it struggles. I play around with different values, but it doesn't make a difference.
I don't think this is going to work - time to try a different approach.

I noticed during some experimentations, when viewing in greyscale that the template arrows show up quite clearly.

![Beacon Patrol game board in greyscale showing lines of the table and outlines of pictures on tiles on the board](assets/images/beacon_patrol/bp_greyscale.png)

Maybe we could use those?

## Template matching

One of the checks I want to do is that the orientation arrows are all pointing the same way - if not, it's an invalid board and won't get a score. These arrows might be an easier way to work out where the tiles are. If the arrows are pointing in different directions, then the board can be discarded anyway. If they're all pointing in the same direction, then we should be able to work out where the tiles are based on the location of the arrows.

I create a template by screenshotting one of the small orientation arrows. The program can now scan the image, looking for anything that matches the template. I am experimenting with different photos, and discover that the template has to match the same direction as in the photo. I also have to experiment with the threshold - too high and it misses some of the arrows. To low, and it starts picking up rocks. 

However, I get there in the end. 

```python
def find_arrows_with_template():
    image = cv2.imread("test_images/valid_boards/14_tiles_arrows_right_white.png")
    template = cv2.imread("images/templates/arrow_on_white_90.png", cv2.IMREAD_GRAYSCALE)
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    
    # Template matching
    result = cv2.matchTemplate(gray, template, cv2.TM_CCOEFF_NORMED)
    
    # Find locations where match is above threshold
    threshold = 0.65
    locations = np.where(result >= threshold)
    
    # Draw rectangles around matches
    template_h, template_w = template.shape
    for pt in zip(*locations[::-1]):
        cv2.rectangle(image, pt, (pt[0] + template_w, pt[1] + template_h), (0, 255, 0), 2)
    
    cv2.imshow("Matches", image)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
    
    return len(locations[0])
```

![Beacon Patrol game board at 90 degrees with green borders highlighting all orientation arrows](assets/images/beacon_patrol/orientation_arrows_right.png)

I'm hoping to be able to analyse a photo with arrows in any direction. However, this means trying to find out which way the majority of arrows are facing to pick up the "correct" direction, and reject others. I do a lot of experimentation with different arrow templates, and different images.

In the end, I decide to simplify things. If we know which direction the arrows _should_ be pointing in to start with, then it's a lot easier to find arrows pointing in the wrong direction. And not mistake rocks or houses for arrows.

So the app will require photos to be uploaded with all arrows pointing up.

## Checking arrow orientation

I test it with a basic image, now that we have clearer expectations.

![Beacon Patrol game board at 90 degrees with green borders highlighting all but 2 orientation arrows](assets/images/beacon_patrol/arrow_template_result1.png)

It misses the 2 either side. 

I create templates of those 2 specific arrows. 
Same result. 

I feel fairly sure that these should show up. We create a test to try the different templates (images of the different arrows including some of the surrounding areas) at different thresholds.

```python
def test_single_template(image_path, template_path, threshold=0.6):
    """Test a single template at various thresholds"""
    image = cv2.imread(image_path)
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    template = cv2.imread(template_path, cv2.IMREAD_GRAYSCALE)
    
    for thresh in [0.5, 0.6, 0.65, 0.7, 0.75]:
        result = cv2.matchTemplate(gray, template, cv2.TM_CCOEFF_NORMED)
        locations = np.where(result >= thresh)
        count = remove_duplicate_detections(locations, min_distance=20)
        print(f"Threshold {thresh}: {count} matches")

def test_all_templates(image_path):
    """Test all templates individually"""
    template_paths = [
        "images/templates/arrow_blue.png", 
        "images/templates/arrow_mixed1.png",
        "images/templates/arrow_mixed2.png",
        "images/templates/arrow_mixed3.png",
        "images/templates/arrow_mixed4.png",
        "images/templates/arrow_terrain.png",
        "images/templates/plain_arrow.png"
    ]
    
    for template_path in template_paths:
        print(f"\n--- Testing {template_path} ---")
        test_single_template(image_path, template_path)
```

These are the results: 

- `arrow_blue.png`: Works well at 0.5-0.75 (finds 4 arrows consistently)
- `arrow_mixed1.png`: Only works at 0.5 (finds 5 arrows)
- `arrow_mixed2.png`: Works at 0.5-0.7 (finds 1-6 arrows)
- `arrow_mixed3.png`: Works consistently at all thresholds (finds 1 arrow)
- `arrow_mixed4.png`: Works at 0.5-0.75 (finds 1-5 arrows)
- `plain_arrow.png`: Too many false positives at low thresholds

I applied these rules to code, but it was then seeing arrows where there weren't any, and missing others that were there. 

I added colour coding to the visual output, to see which templates were working and which weren't.

![Beacon Patrol game board with different coloured boxes that are numbered. Some correctly highlight arrows, 2 incorrectly highlight other parts of the board and table](assets/images/beacon_patrol/arrow_id_with_colours.png)

Here we can see that Template 5 (in pink) is catching the most arrows, and 1 (cut off on the top left) and 2 are returning false positives. Template 3 is catching a unique arrow. 

It looks like we can probably lose templates 1, 2 and 4 and just stick with 3 and 5. I try lowering the threshold, but it still can't find the bottom arrow. 

I create a template of just that arrow, and it works!

![Beacon Patrol game board with different coloured boxes that are numbered. They correctly highlight all arrows - some arrows are highlighted by multiple boxes](assets/images/beacon_patrol/arrow_id_success.png)

Time to check it against the other images.

It still wasn't working 100%. I decided to create a template that was cropped as close the arrow as possible, to try to only pull out the black shape of the arrow.

When I tried this one, it worked really well, and I realised that it was finding all of the arrows - the other templates were redundant! So those could be scrapped, and I'd just stick to this. one. 

Testing it against invalid boards, I realised that it can find the correct arrows, but will just ignore the other ones. So there's no way of knowing if the board is valid or not. However, now that the board is always facing the same direction, I should be able to recreate the rotated templates again, and it should manage to find other arrows rotated at 90 degree intervals.

It works!

![Beacon Patrol game board with green and red boxes. They correctly highlight all arrows - 2 arrows pointing in different directions are highlighted in a red box with with the word 'bad'. The others are highlighted in green and say 'ok'](assets/images/beacon_patrol/good_bad_orientation.png)

Now that arrow recognition is working consistently, I want to incorporate it into the web app, and restructure that so that the analysis returns consistent results. That way the app only has to respond to the same format of data, and the logic is being done in the `board_analyzer`.

Everything was moved over, and I tried testing the app locally with a picture of the beacon patrol board - it failed the blue test! I experimented, and have lowered the threshold of "blue" to 15% down from 50% - often there will be some table edge, and the game itself has a significant amount of white in as well. I used a real photo in the tests rather than programmatically generating an image.

The next step will be identifying scorable tiles (those adjacent on all sides), and calculating a score correctly based on what is on each tile.