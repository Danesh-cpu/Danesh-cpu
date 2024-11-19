<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Photo Color Change App</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 20px;
        }
        canvas {
            display: block;
            margin: 20px auto;
            border: 1px solid #ccc;
        }
        input, button {
            margin: 10px;
            padding: 10px;
        }
    </style>
</head>
<body>
    <h1>Photo Color Change App</h1>
    <p>Upload an image and apply a color filter!</p>
    <input type="file" id="fileInput" accept="image/*">
    <canvas id="photoCanvas" width="400" height="300"></canvas>
    <div>
        <button onclick="applyFilter('grayscale')">Grayscale</button>
        <button onclick="applyFilter('sepia')">Sepia</button>
        <button onclick="applyFilter('invert')">Invert</button>
        <button onclick="applyFilter('reset')">Reset</button>
    </div>

    <script>
        const fileInput = document.getElementById('fileInput');
        const canvas = document.getElementById('photoCanvas');
        const ctx = canvas.getContext('2d');
        let originalImage = null;

        // Load image into canvas
        fileInput.addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    const img = new Image();
                    img.onload = () => {
                        // Resize canvas to match image dimensions
                        canvas.width = img.width;
                        canvas.height = img.height;
                        ctx.drawImage(img, 0, 0);
                        originalImage = ctx.getImageData(0, 0, canvas.width, canvas.height); // Save original image
                    };
                    img.src = e.target.result;
                };
                reader.readAsDataURL(file);
            }
        });

        // Apply filter to the image
        function applyFilter(filter) {
            if (!originalImage) return alert('Please upload an image first!');
            
            const imageData = new ImageData(
                new Uint8ClampedArray(originalImage.data), 
                originalImage.width, 
                originalImage.height
            );

            const data = imageData.data;

            for (let i = 0; i < data.length; i += 4) {
                let r = data[i], g = data[i + 1], b = data[i + 2];
                if (filter === 'grayscale') {
                    const gray = 0.3 * r + 0.59 * g + 0.11 * b;
                    data[i] = data[i + 1] = data[i + 2] = gray;
                } else if (filter === 'sepia') {
                    data[i] = 0.393 * r + 0.769 * g + 0.189 * b;
                    data[i + 1] = 0.349 * r + 0.686 * g + 0.168 * b;
                    data[i + 2] = 0.272 * r + 0.534 * g + 0.131 * b;
                } else if (filter === 'invert') {
                    data[i] = 255 - r;
                    data[i + 1] = 255 - g;
                    data[i + 2] = 255 - b;
                } else if (filter === 'reset') {
                    ctx.putImageData(originalImage, 0, 0);
                    return;
                }
            }
            ctx.putImageData(imageData, 0, 0);
        }
    </script>
</body>
</html>
