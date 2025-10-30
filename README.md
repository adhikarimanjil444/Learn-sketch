<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Learn-Sketch</title>
<style>
  body {
    font-family: Arial, sans-serif;
    text-align: center;
    background: #f0f0f0;
    padding: 20px;
  }
  h1 {
    color: #333;
  }
  .upload-section {
    margin: 20px 0;
  }
  img {
    max-width: 90%;
    margin: 10px 0;
    border: 2px solid #ccc;
  }
  canvas {
    max-width: 90%;
    margin: 10px 0;
    border: 2px solid #333;
  }
  button {
    background: #4CAF50;
    color: white;
    padding: 10px 20px;
    border: none;
    cursor: pointer;
    font-size: 16px;
  }
  button:hover {
    background: #45a049;
  }
</style>
</head>
<body>

<h1>Learn-Sketch</h1>
<p>Upload your image to see it as a sketch!</p>

<div class="upload-section">
  <input type="file" id="imageUpload" accept="image/*">
</div>

<div>
  <h3>Original Image</h3>
  <img id="originalImage" src="" alt="Your Image Preview">
</div>

<div>
  <h3>Sketch Preview</h3>
  <canvas id="sketchCanvas"></canvas>
</div>

<button id="downloadBtn">Download Sketch</button>

<script>
const imageUpload = document.getElementById('imageUpload');
const originalImage = document.getElementById('originalImage');
const canvas = document.getElementById('sketchCanvas');
const ctx = canvas.getContext('2d');
const downloadBtn = document.getElementById('downloadBtn');

imageUpload.addEventListener('change', function(event){
    const file = event.target.files[0];
    if(!file) return;

    const reader = new FileReader();
    reader.onload = function(e){
        originalImage.src = e.target.result;
        originalImage.onload = function(){
            // Set canvas size same as image
            canvas.width = originalImage.width;
            canvas.height = originalImage.height;
            createSketch(originalImage);
        }
    }
    reader.readAsDataURL(file);
});

function createSketch(img){
    ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
    let imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
    let data = imageData.data;

    // Convert to grayscale
    for(let i=0; i<data.length; i+=4){
        let avg = (data[i] + data[i+1] + data[i+2]) / 3;
        data[i] = data[i+1] = data[i+2] = avg;
    }

    // Simple invert to mimic sketch
    for(let i=0; i<data.length; i+=4){
        data[i] = 255 - data[i];
        data[i+1] = 255 - data[i+1];
        data[i+2] = 255 - data[i+2];
    }

    ctx.putImageData(imageData, 0, 0);
}

// Download sketch
downloadBtn.addEventListener('click', function(){
    const link = document.createElement('a');
    link.download = 'sketch.png';
    link.href = canvas.toDataURL();
    link.click();
});
</script>

</body>
</html>
