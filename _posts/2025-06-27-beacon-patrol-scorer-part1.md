---
layout: post
title: "Building a Beacon Patrol Scorer pt. 1: Basic setup with flask"
date: 2025-06-25
categories: [Web Development, Beacon Patrol Scorer]
tags: [side-project, learning, python, flask, computer-vision, accessibility, boardgames, solo-gaming, image-processing, testing, pytest]
image:
  path: assets/images/beacon_patrol/boat_closeup.jpg
  alt: "Beacon Patrol board game setup showing close up of boat meeple on tiles"
---

I really enjoy playing boardgames, and don't have any local friends to play with (yet?!), so play a lot of solo games. One of my favourites is a tile laying game called [Beacon Patrol](https://beaconpatrol.com/). With a lot of tile laying games (Carcassonne is a great example), you have quite a lot of freedom about where you place tiles - you can turn them around, you aren't physically limited. With Beacon Patrol, the idea is that you are a coast guard, exploring the ocean. So you can only place a tile next to where your little boat is, to mimic you travelling around. And you are on a boat, so you can only travel through the watery parts of the board, and have a limit to how far you can travel.

While I love playing this game, the end of game scoring is a bit annoying. You have to check every tile that you've laid, and see if it's been fully "explored" - that there is a tile touching all four sides.
If so, the score depends on the tile:
- Nothing = 1 point
- Beacon buoy = 2 points
- Lighthouse = 3 points

In this example you would score 3 points - the only tile surrounded is on with a Lighthouse on it.

![Beacon Patrol game board with one highlighted tile showing lighthouse scoring](assets/images/beacon_patrol/bp_highlighted1.png)

The scoring is straightforward enough, but having to check every single tile, and keep a running total in your head, just requires that bit more concentration that makes it annoying.

But wait. I'm a developer! Surely I can build an app for that... 

## The plan
I want to build an app where you can upload a photo of a finished game of Beacon Patrol, and receive a score. I plan to build it in Python, as it has some good photo analysing tools. I'm going to try building it in Flask, as I believe that's similar to Rails and I'm curious about the differences. I will probably just stick to the basic scoring for now, and ignore the little expansions.

### Requirements
- You can upload a photo of a finished game using a simple web interface
- It can recognise whether the tiles have been legally placed
	- Orientation arrows are all in the same direction
	- The land and sea are all correctly aligned according to the rules
- It will return a correct score according to the scoring rules
- It will return the "rank" based on the score according to the rules
- It will return a visual signifier of what tiles have been scored (I think this might be a stretch goal)

With this in mind, this is the development plan:

### Phase 1: Basic Web App
- Simple upload form
- Basic image processing to validate it's a reasonable board photo
- Just return a score number
- Get the web framework working

### Phase 2: Full Computer Vision
- Show the uploaded image back to user
- Automatic tile detection and classification
- Orientation arrow validation
- Full automated scoring

### Phase 3: Polish and deploy
- Styling
- Deployment

I've decided to use Flask, as I want to compare that to my Rails experience. I've been persuaded by Josh Comeau's [view](https://www.smashingmagazine.com/2022/05/you-dont-need-ui-framework/) that vanilla CSS is actually pretty powerful these days, and will make my app lighter.

## Phase 1 - introduction to Flask
### First things first - indentation

Python uses 4 spaces for indentation rather than the 2 that I know and love. I'm not quite ready to make this a universal change in my VSCode, but it turns out you can create a settings file especially for a project!

In `.vscode/settings.json` I add in some helpful Python specific settings: 

```json
{
	"python.defaultInterpreterPath": "./venv/bin/python",
	"editor.tabSize": 4,
	"editor.insertSpaces": true,
	"python.formatting.provider": "black",
	"[python]": {
		"editor.tabSize": 4,
		"editor.insertSpaces": true,
		"editor.detectIndentation": false
	}
}
```

### app.py - the file that does everything

Claude.AI has helpfully provided me with some code to get my initial Flask file set up. I've decided to type it rather than copy it, in the hopes of making sure I understand everything.

The individual lines mostly make sense, but I'm surprised they're all in the same place.

```python
from flask import Flask, render_template, request, redirect, url_for
import os
from werkzeug.utils import secure_filename
from PIL import Image

app = Flask(__name__)
app.config["UPLOAD_FOLDER"] = "uploads"
app.config["MAX_CONTENT_LENGTH"] = 16 * 1024 * 1024 # 16 MB max file size

# Ensure upload directory exists
os.makedirs(app.config["UPLOAD_FOLDER"], exist_ok=True)

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/upload", methods=["POST"])
def upload_file():
    if "file" not in request.files:
        return "No file selected", 400
    
    file = request.files["file"]
    if file.filename == "":
        return "No file selected", 400
    
    if file:
        filename = secure_filename(file.filename)
        filepath = os.path.join(app.config["UPLOAD_FOLDER"], filename)
        file.save(filepath)

        # Basic image validation
        try:
            with Image.open(filepath) as img:
                width, height = img.size
                # Placeholder score
                score = 42 # To be replaced later
            
            return f"Image uploaded! Size: {width}x{height}px. Your score: {score}"
        except Exception as e:
            return f"Error processing image: {str(e)}", 400

if __name__ == "__main__":
    app.run(debug=True)

```

We seem to be importing useful tools, setting up config, creating routes and actions, all in the same file. Interesting!

### Creating a simple form
Next step is to create a form for uploading an image. 

I upload this picture of my beautiful dog Bodhi, which I just happen to have easily to hand:

![A beagle cross' face with mouth open, looking like he's smiling](assets/images/beacon_patrol/b-dog.png)

And this is the response!

![Screenshot showing local development with text: Image uploaded! Size: 300x300px. Your score: 42](assets/images/beacon_patrol/flask_initial_upload.png)

Very exciting!

### Basic image processing

I want to do very basic quality control that the photo is of a reasonable size, so add a basic check that the width and height are larger than 200px and smaller than 4000px. I think now is the time to add some tests, so get pytest set up. 

The format of tests looks quite different to RSpec, but actually makes sense once I read them more, and I was able to add my own after Claude.AI's examples quite easily. Here's a few examples:

```python

@pytest.fixture
def client():
    """Create a test client for the Flask app"""
    app.config["TESTING"] = True
    app.config["UPLOAD_FOLDER"] = tempfile.mkdtemp()
    
    with app.test_client() as client:
        yield client

@pytest.fixture
def sample_image():
    """Create a sample image for testing"""
    img = Image.new("RGB", (800, 600), color="blue")
    img_bytes = io.BytesIO()
    img.save(img_bytes, format="JPEG")
    img_bytes.seek(0)
    return img_bytes

@pytest.fixture
def text_file():
    """Create a sample text file for testing"""
    text_content = io.BytesIO(b"Hello world, this is not an image!")
    return text_content

def test_index_page(client):
    """Test the home page loads"""
    response = client.get("/")
    assert response.status_code == 200
    assert b"Beacon Patrol Board Scorer" in response.data

def test_upload_valid_image(client, sample_image):
    """Test uploading a valid image"""
    response = client.post("/upload", data={
        "file": (sample_image, "test.jpg")
    })
    assert response.status_code == 200
    assert b"Valid board image!" in response.data
    assert b"Your score:" in response.data

def test_upload_non_image(client, text_file):
    """Test uploading a text file"""
    response = client.post("/upload", data={
        "file": (text_file, "test.txt")
    })
    assert response.status_code == 400
    assert b"Error: Not a valid image file" in response.data
```

## Phase 2 - displaying and evaluating images

### Displaying the image

The first step is to display the uploaded image back to the user. 
I add a `results.html` template

```html
<div class="results">
    <h2>Your Results</h2>
    <p><strong>Score:</strong> {{ score }} points</p>
    <p><strong>Rank:</strong> {{ rank }}</p>

    <h3>Your Board</h3>
    <img src="{{ url_for('uploaded_file', filename=filename) }}" alt="Uploaded Beacon Patrol board"
        style="max-width: 100%; height: auto;">

    <a href="{{ url_for('index') }}">Score Another Board</a>
</div>
```

And these are added to `app.py` (among some other explicit rendering of the `index.html`)
```python
return render_template("results.html", 
                                     filename=filename, 
                                     score=score, 
                                     rank=rank)
```

```python
@app.route("/uploads/<filename>")
def uploaded_file(filename):
    return send_from_directory(app.config["UPLOAD_FOLDER"], filename)
```

I'm confused about how we get from `route("/uploads/<filename>")` to rendering the results template, as it doesn't follow the same pattern as `render_template("results.html"...`

Claude.AI explains that the magic lies within the `url_for` function. 

The key insight is that these are actually **two separate requests**:
1. POST to `/upload` → renders `results.html` template 
2. GET to `/uploads/filename.jpg` → serves the actual image file

It generates a URL based on what is passed in. `url_for('uploaded_file', filename=filename)` will generate a URL, and then Flask is able to match that URL to the uploads route that we defined.

- **Route definition**: `@app.route("/uploads/<filename>")`
- **Function name**: `uploaded_file` (this is what `url_for` references)
- **Template usage**: `url_for('uploaded_file', filename=filename)`
- **File serving**: `send_from_directory(app.config["UPLOAD_FOLDER"], filename)`

Pretty clever! (Once you know what's going on).

### Testing lessons
When I updated to template-based responses, my tests broke - they were looking for plain text responses but now getting full HTML. I decided to take a more pragmatic approach and test the behavior (correct status codes and meaningful page content) rather than exact error messages.

My tests changed from something like this: 

```python
assert response.status_code == 400
assert b"Error: Not a valid image file" in response.data
```

To this:

```python
assert response.status_code == 400
assert b"Upload a photo of your game board" in response.data
```

I made sure that the errors would render on the index.html page.

When I went to test it manually to check that they were showing, I discovered that the browser was doing a lot of the heavy lifting for me - I couldn't select any files from my computer that weren't in the correct file format, and when I tried to just not select a file, I got a nice browser error. Thank you browser form validation (`required`, `accept="image/*"`)!

I could still check for the sizing error though, and received the expected error message.

### Evaluating the image
This is where things start to get more complicated! We want to evaluate the image, and as a first step, reject any images that are obviously not Beacon Patrol tiles.

It makes sense to start with colour analysis as Beacon Patrol has a clear colour palette: 
- Dominant blue (water)
- Red (lighthouses)
- White (land, waves, lighthouse stripes)

Steps: 
- Convert the image to a format that's easy to analyse (like Hue Saturation Value color space)
- Count pixels in certain colour ranges
- Check if the ratios match what you'd expect from a Beacon Patrol board

Test Driven Development feels like a good way to develop this, with fake generated images to check things work as expected and then can progress to real photos. Here are some tests to start:

```python
def test_valid_beacon_patrol_colors():
    # Image with blue, white, some red
    # Should return True or high confidence score

def test_invalid_colors_mostly_green():
    # Image that's predominantly green
    # Should return False or low confidence

def test_invalid_colors_no_blue():
    # Image with no blue at all
    # Should return False

def test_grayscale_image():
    # Black and white photo
    # Should return False
```

Let's start with a really basic one: 

```python
@pytest.fixture
def blue_dominant_image():
    """Create an image that's mostly blue (like Beacon Patrol water)"""
    img = Image.new("RGB", (800, 600), color=(135, 206, 235))
    img_bytes = io.BytesIO()
    img.save(img_bytes, format="JPEG")
    img_bytes.seek(0)
    return img_bytes

def test_predominantly_blue_image_is_valid_board(blue_dominant_image):
    """Test result from a predominantly blue image"""
    response = analyze_board_colors(blue_dominant_image)
    assert response == True
```

To try to get this to work, I start experimenting with how to convert the image_data into information to process the colour: 

```python
from PIL import Image

def analyze_board_colors(image_data):
    image_data.seek(0)
    img = Image.open(image_data)

    img = img.convert("RGB")

    print(img.width, img.height)
```

I run the test again, and see: 

```python
-------------------------- Captured stdout call -------------------------------
800 600

```

Hooray! I have an image I can work with.

So now to look at the colours of the pixels: 

```python
pixels = list(img.getdata()) # Returns list of (R,G,B) tuples

sampled_pixels = pixels[::50]  # Every 50th pixel
print(f"Sampled {len(sampled_pixels)} pixels")
print(f"First few pixels: {sampled_pixels[:5]}")
```

A tuple is a data structure that holds multiple values in a specific order - a bit like an array, but immutable. Not just a great [pairing tool](https://tuple.app) (which is what I'm more familiar with!).

Test result: 

```python
-------------------------- Captured stdout call --------------------------------
Sampled 9600 pixels
First few pixels: [(135, 206, 234), (135, 206, 234), (135, 206, 234), (135, 206, 234), (135, 206, 234)]
```

Very cool! The difference in the blue 235 --> 234 is likely to do with JPEG compression.

Now let's get down to business and analyse for the colour blue: 

```python
def is_blue(pixel):
    r, g, b = pixel

    return b > r and b > g and b > 100
```

The first line is called "tuple unpacking" and basically assigns each element in the tuple to the respective variables.

I then check that blue is the predominant colour - and have a passing test when it's applied within the main function! I chose 50% because the main colour of the photo should be blue - any Beacon Patrol board will have a lot of water. But some of the individual tiles will have a lot of white land on them. This proportion may need revisiting once I start testing with real board photos.

```python
def analyze_board_colors(image_data):
    image_data.seek(0)
    img = Image.open(image_data)

    img = img.convert("RGB")
    pixels = list(img.getdata())

    sampled_pixels = pixels[::50]  # Every 50th pixel

    blue_count = 0
    for pixel in sampled_pixels:
        if is_blue(pixel):
            blue_count += 1

    blue_percentage = blue_count / len(sampled_pixels)
    print(f"Blue percentage: {blue_percentage:.2f}")

    print(f"Sampled {len(sampled_pixels)} pixels")
    print(f"First few pixels: {sampled_pixels[:5]}")

    return blue_percentage > 0.5  # 50% threshold
```

I was able to then write further tests to check that the other types of image would return false if there isn't enough blue on them. 

Time to apply it within the code.

### Rejecting an image that isn't blue enough

I could now use the board_analyzer code within the `app.py`. This code already exists:

```python
with Image.open(filepath) as img:
	width, height = img.size
	
	if width < 200 or height < 200:
		return render_template("index.html", error="Image too small"), 400
	if width > 4000 or height > 4000:
		return render_template("index.html", error="Image too large"), 400
```

This was the wrong format for using the `analyze_board_colors` function as is - that takes BytesIO image data, but `img` here is a PIL image. I decide to just update function to be able to take this type as well, rather than do any further processing on the image.

This is the kind of thing that I'm grateful to have Claude.AI's help on as I would have probably been going round in circles trying to understand why it wasn't working!

Updated code: 

```python
# If it's BytesIO, convert to PIL Image
if hasattr(image_data, 'read'):
	image_data.seek(0)
	img = Image.open(image_data)
else:
	# It's already a PIL Image
	img = image_data
```

I try it out locally, with my lovely picture of Bodhi - it works!

![Screenshot of local development showing: Error: This does not look like a Beacon Patrol game. Please upload a different photo.](assets/images/beacon_patrol/blue_error.png)

This will act as a good rough gatekeeper to prevent pictures that are obviously not of Beacon Patrol from being analysed.

I'm feeling good about the progress made:

**Phase 1: Basic Web App**
* ✅ Simple upload form
* ✅ Basic image processing to validate it's a reasonable board photo
* ✅ Just return a score number
* ✅ Get the web framework working

**Phase 2: Full Computer Vision**
* ✅ Show the uploaded image back to user
* Automatic tile detection and classification
* Orientation arrow validation
* Full automated scoring

**Phase 3: Polish and deploy**
* Styling
* Deployment

Next step will be to analyse the pictures themselves and recognise the tiles.
