<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>MasterFoot | eFootball Draft</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * { box-sizing: border-box; }
        body {
            background-color: #0d1117;
            color: #ffffff;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 12px;
            user-select: none;
            text-align: center;
        }

        .screen { display: none; }
        .screen.active { display: block; }

        .brand-badge {
            background: rgba(0, 255, 136, 0.1);
            color: #00ff88;
            border: 1px solid rgba(0, 255, 136, 0.3);
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 11px;
            font-weight: bold;
            letter-spacing: 1px;
            display: inline-block;
            margin-bottom: 10px;
        }

        .home-card {
            background: linear-gradient(135deg, #161b22 0%, #1f242d 100%);
            border: 1px solid #30363d;
            border-radius: 16px;
            padding: 25px 20px;
            margin-top: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
        }
        .logo { font-size: 48px; margin-bottom: 8px; }
        .title { font-size: 26px; font-weight: bold; color: #00ff88; margin-bottom: 4px; }
        .channel-name { font-size: 14px; color: #ffcc00; font-weight: bold; margin-bottom: 12px; }
        .subtitle { font-size: 13px; color: #8b949e; margin-bottom: 25px; }

        .main-btn {
            background: linear-gradient(90deg, #00ff88 0%, #00b862 100%);
            color: #000;
            border: none;
            padding: 16px 24px;
            font-size: 18px;
            font-weight: bold;
            border-radius: 12px;
            cursor: pointer;
            width: 100%;
            box-shadow: 0 4px 15px rgba(0, 255, 136, 0.3);
        }
        .main-btn:active { transform: scale(0.98); }

        .channel-link-btn {
            background: #21262d;
            color: #58a6ff;
            border: 1px solid #30363d;
            padding: 12px;
            border-radius: 10px;
            margin-top: 12px;
            width: 100%;
            font-size: 13px;
            font-weight: bold;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
        }

        .formation-list { display: flex; flex-direction: column; gap: 12px; margin-top: 20px; }
        .form-card {
            background: #161b22;
            border: 1px solid #30363d;
            border-radius: 12px;
            padding: 16px;
            font-size: 18px;
            font-weight: bold;
            color: #00ff88;
            cursor: pointer;
        }

        .header-stats {
            display: flex;
            justify-content: space-around;
            background: #161b22;
            padding: 10px;
            border-radius: 12px;
            margin-bottom: 10px;
            border: 1px solid #30363d;
        }
        .stat-box { text-align: center; }
        .stat-title { font-size: 10px; color: #8b949e; text-transform: uppercase; }
        .stat-value { font-size: 20px; font-weight: bold; color: #00ff88; }

        .pitch {
            background: linear-gradient(180deg, #1e4d2b 0%, #14361e 100%);
            border: 2px solid #2ea043;
            border-radius: 16px;
            height: 460px;
            position: relative;
            overflow: hidden;
            box-shadow: inset 0 0 40px rgba(0,0,0,0.6);
        }
        .pitch::before {
            content: '';
            position: absolute;
            top: 50%; left: 0; right: 0;
            height: 2px; background: rgba(255,255,255,0.2);
        }
        .pitch-watermark {
            position: absolute;
            top: 50%; left: 50%;
            transform: translate(-50%, -50%);
            font-size: 24px;
            font-weight: 900;
            color: rgba(255, 255, 255, 0.08);
            letter-spacing: 2px;
            pointer-events: none;
            white-space: nowrap;
        }
        .center-circle {
            position: absolute;
            top: 50%; left: 50%;
            width: 80px; height: 80px;
            border: 2px solid rgba(255,255,255,0.2);
            border-radius: 50%;
            transform: translate(-50%, -50%);
        }

        .slot {
            position: absolute;
            width: 60px;
            height: 70px;
            background: rgba(22, 27, 34, 0.85);
            border: 2px dashed #00ff88;
            border-radius: 8px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transform: translate(-50%, -50%);
        }
        .slot.filled { border-style: solid; background: #1f242d; }
        .slot-pos { font-size: 10px; font-weight: bold; color: #8b949e; }
        .slot-add { font-size: 16px; color: #00ff88; }
        .slot-name { font-size: 9px; font-weight: bold; text-align: center; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; width: 100%; padding: 0 2px; }
        .slot-ovr { font-size: 11px; font-weight: bold; color: #ffcc00; }

        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.85);
            z-index: 100;
            justify-content: center;
            align-items: center;
            padding: 15px;
        }
        .modal-content {
            background: #161b22;
            border-radius: 16px;
            padding: 15px;
            width: 100%;
            max-width: 380px;
            border: 1px solid #30363d;
        }
        .modal-title { font-size: 16px; text-align: center; margin-bottom: 12px; color: #00ff88; }
        .card-list { display: flex; flex-direction: column; gap: 8px; }
        
        .pick-card {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: #21262d;
            padding: 10px 14px;
            border-radius: 10px;
            border-left: 4px solid #8b949e;
            cursor: pointer;
        }
        .pick-card.Epic { border-left-color: #d4af37; background: linear-gradient(90deg, #3a2e05 0%, #21262d 100%); }
        .pick-card.Highlight { border-left-color: #00ff88; background: linear-gradient(90deg, #0e2b1b 0%, #21262d 100%); }
        .pick-card.Standard { border-left-color: #388bfd; }

        .player-meta { text-align: left; }
        .p-name { font-weight: bold; font-size: 13px; }
        .p-details { font-size: 10px; color: #8b949e; }
        .p-ovr { font-size: 17px; font-weight: bold; color: #ffcc00; }

        .reset-btn {
            background: #21262d;
            color: #f85149;
            border: 1px solid #30363d;
            padding: 10px;
            border-radius: 8px;
            margin-top: 10px;
            width: 100%;
            font-weight: bold;
            cursor: pointer;
        }
    </style>
</head>
<body>

    <!-- 1. ГЛАВНОЕ МЕНЮ -->
    <div id="screen-home" class="screen active">
        <div class="home-card">
            <div class="brand-badge">OFFICIAL GAME APP</div>
            <div class="logo">⚽</div>
            <div class="title">eFootball Draft</div>
            <div class="channel-name">by MasterFoot</div>
            <div class="subtitle">Собери свой сильнейший состав!</div>
            
            <button class="main-btn" onclick="showScreen('screen-formation')">НАЧАТЬ ДРАФТ</button>
            <button class="channel-link-btn" onclick="openChannel()">📢 Наш Telegram Канал</button>
        </div>
    </div>

    <!-- 2. ВЫБОР СХЕМЫ -->
    <div id="screen-formation" class="screen">
        <h2 style="color:#00ff88;">Выбери схему</h2>
        <p class="subtitle">Тактика определит позиции на поле</p>
        <div class="formation-list">
            <div class="form-card" onclick="startDraft('4-3-3')">4 - 3 - 3 (Атака)</div>
            <div class="form-card" onclick="startDraft('4-2-1-3')">4 - 2 - 1 - 3 (Контратака)</div>
            <div class="form-card" onclick="startDraft('3-2-2-3')">3 - 2 - 2 - 3 (Вингеры)</div>
        </div>
    </div>

    <!-- 3. ЭКРАН СБОРКИ -->
    <div id="screen-draft" class="screen">
        <div class="header-stats">
            <div class="stat-box">
                <div class="stat-title">Схема</div>
                <div id="selected-formation-name" class="stat-value" style="font-size:16px;">4-3-3</div>
            </div>
            <div class="stat-box">
                <div class="stat-title">Team OVR</div>
                <div id="team-ovr" class="stat-value">0</div>
            </div>
            <div class="stat-box">
                <div class="stat-title">Игроков</div>
                <div id="team-count" class="stat-value">0/11</div>
            </div>
        </div>

        <div class="pitch" id="pitch-container">
            <div class="center-circle"></div>
            <div class="pitch-watermark">MASTERFOOT</div>
        </div>

        <button class="reset-btn" onclick="resetDraft()">Заново в меню</button>
    </div>

    <!-- МОДАЛКА ВЫБОРА ИГРОКА -->
    <div class="modal" id="picker-modal">
        <div class="modal-content">
            <div class="modal-title" id="modal-heading">Выбери игрока</div>
            <div class="card-list" id="card-options"></div>
        </div>
    </div>

    <script>
        if (window.Telegram && window.Telegram.WebApp) {
            window.Telegram.WebApp.expand();
        }

        // БАЗА ДАННЫХ
        const database = [
            // GK
            { id: 1, name: "P. Schmeichel", ovr: 99, pos: "GK", type: "Epic", club: "Man Utd" },
            { id: 2, name: "M. Neuer", ovr: 96, pos: "GK", type: "Highlight", club: "Bayern" },
            { id: 3, name: "G. Donnarumma", ovr: 94, pos: "GK", type: "Standard", club: "PSG" },
            { id: 4, name: "T. Courtois", ovr: 95, pos: "GK", type: "Standard", club: "Real Madrid" },
            { id: 5, name: "I. Casillas", ovr: 98, pos: "GK", type: "Epic", club: "Real Madrid" },

            // CB
            { id: 10, name: "P. Maldini", ovr: 100, pos: "CB", type: "Epic", club: "AC Milan" },
            { id: 11, name: "V. van Dijk", ovr: 97, pos: "CB", type: "Highlight", club: "Liverpool" },
            { id: 12, name: "A. Nesta", ovr: 98, pos: "CB", type: "Epic", club: "AC Milan" },
            { id: 13, name: "Rúben Dias", ovr: 95, pos: "CB", type: "Standard", club: "Man City" },
            { id: 14, name: "F. Cannavaro", ovr: 98, pos: "CB", type: "Epic", club: "Italy" },
            { id: 15, name: "E. Militao", ovr: 94, pos: "CB", type: "Standard", club: "Real Madrid" },
            { id: 16, name: "A. Rüdiger", ovr: 95, pos: "CB", type: "Highlight", club: "Real Madrid" },

            // LB
            { id: 20, name: "Roberto Carlos", ovr: 98, pos: "LB", type: "Epic", club: "Real Madrid" },
            { id: 21, name: "T. Hernandez", ovr: 94, pos: "LB", type: "Highlight", club: "AC Milan" },
            { id: 22, name: "A. Robertson", ovr: 93, pos: "LB", type: "Standard", club: "Liverpool" },
            { id: 23, name: "A. Davies", ovr: 95, pos: "LB", type: "Highlight", club: "Bayern" },

            // RB
            { id: 30, name: "Cafu", ovr: 97, pos: "RB", type: "Epic", club: "AC Milan" },
            { id: 31, name: "A. Hakimi", ovr: 94, pos: "RB", type: "Highlight", club: "PSG" },
            { id: 32, name: "J. Koundé", ovr: 94, pos: "RB", type: "Highlight", club: "Barcelona" },
            { id: 33, name: "Trent A-A", ovr: 95, pos: "RB", type: "Highlight", club: "Liverpool" },

            // DMF
            { id: 50, name: "P. Vieira", ovr: 100, pos: "DMF", type: "Epic", club: "Arsenal" },
            { id: 51, name: "Rodri", ovr: 96, pos: "DMF", type: "Standard", club: "Man City" },
            { id: 52, name: "Casemiro", ovr: 94, pos: "DMF", type: "Standard", club: "Man Utd" },
            { id: 53, name: "F. Rijkaard", ovr: 98, pos: "DMF", type: "Epic", club: "AC Milan" },

            // CMF
            { id: 54, name: "L. Modrić", ovr: 96, pos: "CMF", type: "Highlight", club: "Real Madrid" },
            { id: 55, name: "Pedri", ovr: 94, pos: "CMF", type: "Standard", club: "Barcelona" },
            { id: 56, name: "F. Valverde", ovr: 96, pos: "CMF", type: "Highlight", club: "Real Madrid" },
            { id: 57, name: "Xavi", ovr: 97, pos: "CMF", type: "Epic", club: "Barcelona" },
            { id: 58, name: "Iniesta", ovr: 98, pos: "CMF", type: "Epic", club: "Barcelona" },

            // AMF
            { id: 40, name: "Ruud Gullit", ovr: 101, pos: "AMF", type: "Epic", club: "AC Milan" },
            { id: 41, name: "K. De Bruyne", ovr: 97, pos: "AMF", type: "Highlight", club: "Man City" },
            { id: 42, name: "J. Bellingham", ovr: 98, pos: "AMF", type: "Highlight", club: "Real Madrid" },
            { id: 43, name: "Kaká", ovr: 99, pos: "AMF", type: "Epic", club: "AC Milan" },
            { id: 44, name: "D. Maradona", ovr: 101, pos: "AMF", type: "Epic", club: "Argentina" },

            // LWF
            { id: 70, name: "Ronaldinho", ovr: 100, pos: "LWF", type: "Epic", club: "Barcelona" },
            { id: 71, name: "Vini Jr.", ovr: 97, pos: "LWF", type: "Highlight", club: "Real Madrid" },
            { id: 72, name: "K. Mbappé", ovr: 98, pos: "LWF", type: "Highlight", club: "Real Madrid" },
            { id: 73, name: "Neymar Jr", ovr: 99, pos: "LWF", type: "Epic", club: "Santos" },

            // RWF
            { id: 60, name: "L. Messi", ovr: 102, pos: "RWF", type: "Epic", club: "Inter Miami" },
            { id: 61, name: "M. Salah", ovr: 96, pos: "RWF", type: "Highlight", club: "Liverpool" },
            { id: 62, name: "L. Yamal", ovr: 95, pos: "RWF", type: "Highlight", club: "Barcelona" },
            { id: 63, name: "L. Figo", ovr: 97, pos: "RWF", type: "Epic", club: "Real Madrid" },

            // CF
            { id: 80, name: "Ronaldo Nazário", ovr: 101, pos: "CF", type: "Epic", club: "Inter" },
            { id: 81, name: "C. Ronaldo", ovr: 98, pos: "CF", type: "Epic", club: "Al Nassr" },
            { id: 82, name: "E. Haaland", ovr: 97, pos: "CF", type: "Highlight", club: "Man City" },
            { id: 83, name: "M. van Basten", ovr: 99, pos: "CF", type: "Epic", club: "AC Milan" },
            { id: 84, name: "R. Lewandowski", ovr: 96, pos: "CF", type: "Standard", club: "Barcelona" }
        ];

        // КАРТА РОДСТВЕННЫХ ПОЗИЦИЙ (ЕСЛИ ТОЧНЫЕ ИГРОКИ ЗАКОНЧИЛИСЬ)
        const positionGroups = {
            'GK': ['GK'],
            'CB': ['CB', 'LB', 'RB'],
            'LB': ['LB', 'CB', 'LMF'],
            'RB': ['RB', 'CB', 'RMF'],
            'DMF': ['DMF', 'CMF'],
            'CMF': ['CMF', 'DMF', 'AMF'],
            'AMF': ['AMF', 'CMF', 'SS'],
            'LWF': ['LWF', 'RWF', 'CF', 'LMF'],
            'RWF': ['RWF', 'LWF', 'CF', 'RMF'],
            'CF': ['CF', 'SS', 'LWF', 'RWF']
        };

        const formations = {
            '4-3-3': [
                { id: 'pos-1', pos: 'GK', top: '88%', left: '50%' },
                { id: 'pos-2', pos: 'LB', top: '72%', left: '18%' },
                { id: 'pos-3', pos: 'CB', top: '74%', left: '39%' },
                { id: 'pos-4', pos: 'CB', top: '74%', left: '61%' },
                { id: 'pos-5', pos: 'RB', top: '72%', left: '82%' },
                { id: 'pos-6', pos: 'CMF', top: '50%', left: '28%' },
                { id: 'pos-7', pos: 'AMF', top: '45%', left: '50%' },
                { id: 'pos-8', pos: 'CMF', top: '50%', left: '72%' },
                { id: 'pos-9', pos: 'LWF', top: '22%', left: '20%' },
                { id: 'pos-10', pos: 'CF', top: '16%', left: '50%' },
                { id: 'pos-11', pos: 'RWF', top: '22%', left: '80%' }
            ],
            '4-2-1-3': [
                { id: 'pos-1', pos: 'GK', top: '88%', left: '50%' },
                { id: 'pos-2', pos: 'LB', top: '72%', left: '18%' },
                { id: 'pos-3', pos: 'CB', top: '74%', left: '39%' },
                { id: 'pos-4', pos: 'CB', top: '74%', left: '61%' },
                { id: 'pos-5', pos: 'RB', top: '72%', left: '82%' },
                { id: 'pos-6', pos: 'DMF', top: '56%', left: '36%' },
                { id: 'pos-7', pos: 'DMF', top: '56%', left: '64%' },
                { id: 'pos-8', pos: 'AMF', top: '40%', left: '50%' },
                { id: 'pos-9', pos: 'LWF', top: '22%', left: '20%' },
                { id: 'pos-10', pos: 'CF', top: '16%', left: '50%' },
                { id: 'pos-11', pos: 'RWF', top: '22%', left: '80%' }
            ],
            '3-2-2-3': [
                { id: 'pos-1', pos: 'GK', top: '88%', left: '50%' },
                { id: 'pos-2', pos: 'CB', top: '74%', left: '25%' },
                { id: 'pos-3', pos: 'CB', top: '76%', left: '50%' },
                { id: 'pos-4', pos: 'CB', top: '74%', left: '75%' },
                { id: 'pos-5', pos: 'DMF', top: '56%', left: '38%' },
                { id: 'pos-6', pos: 'DMF', top: '56%', left: '62%' },
                { id: 'pos-7', pos: 'LB', top: '40%', left: '18%' },
                { id: 'pos-8', pos: 'RB', top: '40%', left: '82%' },
                { id: 'pos-9', pos: 'LWF', top: '22%', left: '22%' },
                { id: 'pos-10', pos: 'CF', top: '16%', left: '50%' },
                { id: 'pos-11', pos: 'RWF', top: '22%', left: '78%' }
            ]
        };

        let currentFormation = '4-3-3';
        let currentSlot = null;
        let squad = {};

        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(screenId).classList.add('active');
        }

        function openChannel() {
            if (window.Telegram && window.Telegram.WebApp) {
                window.Telegram.WebApp.openTelegramLink('https://t.me/masterfoot1'); 
            } else {
                window.open('https://t.me/masterfoot1', '_blank');
            }
        }

        function startDraft(formationKey) {
            currentFormation = formationKey;
            squad = {};
            document.getElementById('selected-formation-name').innerText = formationKey;
            document.getElementById('team-ovr').innerText = "0";
            document.getElementById('team-count').innerText = "0/11";

            const pitch = document.getElementById('pitch-container');
            pitch.innerHTML = `
                <div class="center-circle"></div>
                <div class="pitch-watermark">MASTERFOOT</div>
            `;

            formations[formationKey].forEach(item => {
                const slot = document.createElement('div');
                slot.className = 'slot';
                slot.id = item.id;
                slot.style.top = item.top;
                slot.style.left = item.left;
                slot.onclick = () => openPicker(item.id, item.pos);
                slot.innerHTML = `<span class="slot-pos">${item.pos}</span><span class="slot-add">+</span>`;
                pitch.appendChild(slot);
            });

            showScreen('screen-draft');
        }

        function openPicker(slotId, targetPos) {
            currentSlot = slotId;

            const chosenIds = Object.values(squad).map(p => p.id);
            let available = database.filter(p => !chosenIds.includes(p.id));

            // 1. Пытаемся найти ровно по нужной позиции
            let pool = available.filter(p => p.pos === targetPos);

            // 2. Если мало, подключаем родственные позиции (защитники к защитникам, хавы к хавам)
            if (pool.length < 5) {
                const allowedPositions = positionGroups[targetPos] || [targetPos];
                pool = available.filter(p => allowedPositions.includes(p.pos));
            }

            const shuffled = [...pool].sort(() => 0.5 - Math.random());
            const options = shuffled.slice(0, 5);

            const container = document.getElementById('card-options');
            container.innerHTML = '';
            document.getElementById('modal-heading').innerText = "Выбери игрока на " + targetPos;

            options.forEach(p => {
                const card = document.createElement('div');
                card.className = `pick-card ${p.type}`;
                card.innerHTML = `
                    <div class="player-meta">
                        <div class="p-name">${p.name}</div>
                        <div class="p-details">${p.pos} | ${p.club} • ${p.type}</div>
                    </div>
                    <div class="p-ovr">${p.ovr}</div>
                `;
                card.onclick = () => selectPlayer(p, targetPos);
                container.appendChild(card);
            });

            document.getElementById('picker-modal').style.display = 'flex';
        }

        function selectPlayer(player, targetPos) {
            squad[currentSlot] = player;
            
            const slotEl = document.getElementById(currentSlot);
            slotEl.classList.add('filled');
            slotEl.innerHTML = `
                <div class="slot-pos">${targetPos}</div>
                <div class="slot-name">${player.name}</div>
                <div class="slot-ovr">${player.ovr}</div>
            `;

            document.getElementById('picker-modal').style.display = 'none';
            calculateTeamOVR();
        }

        function calculateTeamOVR() {
            const players = Object.values(squad);
            const total = players.reduce((sum, p) => sum + p.ovr, 0);
            document.getElementById('team-ovr').innerText = total;
            document.getElementById('team-count').innerText = `${players.length}/11`;

            if (players.length === 11) {
                if(window.Telegram && window.Telegram.WebApp) {
                    window.Telegram.WebApp.HapticFeedback.notificationOccurred('success');
                }
            }
        }

        function resetDraft() {
            showScreen('screen-home');
        }
    </script>
</body>
</html>
