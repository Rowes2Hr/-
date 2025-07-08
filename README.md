<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>منشئ السيرة الذاتية - CV Generator</title>
  <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.9.2/html2pdf.bundle.min.js"></script>
  <style>
    * {box-sizing: border-box;}
    body {
      font-family: 'Cairo', sans-serif;
      margin: 0;
      padding: 0;
      background: linear-gradient(to bottom right, #f2f4f8, #dfe6ed);
      direction: rtl;
      color: #333;
    }
    h1 {
      text-align: center;
      padding: 20px 0;
    }
    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }
    .form-section {
      background: #fff;
      border-radius: 20px;
      box-shadow: 0 10px 20px rgba(0,0,0,0.1);
      padding: 30px;
      max-width: 450px;
      width: 100%;
      margin-bottom: 30px;
    }
    label {
      font-weight: bold;
      display: block;
      margin-top: 15px;
    }
    input, textarea, select {
      width: 100%;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 8px;
      margin-top: 5px;
      font-family: inherit;
    }
    .btn {
      margin-top: 20px;
      padding: 12px 20px;
      background: #007bff;
      color: #fff;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-size: 16px;
    }
    .btn:hover {
      background: #0056b3;
    }
    .cv-output {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 20px;
    }
    .cv-card {
      width: 360px;
      min-height: 640px;
      border-radius: 20px;
      box-shadow: 0 10px 25px rgba(0,0,0,0.1);
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }
    .theme-blue .cv-top {
      background: #2b5f9e;
      color: white;
    }
    .theme-dark .cv-top {
      background: #1e1e1e;
      color: white;
    }
    .theme-purple .cv-top {
      background: linear-gradient(to right, #c850c0, #4158d0);
      color: white;
    }
    .theme-modern .cv-top {
      background: #222;
      color: white;
      clip-path: polygon(0 0, 100% 0, 100% 85%, 0% 100%);
    }
    .cv-top {
      padding: 30px 20px;
      text-align: center;
    }
    .cv-top img {
      width: 100px;
      height: 100px;
      object-fit: cover;
      border-radius: 50%;
      border: 4px solid #fff;
      margin-bottom: 10px;
    }
    .cv-top h2 {
      font-size: 20px;
      margin: 10px 0 5px;
    }
    .cv-top p {
      font-size: 14px;
      margin: 0;
    }
    .cv-body {
      background: #fff;
      padding: 20px;
    }
    .cv-body h3 {
      font-size: 16px;
      margin-bottom: 5px;
      border-bottom: 2px solid #007bff;
      padding-bottom: 4px;
    }
    .cv-body p {
      font-size: 14px;
      margin-bottom: 10px;
      line-height: 1.6;
    }
    .cv-body .skills span {
      display: inline-block;
      background: #eee;
      margin: 4px;
      padding: 5px 10px;
      border-radius: 12px;
      font-size: 13px;
    }
  </style>
</head>
<body>
  <h1>منشئ السيرة الذاتية - تصميم عمودي</h1>
  <div class="container">
    <div class="form-section">
      <label>الصورة الشخصية:</label>
      <input type="file" id="profilePic" accept="image/*" onchange="previewImage(event)">
      <img id="picPreview" style="display:none;max-width:80px;border-radius:50%;margin:10px auto;"/>

      <label>نمط التصميم:</label>
      <select id="theme">
        <option value="theme-blue">أزرق رسمي</option>
        <option value="theme-dark">غامق أنيق</option>
        <option value="theme-purple">متدرج جذاب</option>
        <option value="theme-modern">تصميم حديث</option>
      </select>

      <label>الاسم الكامل:</label>
      <input type="text" id="name">

      <label>المسمى الوظيفي:</label>
      <input type="text" id="title">

      <label>معلومات التواصل:</label>
      <input type="text" id="contact" placeholder="example@email.com - 0123456789">

      <label>المهارات:</label>
      <textarea id="skills" placeholder="HTML, CSS, JavaScript..."></textarea>

      <label>نبذة عنك:</label>
      <textarea id="summary"></textarea>

      <label>الخبرات:</label>
      <textarea id="experience"></textarea>

      <label>التعليم:</label>
      <textarea id="education"></textarea>

      <button class="btn" onclick="generateCV()">توليد السيرة الذاتية</button>
      <button class="btn" onclick="downloadPDF()">تحميل PDF</button>
      <button class="btn" onclick="downloadImage()">تحميل PNG</button>
    </div>

    <div class="cv-output" id="cvResult"></div>
  </div>

  <script>
    function previewImage(event) {
      const reader = new FileReader();
      reader.onload = () => {
        document.getElementById('picPreview').src = reader.result;
        document.getElementById('picPreview').style.display = 'block';
      };
      reader.readAsDataURL(event.target.files[0]);
    }

    function generateCV() {
      const name = document.getElementById('name').value;
      const title = document.getElementById('title').value;
      const contact = document.getElementById('contact').value;
      const summary = document.getElementById('summary').value;
      const skills = document.getElementById('skills').value;
      const experience = document.getElementById('experience').value;
      const education = document.getElementById('education').value;
      const imgSrc = document.getElementById('picPreview').src;
      const theme = document.getElementById('theme').value;

      const skillTags = skills.split(',').map(s => `<span>${s.trim()}</span>`).join('');

      const html = `
        <div class="cv-card ${theme}" id="cvCard">
          <div class="cv-top">
            ${imgSrc ? `<img src="${imgSrc}" alt="صورة">` : ''}
            <h2>${name}</h2>
            <p>${title}</p>
            <p><i class="fas fa-phone"></i> ${contact}</p>
          </div>
          <div class="cv-body">
            <h3><i class="fas fa-user"></i> نبذة</h3>
            <p>${summary.replace(/\n/g, '<br>')}</p>

            <h3><i class="fas fa-briefcase"></i> الخبرات</h3>
            <p>${experience.replace(/\n/g, '<br>')}</p>

            <h3><i class="fas fa-graduation-cap"></i> التعليم</h3>
            <p>${education.replace(/\n/g, '<br>')}</p>

            <h3><i class="fas fa-cogs"></i> المهارات</h3>
            <div class="skills">${skillTags}</div>
          </div>
        </div>`;

      document.getElementById('cvResult').innerHTML = html;
    }

    function downloadPDF() {
      const element = document.getElementById('cvCard');
      html2pdf().from(element).save('سيرتي-الذاتية.pdf');
    }

    function downloadImage() {
      html2canvas(document.getElementById("cvCard")).then(canvas => {
        const link = document.createElement('a');
        link.download = 'سيرتي-الذاتية.png';
        link.href = canvas.toDataURL();
        link.click();
      });
    }
  </script>
</body>
</html>
