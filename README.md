<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>eFootball Draft Simulator</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * { box-sizing: border-box; }
        body {
            background-color: #0d1117;
            color: #ffffff;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 10px;
            user-select: none;
        }

        .header-stats {
            display: flex;
            justify-content: space-around;
            background: #161b22;
            padding: 12px;
            border-radius: 12px;
            margin-bottom: 12px;
            border: 1px solid #30363d;
        }
        .stat-box { text-align: center; }
        .stat-title { font-size: 11px; color: #8b949e; text-transform: uppercase; }
        .stat-value { font-size: 22px; font-weight: bold; color: #00ff88; }

        .pitch {
            background: linear-gradient(180deg, #1e4d2b 0%, #14361e 100%);
            border: 2px solid #2ea043;
            border-radius: 16px;
            height: 480px;
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
        .center-circle {
            position: absolute;
            top: 50%; left: 50%;
            width: 90px; height: 90px;
            border: 2px solid rgba(255,255,255,0.2);
            border-radius: 50%;
            transform: translate(-50%, -50%);
        }

        .slot {
            position: absolute;
            width: 65px;
            height: 75px;
            background: rgba(22, 27, 34, 0.85);
            border: 2px dashed #00ff88;
            border-radius: 8px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transform: translate(-50%, -50%);
            transition: all 0.2s ease;
        }
        .slot.filled {
            border-style: solid;
            background: #1f242d;
        }
        .slot-pos { font-size: 11px; font-weight: bold; color: #8b949e; }
        .slot-add { font-size: 18px; color: #00ff88; margin-top: 2px; }
        .slot-name { font-size: 10px; font-weight: bold; text-align: center; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; width: 100%; padding: 0 2px; }
        .slot-ovr { font-size: 12px; font-weight: bold; color: #ffcc00; }

        /* Координаты 4-3-3 */
        #pos-GK  { top: 88%; left: 50%; }
        #pos-LB  { top: 72%; left: 18%; }
        #pos-CB1 { top: 74%; left: 39%; }
        #pos-CB2 { top: 74%; left: 61%; }
        #pos-RB  { top: 72%; left: 82%; }
        #pos-CM1 { top: 50%; left: 28%; }
        #pos-AMF { top: 45%; left: 50%; }
        #pos-CM2 { top: 50%; left: 72%; }
        #pos-LWF { top: 22%; left: 20%; }
        #pos-CF  { top: 16%; left: 50%; }
        #pos-RWF { top: 22%; left: 80%; }

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
        .p-name { font-weight: bold; font-size: 14px; }
        .p-details { font-size: 11px; color: #8b949e; }
        .p-ovr { font-size: 18px; font-weight: bold; color: #ffcc00; }
    </style>
</head>
<body>

    <div class="header-stats">
        <div class="stat-box">
            <div class="stat-title">Team OVR</div>
            <div id="team-ovr" class="stat-value">0</div>
        </div>
        <div class="stat-box">
            <div class="stat-title">Playstyle</div>
            <div id="team-style" class="stat-value">70</div>
        </div>
    </div>

    <div class="pitch">
        <div class="center-circle"></div>

        <div class="slot" id="pos-GK" onclick="openPicker('GK')"><span class="slot-pos">GK</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-LB" onclick="openPicker('LB')"><span class="slot-pos">LB</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-CB1" onclick="openPicker('CB1')"><span class="slot-pos">CB</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-CB2" onclick="openPicker('CB2')"><span class="slot-pos">CB</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-RB" onclick="openPicker('RB')"><span class="slot-pos">RB</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-CM1" onclick="openPicker('CM1')"><span class="slot-pos">CMF</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-AMF" onclick="openPicker('AMF')"><span class="slot-pos">AMF</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-CM2" onclick="openPicker('CM2')"><span class="slot-pos">CMF</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-LWF" onclick="openPicker('LWF')"><span class="slot-pos">LWF</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-CF" onclick="openPicker('CF')"><span class="slot-pos">CF</span><span class="slot-add">+</span></div>
        <div class="slot" id="pos-RWF" onclick="openPicker('RWF')"><span class="slot-pos">RWF</span><span class="slot-add">+</span></div>
    </div>

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

        // Полноценная база данных с ID
        const database = [
            // Вратари (GK)
            { id: 1, name: "P. Schmeichel", ovr: 99, pos: "GK", type: "Epic", club: "Man Utd" },
            { id: 2, name: "M. Neuer", ovr: 96, pos: "GK", type: "Highlight", club: "Bayern" },
            { id: 3, name: "G. Donnarumma", ovr: 94, pos: "GK", type: "Standard", club: "PSG" },
            { id: 4, name: "T. Courtois", ovr: 95, pos: "GK", type: "Standard", club: "Real Madrid" },
            { id: 5, name: "Y. Bounou", ovr: 92, pos: "GK", type: "Standard", club: "Al Hilal" },
            { id: 6, name: "O. Kahn", ovr: 98, pos: "GK", type: "Epic", club: "Bayern" },
            { id: 7, name: "E. van der Sar", ovr: 98, pos: "GK", type: "Epic", club: "Man Utd" },

            // Центральные защитники (CB)
            { id: 10, name: "P. Maldini", ovr: 100, pos: "CB", type: "Epic", club: "AC Milan" },
            { id: 11, name: "V. van Dijk", ovr: 97, pos: "CB", type: "Highlight", club: "Liverpool" },
            { id: 12, name: "A. Nesta", ovr: 98, pos: "CB", type: "Epic", club: "AC Milan" },
            { id: 13, name: "Rúben Dias", ovr: 95, pos: "CB", type: "Standard", club: "Man City" },
            { id: 14, name: "Marquinhos", ovr: 93, pos: "CB", type: "Standard", club: "PSG" },
            { id: 15, name: "R. Araújo", ovr: 94, pos: "CB", type: "Highlight", club: "Barcelona" },
            { id: 16, name: "F. Cannavaro", ovr: 99, pos: "CB", type: "Epic", club: "Italy" },
            { id: 17, name: "F. Rijkaard", ovr: 98, pos: "CB", type: "Epic", club: "AC Milan" },
            { id: 18, name: "E. Militao", ovr: 93, pos: "CB", type: "Standard", club: "Real Madrid" },

            // Левые защитники (LB)
            { id: 20, name: "Roberto Carlos", ovr: 98, pos: "LB", type: "Epic", club: "Real Madrid" },
            { id: 21, name: "T. Hernandez", ovr: 94, pos: "LB", type: "Highlight", club: "AC Milan" },
            { id: 22, name: "A. Davies", ovr: 93, pos: "LB", type: "Standard", club: "Bayern" },
            { id: 23, name: "D. Alaba", ovr: 93, pos: "LB", type: "Standard", club: "Real Madrid" },
            { id: 24, name: "P. Lahm", ovr: 97, pos: "LB", type: "Epic", club: "Bayern" },

            // Правые защитники (RB)
            { id: 30, name: "Cafu", ovr: 97, pos: "RB", type: "Epic", club: "AC Milan" },
            { id: 31, name: "A. Hakimi", ovr: 94, pos: "RB", type: "Highlight", club: "PSG" },
            { id: 32, name: "K. Walker", ovr: 92, pos: "RB", type: "Standard", club: "Man City" },
            { id: 33, name: "J. Koundé", ovr: 93, pos: "RB", type: "Highlight", club: "Barcelona" },
            { id: 34, name: "J. Zanetti", ovr: 98, pos: "RB", type: "Epic", club: "Inter" },

            // Центральные и Атакующие полузащитники (CMF / AMF)
            { id: 40, name: "Ruud Gullit", ovr: 101, pos: "AMF", type: "Epic", club: "AC Milan" },
            { id: 41, name: "K. De Bruyne", ovr: 97, pos: "AMF", type: "Highlight", club: "Man City" },
            { id: 42, name: "J. Bellingham", ovr: 98, pos: "AMF", type: "Highlight", club: "Real Madrid" },
            { id: 43, name: "Kaká", ovr: 99, pos: "AMF", type: "Epic", club: "AC Milan" },
            { id: 44, name: "Bruno Fernandes", ovr: 94, pos: "AMF", type: "Standard", club: "Man Utd" },
            { id: 45, name: "Z. Zidane", ovr: 101, pos: "AMF", type: "Epic", club: "Real Madrid" },
            
            { id: 50, name: "P. Vieira", ovr: 100, pos: "CMF", type: "Epic", club: "Arsenal" },
            { id: 51, name: "L. Modrić", ovr: 96, pos: "CMF", type: "Highlight", club: "Real Madrid" },
            { id: 52, name: "Pedri", ovr: 94, pos: "CMF", type: "Standard", club: "Barcelona" },
            { id: 53, name: "F. Valverde", ovr: 95, pos: "CMF", type: "Highlight", club: "Real Madrid" },
            { id: 54, name: "Rodri", ovr: 96, pos: "CMF", type: "Standard", club: "Man City" },
            { id: 55, name: "A. Pirlo", ovr: 99, pos: "CMF", type: "Epic", club: "AC Milan" },
            { id: 56, name: "S. Gerrard", ovr: 97, pos: "CMF", type: "Epic", club: "Liverpool" },

            // Нападающие (RWF, LWF, CF)
            { id: 60, name: "L. Messi", ovr: 102, pos: "RWF", type: "Epic", club: "Inter Miami" },
            { id: 61, name: "M. Salah", ovr: 96, pos: "RWF", type: "Highlight", club: "Liverpool" },
            { id: 62, name: "Lamine Yamal", ovr: 95, pos: "RWF", type: "Highlight", club: "Barcelona" },
            { id: 63, name: "B. Saka", ovr: 94, pos: "RWF", type: "Standard", club: "Arsenal" },
            { id: 64, name: "L. Figo", ovr: 97, pos: "RWF", type: "Epic", club: "Real Madrid" },

            { id: 70, name: "Ronaldinho", ovr: 100, pos: "LWF", type: "Epic", club: "Barcelona" },
            { id: 71, name: "Vini Jr.", ovr: 97, pos: "LWF", type: "Highlight", club: "Real Madrid" },
            { id: 72, name: "K. Mbappé", ovr: 98, pos: "LWF", type: "Highlight", club: "Real Madrid" },
            { id: 73, name: "K. Kvaratskhelia", ovr: 93, pos: "LWF", type: "Standard", club: "Napoli" },
            { id: 74, name: "Neymar Jr", ovr: 98, pos: "LWF", type: "Epic", club: "Santos" },

            { id: 80, name: "Ronaldo Nazário", ovr: 101, pos: "CF", type: "Epic", club: "Inter" },
            { id: 81, name: "C. Ronaldo", ovr: 98, pos: "CF", type: "Epic", club: "Al Nassr" },
            { id: 82, name: "E. Haaland", ovr: 97, pos: "CF", type: "Highlight", club: "Man City" },
            { id: 83, name: "H. Kane", ovr: 96, pos: "CF", type: "Standard", club: "Bayern" },
            { id: 84, name: "M. van Basten", ovr: 99, pos: "CF", type: "Epic", club: "AC Milan" },
            { id: 85, name: "A. Shevchenko", ovr: 98, pos: "CF", type: "Epic", club: "AC Milan" },
            { id: 86, name: "R. Lewandowski", ovr: 95, pos: "CF", type: "Standard", club: "Barcelona" }
        ];

        let currentSlot = null;
        let squad = {}; // Выбранные игроки по слотам

        function openPicker(slotId) {
            currentSlot = slotId;
            // Очищаем позицию от цифр (CB1 -> CB, CM1 -> CMF)
            let basePos = slotId.replace(/[0-9]/g, '');
            if (basePos === "CM") basePos = "CMF";

            // 1. Фильтруем свободных игроков (тех, кого еще НЕТ в составе)
            const chosenIds = Object.values(squad).map(p => p.id);
            let available = database.filter(p => !chosenIds.includes(p.id));

            // 2. Ищем именно по нужной позиции
            let pool = available.filter(p => p.pos === basePos);

            // Если доступных игроков на конкретную позицию меньше 5, добавляем смежных или любых доступных
            if (pool.length < 5) {
                let extra = available.filter(p => p.pos !== basePos);
                pool = pool.concat(extra);
            }

            // Перемешиваем и берём 5 случайных
            const shuffled = [...pool].sort(() => 0.5 - Math.random());
            const options = shuffled.slice(0, 5);

            const container = document.getElementById('card-options');
            container.innerHTML = '';
            document.getElementById('modal-heading').innerText = "Выбери игрока на " + basePos;

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
                card.onclick = () => selectPlayer(p);
                container.appendChild(card);
            });

            document.getElementById('picker-modal').style.display = 'flex';
        }

        function selectPlayer(player) {
            squad[currentSlot] = player;
            
            const slotEl = document.getElementById(`pos-${currentSlot}`);
            slotEl.classList.add('filled');
            
            // Красиво сокращаем название позиции для слота
            let displayPos = player.pos;
            slotEl.innerHTML = `
                <div class="slot-pos">${displayPos}</div>
                <div class="slot-name">${player.name}</div>
                <div class="slot-ovr">${player.ovr}</div>
            `;

            document.getElementById('picker-modal').style.display = 'none';
            calculateTeamOVR();
        }

        function calculateTeamOVR() {
            const players = Object.values(squad);
            if (players.length === 0) return;
            const total = players.reduce((sum, p) => sum + p.ovr, 0);
            document.getElementById('team-ovr').innerText = total;
            
            if (players.length === 11) {
                document.getElementById('team-style').innerText = "100";
                if(window.Telegram && window.Telegram.WebApp) {
                    window.Telegram.WebApp.HapticFeedback.notificationOccurred('success');
                }
            }
        }
    </script>
</body>
</html>
