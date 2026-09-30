<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>eFootball Draft Simulator</title>
    <!-- Подключаем Telegram SDK -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body {
            background-color: #121212;
            color: #ffffff;
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 0;
            padding: 20px;
        }
        .card-container {
            border: 2px solid #00ff88;
            border-radius: 12px;
            padding: 20px;
            margin-top: 30px;
            background-color: #1e1e1e;
            box-shadow: 0 0 15px rgba(0, 255, 136, 0.2);
        }
        .btn {
            background-color: #00ff88;
            color: #000;
            border: none;
            padding: 15px 30px;
            font-size: 18px;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            margin-top: 20px;
            width: 100%;
        }
        .rating {
            font-size: 24px;
            color: #ffcc00;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <h2>⚽ eFootball Draft</h2>
    
    <div class="card-container">
        <h3 id="player-name">Нажми кнопку ниже!</h3>
        <div id="player-rating" class="rating"></div>
        <p id="player-info">Выбери своего первого игрока</p>
    </div>

    <button class="btn" onclick="getRandomPlayer()">Получить игрока</button>

    <script>
        // Инициализация Telegram SDK
        if (window.Telegram && window.Telegram.WebApp) {
            window.Telegram.WebApp.expand();
        }

        // База данных для первого теста
        const players = [
            { name: "L. Messi", rating: 99, position: "RW", type: "Epic" },
            { name: "C. Ronaldo", rating: 98, position: "CF", type: "Highlight" },
            { name: "K. De Bruyne", rating: 96, position: "AMF", type: "Standard" },
            { name: "V. van Dijk", rating: 95, position: "CB", type: "Highlight" },
            { name: "Neymar Jr", rating: 97, position: "LWF", type: "Show Time" },
            { name: "K. Mbappé", rating: 98, position: "CF", type: "Highlight" }
        ];

        function getRandomPlayer() {
            const randomIndex = Math.floor(Math.random() * players.length);
            const player = players[randomIndex];
            
            document.getElementById('player-name').innerText = player.name;
            document.getElementById('player-rating').innerText = "★ " + player.rating;
            document.getElementById('player-info').innerText = "Позиция: " + player.position + " | Тип: " + player.type;
        }
    </script>
</body>
</html>
