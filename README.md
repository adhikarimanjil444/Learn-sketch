<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Learn-Sketch</title>
<style>
  body {
    font-family: 'Arial', sans-serif;
    background: #fdf6e3;
    margin: 0;
    padding: 0;
    text-align: center;
  }
  h1 {
    color: #2c3e50;
    margin: 20px 0;
  }
  p {
    color: #555;
  }
  .container {
    max-width: 900px;
    margin: 0 auto;
    padding: 20px;
  }
  input[type="file"] {
    margin: 20px 0;
  }
  .images {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
    gap: 20px;
  }
  .images div {
    flex: 1 1 300px;
    text-align: center;
    position: relative;
  }
  img, canvas {
    max-width: 100%;
    border: 2px solid #ccc;
    border-radius: 8px;
    background: white;
  }
  button {
    background: #e67e22;
    color: white;
    padding: 12px 25px;
    margin: 15px 0;
    border: none;
    border-radius: 6px;
    font-size: 16px;
    cursor: pointer;
  }
  button:hover {
    background: #d35400;
  }
  .tutorial {
    background: #f1f1f1;
    padding: 15px;
    margin-top: 30px;
    border-radius: 8px;
    text-align: left;
  }
  .tutorial h3 {
    margin-top: 0;
  }
  @media(max-width: 600px){
    .images {
      flex-direction: column;
    }
  }
</style>
</head>
<body>

<div class="container">
  <h1>Learn-Sketch</h1>
  <p>Upload an image and get a realistic pencil sketch with tracing tutorial!</p>

  <input type="file" id="imageUpload" accept="image/*">

  <div class="images">
    <div>
      <h3>Original Image</h3>
      <img id="originalImage" src="" alt="Your Image Preview">
    </div>
    <div>
      <h3>Pencil Sketch + Tracing</h3>
      <canvas id="sketchCanvas"></canvas>
    </div>
  </div>

  <button id="downloadBtn">Download Sketch</button>

  <div class="tutorial">
    <h3>Step-by-Step Drawing Tutorial:</h3>
    <ol>
      <li>Start by tracing the **main outlines** lightly.</li>
      <li>Add **edges and contours** according to the sketch.</li>
      <li>Shade areas gradually using **cross-hatching** for texture.</li>
      <li>Leave **highlights** white for light reflections.</li>
      <li>Refine details, erase extra lines, and compare with the sketch.</li>
      <li>Follow the **overlay lines** to improve your drawing skills.</li>
    </ol>
  </div>
</div>

<script>
const imageUpload = document.getElementById('imageUpload');
const originalImage = document.getElementById('originalImage');
const canvas = document.getElementById('sketchCanvas');
const ctx = canvas.getContext('2d');
const downloadBtn = document.getElementById('downloadBtn');
const DEEPAI_API_KEY = 5a2a1714-9883-49d6-b2e1-364fba087eef;
  
let sketchImg = new Image();

imageUpload.addEventListener('change', async function(event){
    const file = event.target.files[0];
    if(!file) return;

    // Show original image
    const reader = new FileReader();
    reader.onload = function(e){
        originalImage.src = e.target.result;
    }
    reader.readAsDataURL(file);

    // Show processing
    ctx.clearRect(0,0,canvas.width,canvas.height);
    ctx.font = "20px Arial";
    ctx.fillStyle = "#555";
    ctx.fillText("Processing sketch...", 20, 40);

    // Call DeepAI Pencil Sketch API
    let formData = new FormData();
formData.append("image", file);
fetch("API_URL", { method: "POST", body: formData, headers: { "API-Key": "..." }})

    try {
        const response = await fetch("https://api.deepai.org/api/pencil-sketch", {
            method: "POST",
            headers: { 'api-key': DEEPAI_API_KEY },
            body: formData
        });
        const data = await response.json();

        // Load AI sketch into image
        sketchImg = new Image();
        sketchImg.crossOrigin = "anonymous"; // avoid CORS issues
        sketchImg.onload = () => drawWithOverlay();
        sketchImg.src = data.output_url;

    } catch(err){
        alert("Error generating sketch: " + err);
    }
});

function drawWithOverlay(){
    // Set canvas size same as sketch
    canvas.width = sketchImg.width;
    canvas.height = sketchImg.height;

    // Draw sketch image
    ctx.drawImage(sketchImg,0,0);

    // Overlay tracing lines (semi-transparent)
    ctx.strokeStyle = "rgba(255,0,0,0.4)";
    ctx.lineWidth = 1.5;
    // Simple grid overlay for tracing guidance
    let step = 50;
    for(let x=0;x<canvas.width;x+=step){
        ctx.beginPath();
        ctx.moveTo(x,0);
        ctx.lineTo(x,canvas.height);
        ctx.stroke();
    }
    for(let y=0;y<canvas.height;y+=step){
        ctx.beginPath();
        ctx.moveTo(0,y);
        ctx.lineTo(canvas.width,y);
        ctx.stroke();
    }
}

// Download button
downloadBtn.addEventListener('click', function(){
    if(!sketchImg.src) return alert("No sketch to download!");
    const link = document.createElement('a');
    link.href = canvas.toDataURL();
    link.download = 'sketch.png';
    link.click();
});
</script>

</body>
</html>
