# Url-SHORTNER-with-qr-code-
The URL Shortener with QR Code and Visualization Features application offers a  comprehensive solution for transforming lengthy URLs into concise, shareable links while  generating QR codes for easy access. This platform enables users to track link performance  through click monitoring and provides robust data visualization tools.
<!-- index.ejs -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Link Shortener</title>
    <link rel="stylesheet" href="css/style.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <script>
        function showAlert() {
            alert('Your link has been submitted successfully!');
        }

        function generateQRCode(shortUrl, id) {
            var qrcodeContainer = document.getElementById('qrcode-' + id);
            var qrButton = document.getElementById('qr-btn-' + id);
            qrcodeContainer.innerHTML = '';
            new QRCode(qrcodeContainer, {
                text: shortUrl,
                width: 80,
                height: 80
            });
            qrButton.style.display = 'none';
        }

        function renderClickChart(labels, data) {
            var ctx = document.getElementById('clickChart').getContext('2d');
            new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: labels,
                    datasets: [{
                        label: '# of Clicks',
                        data: data,
                        backgroundColor: 'rgba(75, 192, 192, 0.6)',
                        borderColor: 'rgba(75, 192, 192, 1)',
                        borderWidth: 1
                    }]
                },
                options: {
                    scales: {
                        y: { beginAtZero: true }
                    },
                    onClick: function(event, elements) {
                        if (elements.length > 0) {
                            const clickedElementIndex = elements[0].index;
                            const url = labels[clickedElementIndex];
                            window.open(url, '_blank');
                        }
                    }
                }
            });
        }
    </script>
</head>
<body>
    <div class="navbar">
        <a href="#home">Home</a>
        <a href="#shortener-section">URLs Shortener</a>
        <a href="#links-section">Dashboard</a>
        <a href="#visualization-section">Data Visualization</a>
    </div>

    <div class="landing-page" id="home">
        <h1>Welcome to the URL Shortener <br>with QR Code and <br>Visualization Features!</h1>
        <p>Shorten your URLs quickly and track the clicks for each of your links.</p>
        <a href="#shortener-section" class="cta-button">Get Started</a>
    </div>

    <div class="container form-container" id="shortener-section">
        <h1 class="heading">Link Shortener</h1>
        <div class="form">
            <form action="/shortUrls" method="post" onsubmit="showAlert()">
                <input type="url" name="full" placeholder="Enter your link" required>
                <button type="submit">Submit</button>
            </form>
        </div>
    </div>

    <div class="container links-container" id="links-section">
        <h1 class="headings">Your Links</h1>
        <div class="links">
            <div class="link-box">
                <div class="full"><h2>Full Link</h2></div>
                <div class="short"><h2>Short Link & QR Code</h2></div>
                <div class="click"><h2>Clicks/Action</h2></div>
            </div>
        </div>
        <% shorturls.forEach((url, index) => { %>
        <div class="links">
            <div class="link-box">
                <div class="full"><a href="<%= url.full %>"><%= url.full %></a></div>
                <div class="short">
                    <a target="_blank" href="<%= url.short %>"><%= url.short %></a>
                    <div id="qrcode-<%= index %>" style="display: inline-block;"></div>
                    <button id="qr-btn-<%= index %>" onclick="generateQRCode('<%= url.short %>', <%= index %>)">Generate QR</button>
                </div>
                <div class="clicks"><span><%= url.clicks %></span></div>
                <div class="delete">
                    <a href="/delete/<%= url._id %>">
                        <span class="material-symbols-outlined">auto_delete</span>
                    </a>
                </div>
            </div>
        </div>
        <% }); %>
    </div>

    <div class="container visualization-container" id="visualization-section">
        <h1 class="headings">Click Data Visualization</h1>
        <canvas id="clickChart" width="700" height="200"></canvas>
    </div>

    <script>
        const shortUrls = [<% shorturls.forEach(url => { %> "<%= url.short %>", <% }); %>];
        const clickCounts = [<% shorturls.forEach(url => { %> <%= url.clicks %>, <% }); %>];
        const clickData = {
            labels: shortUrls,
            datasets: [{
                label: 'Number of Clicks',
                data: clickCounts,
                backgroundColor: [
                    'rgba(75, 192, 192, 0.2)',
                    'rgba(153, 102, 255, 0.2)',
                    'rgba(255, 159, 64, 0.2)',
                    'rgba(255, 99, 132, 0.2)',
                ],
                borderColor: [
                    'rgba(75, 192, 192, 1)',
                    'rgba(153, 102, 255, 1)',
                    'rgba(255, 159, 64, 1)',
                    'rgba(255, 99, 132, 1)',
                ],
                borderWidth: 1
            }]
        };
        const config = {
            type: 'bar',
            data: clickData,
            options: {
                scales: { y: { beginAtZero: true } },
                onClick: function(event, elements) {
                    if (elements.length > 0) {
                        const clickedElementIndex = elements[0].index;
                        const url = shortUrls[clickedElementIndex];
                        window.open(url, '_blank');
                    }
                }
            }
        };
        const clickChart = new Chart(document.getElementById('clickChart'), config);
    </script>
</body>
</html>
/* style.css */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
    overflow-y: auto;
}

.landing-page {
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    padding: 20px;
}

.landing-page h1 {
    font-size: 48px;
    margin-bottom: 20px;
    color: darkolivegreen;
}

.landing-page p {
    font-size: 20px;
    margin-bottom: 30px;
    color: darkcyan;
}

.cta-button {
    padding: 10px 20px;
    font-size: 18px;
    background-color: #007BFF;
    color: white;
    text-decoration: none;
    border-radius: 5px;
    transition: background-color 0.3s;
}

.cta-button:hover {
    background-color: #0056b3;
}

.navbar {
    display: flex;
    justify-content: flex-start;
    background-color: mediumturquoise;
    padding: 5px 10px;
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 1000;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.navbar a {
    color: white;
    text-decoration: none;
    background-color: darkcyan;
    padding: 10px 15px;
    font-size: 18px;
    border: 2px solid whitesmoke;
    border-radius: 5px;
    margin-right: 3px;
    transition: background-color 0.3s, border-color 0.3s;
}

.navbar a:hover {
    background-color: wheat;
    color: black;
    border-color: whitesmoke;
}

html {
    scroll-behavior: smooth;
}

.container {
    padding: 100px 50px 50px;
    height: 100vh;
    text-align: center;
}

.container .heading {
    font-size: 62px;
    color: darkcyan;
}

.qr-code {
    width: 100px;
    height: 50px;
    margin: 0 10px;
}

@media screen and (max-width: 768px) {
    .qr-code {
        width: 80px;
        height: 80px;
    }
}

@media screen and (max-width: 480px) {
    .navbar a {
        font-size: 14px;
        padding: 8px;
    }

    .landing-page h1 {
        font-size: 28px;
    }

    .landing-page p {
        font-size: 14px;
    }

    .container .heading {
        font-size: 36px;
    }

    .form input {
        width: 100%;
        padding: 10px;
    }

    .link-box {
        padding: 10px;
    }

    .qr-code {
        width: 60px;
        height: 60px;
    }
}
// server.js
const express = require('express')
const mongoose = require('mongoose')

const app = express()

const PORT = process.env.PORT || 3000

mongoose.connect('mongodb://127.0.0.1:27017/urlshorts')

const db = mongoose.connection

db.on('error', () => {
    console.log('error')
})

db.once('open', () => {
    console.log("connected")
})

app.set('view engine', 'ejs')
app.use(express.static('public'))
app.use(express.json())
app.use(express.urlencoded({ extended: true }))

const urlRouter = require('./routes/urlRoute')
app.use('/', urlRouter)

app.listen(PORT, () => {
    console.log("server is running")
})
