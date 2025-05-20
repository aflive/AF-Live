/*
AF LIVE Full Website Project
Includes frontend + backend with upload & login functionality.
*/

/**************************************
 Folder: af-live
**************************************/

// === package.json ===
{
  "name": "af-live",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "body-parser": "^1.20.2",
    "express": "^4.18.2",
    "express-session": "^1.17.3",
    "multer": "^1.4.5"
  }
}

// === .render.yaml ===
services:
  - type: web
    name: af-live-backend
    env: node
    plan: free
    buildCommand: npm install
    startCommand: node server.js


// === server.js ===
const express = require('express');
const session = require('express-session');
const bodyParser = require('body-parser');
const multer = require('multer');
const fs = require('fs');
const path = require('path');

const app = express();
const PORT = process.env.PORT || 3000;

app.use(bodyParser.urlencoded({ extended: true }));
app.use(session({ secret: 'aflive123', resave: true, saveUninitialized: true }));
app.use(express.static('public'));
app.use('/uploads', express.static('uploads'));

const storage = multer.diskStorage({
  destination: './uploads/',
  filename: (req, file, cb) => cb(null, Date.now() + '-' + file.originalname)
});
const upload = multer({ storage });

const admin = { username: 'admin', password: '12345' };

app.post('/login', (req, res) => {
  const { username, password } = req.body;
  if (username === admin.username && password === admin.password) {
    req.session.loggedIn = true;
    res.redirect('/admin.html');
  } else {
    res.send('Invalid login');
  }
});

app.post('/upload', upload.single('image'), (req, res) => {
  if (!req.session.loggedIn) return res.status(403).send('Unauthorized');

  const news = {
    title: req.body.title,
    content: req.body.content,
    imageUrl: '/uploads/' + req.file.filename,
    timestamp: new Date()
  };

  const data = fs.existsSync('news.json') ? JSON.parse(fs.readFileSync('news.json', 'utf8')) : [];
  data.unshift(news);
  fs.writeFileSync('news.json', JSON.stringify(data, null, 2));
  res.redirect('/admin.html');
});

app.get('/api/news', (req, res) => {
  const data = fs.existsSync('news.json') ? JSON.parse(fs.readFileSync('news.json', 'utf8')) : [];
  res.json(data);
});

app.listen(PORT, () => console.log(`AF LIVE running on port ${PORT}`));


/**************************************
 Folder: public
**************************************/

// === style.css ===
body { font-family: Arial, sans-serif; background: #f4f4f4; margin: 0; padding: 20px; }
h1, h2, h3 { color: #333; }
form { background: #fff; padding: 20px; border-radius: 10px; max-width: 600px; margin: auto; }
input, textarea, button {
  display: block; width: 100%; padding: 10px; margin-bottom: 15px;
  border-radius: 5px; border: 1px solid #ccc;
}
button { background-color: #333; color: white; border: none; cursor: pointer; }
button:hover { background-color: #555; }
img { max-width: 100%; height: auto; }


// === index.html ===
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>AF LIVE</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>AF LIVE - Latest News</h1>
  <div id="news"></div>
  <script>
    fetch('/api/news')
      .then(res => res.json())
      .then(data => {
        const container = document.getElementById('news');
        data.forEach(n => {
          container.innerHTML += `<h2>${n.title}</h2><img src="${n.imageUrl}"><p>${n.content}</p><hr>`;
        });
      });
  </script>
</body>
</html>


// === login.html ===
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Login - AF LIVE</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h2>Admin Login</h2>
  <form action="/login" method="POST">
    <input type="text" name="username" placeholder="Username" required>
    <input type="password" name="password" placeholder="Password" required>
    <button type="submit">Login</button>
  </form>
</body>
</html>


// === upload.html ===
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Upload News - AF LIVE</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h2>Upload News</h2>
  <form action="/upload" method="POST" enctype="multipart/form-data">
    <input type="text" name="title" placeholder="Title" required>
    <textarea name="content" placeholder="News content..." required></textarea>
    <input type="file" name="image" accept="image/*" required>
    <button type="submit">Upload</button>
  </form>
</body>
</html>


// === admin.html ===
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Admin Dashboard - AF LIVE</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h2>Admin Dashboard</h2>
  <a href="upload.html">Upload New Article</a>
  <hr>
  <div id="newsList"></div>
  <script>
    fetch('/api/news')
      .then(res => res.json())
      .then(data => {
        const list = document.getElementById('newsList');
        data.forEach(n => {
          list.innerHTML += `<h3>${n.title}</h3><img src="${n.imageUrl}" width="200"><p>${n.content}</p><hr>`;
        });
      });
  </script>
</body>
</html>


/**************************************
 Folder: uploads (empty)
**************************************/
// Will be auto-created by multer for uploaded images.


/**************************************
 Extra File: news.json (optional)
**************************************/
// Initially empty or: []
