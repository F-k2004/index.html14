<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <title>🎨 استخراج پالت رنگ از تصویر</title>
  <style>
    body {
      margin: 0;
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: #222;
      color: #fff;
      font-family: sans-serif;
      direction: rtl;
    }

    .container {
      width: 380px;
      text-align: center;
      background: rgba(255,255,255,0.1);
      padding: 25px;
      border-radius: 15px;
      box-shadow: 0 4px 25px rgba(0,0,0,0.4);
      backdrop-filter: blur(6px);
    }

    #preview {
      width: 100%;
      border-radius: 12px;
      margin-top: 10px;
      display: none;
    }

    .palette {
      margin-top: 20px;
      display: flex;
      justify-content: space-between;
    }

    .colorBox {
      width: 60px;
      height: 60px;
      border-radius: 8px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 11px;
      color: #000;
      background: #fff;
      font-weight: bold;
    }

    button {
      margin-top: 15px;
      padding: 10px 20px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      background: #fff;
      color: #000;
      font-size: 15px;
    }
  </style>
</head>
<body>

  <div class="container">
    <h2>🎨 استخراج پالت رنگ از تصویر</h2>

    <input type="file" accept="image/*" id="imgInput" onchange="loadImage()">
    <img id="preview">

    <div class="palette" id="palette"></div>
  </div>

  <script>
    function loadImage() {
      const file = document.getElementById("imgInput").files[0];
      if (!file) return;

      const img = document.getElementById("preview");
      img.src = URL.createObjectURL(file);
      img.style.display = "block";

      img.onload = () => extractPalette(img);
    }

    function extractPalette(img) {
      const canvas = document.createElement("canvas");
      const ctx = canvas.getContext("2d");

      canvas.width = img.width;
      canvas.height = img.height;

      ctx.drawImage(img, 0, 0);

      const data = ctx.getImageData(0, 0, canvas.width, canvas.height).data;

      let colors = {};

      // تحلیل رنگ‌ها
      for (let i = 0; i < data.length; i += 4) {
        const r = data[i];
        const g = data[i + 1];
        const b = data[i + 2];

        const rgb = `${r},${g},${b}`;
        if (!colors[rgb]) colors[rgb] = 0;
        colors[rgb]++;
      }

      // ۵ رنگ پرتکرار
      const topColors = Object.entries(colors)
        .sort((a, b) => b[1] - a[1])
        .slice(0, 5)
        .map(c => c[0]);

      displayPalette(topColors);
    }

    function displayPalette(colors) {
      const box = document.getElementById("palette");
      box.innerHTML = "";

      colors.forEach(rgb => {
        const div = document.createElement("div");
        const hex = rgbToHex(rgb);

        div.className = "colorBox";
        div.style.background = `rgb(${rgb})`;
        div.textContent = hex;

        div.onclick = () => {
          navigator.clipboard.writeText(hex);
          alert("کپی شد: " + hex);
        };

        box.appendChild(div);
      });
    }

    function rgbToHex(rgb) {
      const [r, g, b] = rgb.split(",").map(Number);
      return "#" + 
        r.toString(16).padStart(2, "0") +
        g.toString(16).padStart(2, "0") +
        b.toString(16).padStart(2, "0")
      .toUpperCase();
    }
  </script>

</body>
</html>
