<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fazendinha Pixel RPG - Ultimate Mutations Update</title>
    <style>
        * { box-sizing: border-box; user-select: none; image-rendering: pixelated; }
        body { margin: 0; padding: 0; background-color: #1a2e10; font-family: 'Courier New', Courier, monospace; overflow: hidden; }
       
        /* UI Superior */
        #ui-top { position: absolute; top: 15px; left: 15px; background: #2c1e14; color: #f4ecd8; padding: 12px; border-radius: 2px; border: 4px solid #6b4c35; z-index: 100; min-width: 340px; box-shadow: 0 6px 0px #140d08; }
        #ui-top h2 { margin: 0 0 6px 0; color: #ffcc00; font-size: 14px; text-align: center; border-bottom: 3px double #4e3522; padding-bottom: 4px; text-transform: uppercase; text-shadow: 2px 2px #000; }
        #ui-top p { margin: 4px 0; font-size: 11px; font-weight: bold; display: flex; justify-content: space-between; text-shadow: 1px 1px #000; }
        #shop-timer { color: #ff5555; font-size: 11px; font-weight: bold; margin-top: 6px; text-align: center; background: #140808; padding: 2px; border: 2px solid #4a1515; }
       
        /* Barra de Vida Jogador */
        .player-hp-container { margin-bottom: 8px; background: #000; border: 2px solid #444; padding: 2px; height: 20px; position: relative; }
        .player-hp-fill { height: 100%; width: 100%; background: linear-gradient(to bottom, #ff2222, #aa0000); transition: width 0.1s; }
        .player-hp-text { position: absolute; width: 100%; text-align: center; top: 1px; left: 0; font-size: 11px; font-weight: bold; color: #fff; text-shadow: 1px 1px #000; }

        .btn-mochila { width: 100%; margin-top: 6px; background: #4a5da3; color: white; border: 2px solid #232e5c; border-bottom-width: 4px; padding: 5px; font-weight: bold; cursor: pointer; font-size: 11px; text-transform: uppercase; text-shadow: 1px 1px #000; }
        .btn-mochila:active { border-bottom-width: 2px; margin-top: 8px; margin-bottom: -2px; }

        /* PAINEL DE ADM DEUS AVANÇADO */
        #adm-trigger { position: absolute; top: 15px; right: 15px; background: #990000; color: #fff; border: 3px solid #ff3333; padding: 8px 12px; font-weight: bold; font-size: 12px; cursor: pointer; z-index: 200; box-shadow: 0 4px 0 #440000; text-transform: uppercase; }
        #adm-panel { display: none; position: absolute; top: 60px; right: 15px; background: #111116; border: 4px solid #ff3333; padding: 12px; z-index: 200; width: 320px; color: #00ff00; box-shadow: 0 6px 12px rgba(0,0,0,0.8); max-height: 85vh; overflow-y: auto; }
        #adm-panel h3 { margin: 0 0 10px 0; font-size: 14px; text-align: center; color: #ff3333; text-transform: uppercase; border-bottom: 2px solid #ff3333; padding-bottom: 4px; }
        .adm-row { display: flex; align-items: center; justify-content: space-between; margin-bottom: 8px; font-size: 11px; gap: 4px; }
        .adm-row label { width: 40%; color: #aaa; }
        .adm-input { width: 60%; background: #222; border: 1px solid #ff3333; color: #00ff00; padding: 3px; font-family: inherit; font-size: 11px; }
        .adm-btn-block { width: 100%; background: #ff3333; color: black; border: none; padding: 6px; font-weight: bold; cursor: pointer; text-transform: uppercase; font-family: inherit; font-size: 11px; margin-top: 5px; margin-bottom: 5px; }
        .adm-btn-block:hover { background: #ff6666; }
        .adm-btn-small { background: #222; color: #00ff00; border: 1px solid #00ff00; padding: 2px 6px; cursor: pointer; font-size: 10px; }
        .adm-btn-small:hover { background: #003300; }

        /* Hotbar Rápida */
        #hotbar-container { position: absolute; bottom: 20px; left: 50%; transform: translateX(-50%); display: flex; gap: 4px; background: #18120c; padding: 6px; border: 4px solid #4a3319; z-index: 100; box-shadow: 0 6px 0 #000; }
        .hotbar-slot { width: 52px; height: 52px; background: #362517; border: 3px solid #22160e; display: flex; flex-direction: column; align-items: center; justify-content: center; position: relative; cursor: pointer; }
        .hotbar-slot.selected { border-color: #ffaa00; background: #4e3522; transform: scale(1.05); }
        .hotbar-slot .slot-num { position: absolute; top: 1px; left: 2px; font-size: 9px; color: #8c735b; }
        .hotbar-slot .slot-icon { font-size: 20px; margin-top: 2px; }
        .hotbar-slot .slot-qty { position: absolute; bottom: 1px; right: 2px; font-size: 10px; color: #ffcc00; font-weight: bold; text-shadow: 1px 1px #000; }
        .hotbar-slot .slot-lvl { position: absolute; top: 1px; right: 2px; font-size: 9px; color: #5cc8ff; font-weight: bold; text-shadow: 1px 1px #000; }

        /* Canvases */
        #game-canvas { width: 100vw; height: 100vh; position: absolute; top: 0; left: 0; background: #4d7a34; background-image: radial-gradient(#56873b 20%, transparent 20%); background-size: 16px 16px; z-index: 1; }
        #game-canvas.dungeon-mode { background: #1d1d24; background-image: linear-gradient(45deg, #16161c 25%, transparent 25%); background-size: 32px 32px; }
       
        /* Entidades */
        #player { position: absolute; width: 42px; height: 52px; background: linear-gradient(to bottom, #7fa4ff 20%, #ffcc55 20%, #ffcc55 50%, #444 50%, #333 80%); border: 3px solid #111; border-radius: 4px; display: flex; align-items: center; justify-content: center; font-size: 20px; z-index: 50; box-shadow: 0 6px 0 rgba(0,0,0,0.2); }
        #weapon-render { position: absolute; right: -12px; top: 8px; font-size: 18px; transition: transform 0.1s; transform-origin: bottom left; filter: drop-shadow(2px 2px 0px #000); }

        .character { position: absolute; width: 50px; height: 50px; font-size: 24px; z-index: 10; border: 3px solid #1a110b; display: flex; align-items: center; justify-content: center; box-shadow: 0 4px 0 #000; }
        .npc-label { position: absolute; top: -24px; background: #1c120c; color: #f4ecd8; padding: 2px 5px; border: 2px solid #573b24; font-size: 9px; font-weight: bold; white-space: nowrap; }
       
        #farm-zones { position: absolute; top: 160px; left: 340px; display: flex; flex-wrap: wrap; gap: 8px; max-width: calc(100vw - 720px); z-index: 5; }
        .plot { width: 72px; height: 72px; background: #422a18; border: 4px solid #26170c; position: relative; display: flex; align-items: center; justify-content: center; cursor: pointer; box-shadow: inset 0 4px 6px #000; }
       
        /* Vida dos Mobs */
        .dungeon-ent { position: absolute; width: 40px; height: 40px; display: flex; align-items: center; justify-content: center; font-size: 22px; z-index: 20; transition: left 0.1s linear, top 0.1s linear; }
        .dungeon-ent .ent-weapon { position: absolute; right: -8px; bottom: 4px; font-size: 12px; }
        .hp-bar { position: absolute; top: -12px; width: 120%; left: -10%; height: 6px; background: #220000; border: 1px solid #000; }
        .hp-fill { height: 100%; background: #e61919; width: 100%; transition: width 0.2s ease-out; }

        /* Projéteis */
        .projectile { position: absolute; width: 10px; height: 10px; display: flex; align-items: center; justify-content: center; font-size: 12px; z-index: 60; pointer-events: none; }

        /* ENGINE DE MUTAÇÕES REESTRUTURADA */
        .crop { font-size: 30px; display: flex; flex-direction: column; align-items: center; transition: all 0.3s ease; }
        .crop.growing { filter: grayscale(80%) brightness(40%) scale(0.6) !important; animation: none !important; }
       
        /* Mutação Clássica Ouro */
        .crop.golden { filter: drop-shadow(0px 0px 6px #ffd700) brightness(1.3) contrast(1.1); animation: goldPulse 1.5s infinite alternate ease-in-out; }
        @keyframes goldPulse { 0% { transform: scale(1) rotate(0deg); } 100% { transform: scale(1.15) rotate(5deg); } }

        /* Mutação Congelada */
        .crop.frozen { filter: drop-shadow(0px 0px 8px #70c5ff) hue-rotate(140deg) brightness(1.1); animation: freezeShudder 1.8s infinite linear; }
        @keyframes freezeShudder { 0%, 100% { transform: translateX(0); } 25% { transform: translateX(-1px) translateY(1px); } 75% { transform: translateX(1px) translateY(-1px); } }

        /* Mutação Divina Mítica */
        .crop.divine { filter: drop-shadow(0px 0px 12px #ff00ff) saturate(2) brightness(1.4); animation: divineFloat 1.2s infinite alternate ease-in-out; }
        @keyframes divineFloat { 0% { transform: translateY(0px) scale(1); } 100% { transform: translateY(-8px) scale(1.1); } }

        /* NOVA: Radioativa */
        .crop.radioactive { filter: drop-shadow(0px 0px 10px #39ff14) hue-rotate(90deg) saturate(3); animation: radTremble 0.3s infinite linear; }
        @keyframes radTremble { 0% { transform: translate(1px, 1px) rotate(0deg); } 50% { transform: translate(-1px, -1px) rotate(1deg); } 100% { transform: translate(1px, -2px) rotate(-1deg); } }

        /* NOVA: Infernal */
        .crop.infernal { filter: drop-shadow(0px 0px 12px #ff3300) brightness(1.2) contrast(1.5); animation: flamePulse 0.8s infinite alternate ease; }
        @keyframes flamePulse { 0% { transform: scale(1); filter: drop-shadow(0px 0px 6px #ff3300) saturate(1.5); } 100% { transform: scale(1.1); filter: drop-shadow(0px 0px 16px #ff6600) saturate(2.5); } }

        /* NOVA: Cósmica */
        .crop.cosmic { filter: drop-shadow(0px 0px 14px #00ffff) hue-rotate(240deg) brightness(1.3); animation: cosmicOrbit 3s infinite linear; }
        @keyframes cosmicOrbit { 0% { transform: rotate(0deg) translateY(-2px) rotate(0deg); } 50% { transform: rotate(180deg) translateY(2px) rotate(-180deg); } 100% { transform: rotate(360deg) translateY(-2px) rotate(-360deg); } }

        /* NOVA: Cibernética */
        .crop.cyber { filter: drop-shadow(0px 0px 8px #00f0ff) saturate(2); animation: cyberGlitch 1.5s infinite steps(2); }
        @keyframes cyberGlitch { 0%, 90% { transform: skewX(0deg); opacity: 1; } 95% { transform: skewX(10deg); opacity: 0.85; } 100% { transform: skewX(-5deg); opacity: 0.95; } }

        /* NOVA: Abissal */
        .crop.void { filter: drop-shadow(0px 0px 15px #000) brightness(0.2) contrast(2); animation: voidSuck 2s infinite ease-in-out; }
        @keyframes voidSuck { 0%, 100% { transform: scale(0.9); filter: drop-shadow(0px 0px 5px #4a0099); } 50% { transform: scale(1.1); filter: drop-shadow(0px 0px 20px #000); } }

        /* NOVA: Temporal */
        .crop.chrono { filter: drop-shadow(0px 0px 10px #ffcc00) sepia(0.6); animation: chronoStrobe 4s infinite linear; }
        @keyframes chronoStrobe { 0%, 100% { transform: rotate(0deg); opacity: 1; } 50% { transform: rotate(180deg); opacity: 0.6; } }

        /* NOVA: Quântica */
        .crop.quantum { filter: drop-shadow(0px 0px 18px #ffffff) invert(0.2); animation: quantumShift 0.4s infinite alternate ease-in-out; }
        @keyframes quantumShift { 0% { transform: scale(0.85) skewY(-2deg); opacity: 0.4; } 100% { transform: scale(1.15) skewX(2deg); opacity: 1; } }
       
        .timer-bar { position: absolute; bottom: 4px; left: 4px; width: calc(100% - 8px); height: 5px; background: #111; }
        .timer-fill { height: 100%; width: 0%; background: #38b349; }

        /* Modais */
        .modal { display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background: #291c12; border: 4px solid #543a24; padding: 18px; z-index: 200; width: 92%; max-width: 560px; box-shadow: 0 10px 0px #000; color: #f4ecd8; }
        .modal-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 3px double #402b1b; padding-bottom: 6px; margin-bottom: 10px; }
        .modal-header h3 { margin: 0; color: #ffcc00; font-size: 13px; text-transform: uppercase; }
        .close-btn { background: #912323; color: white; border: 2px solid #4a1212; padding: 3px 8px; cursor: pointer; font-family: inherit; font-weight: bold; }
       
        .shop-tabs { display: flex; gap: 4px; margin-bottom: 10px; }
        .tab-btn { flex: 1; padding: 5px; background: #3d281a; border: 2px solid #1a100a; color: #bfaea0; cursor: pointer; font-size: 11px; font-family: inherit; text-transform: uppercase; }
        .tab-btn.active { background: #735135; color: #fff; border-color: #ffaa00; }

        .shop-grid { display: grid; grid-template-columns: 1fr; gap: 5px; }
        .shop-item { display: flex; align-items: center; justify-content: space-between; padding: 8px; border: 2px solid #3d281a; background: #21160e; }
        .item-info { display: flex; align-items: center; gap: 8px; font-size: 11px; }
        .buy-btn { background: #2b5c16; color: white; border: 2px solid #142e09; border-bottom-width: 3px; padding: 4px 8px; cursor: pointer; font-family: inherit; font-weight: bold; font-size: 11px; }
        .buy-btn:disabled { background: #3a3a3a; border-color: #111; color: #666; cursor: not-allowed; }
    </style>
</head>
<body>

    <div id="ui-top">
        <h2>Fazenda Retrô Agrinho</h2>
       
        <div class="player-hp-container">
            <div id="hp-bar" class="player-hp-fill"></div>
            <div id="hp-text" class="player-hp-text">HP: 100 / 100</div>
        </div>

        <p><span>🪙 Moedas:</span> <span id="coin-count">150</span></p>
        <p><span>⚔️ Arma Equipada:</span> <span id="weapon-status" style="color: #ffaa00;">Espada de Ferro</span></p>
        <p><span>🌱 Item Ativo:</span> <span id="selected-seed-text">Nenhum</span></p>
        <button class="btn-mochila" onclick="openBackpackModal()">🎒 Ver Mochila & Forja [Space]</button>
        <div id="shop-timer">Loja reestoca em: 03:00</div>
    </div>

    <button id="adm-trigger" onclick="toggleAdmPanel()">🛠️ Painel ADM</button>
    <div id="adm-panel">
        <h3>Menu Deus Hack v3.0</h3>
       
        <button class="adm-btn-block" style="background:#00ff00; color:#000;" onclick="aplicarAlteracoesADM()">⚡ Aplicar Modificações</button>

        <div class="adm-row">
            <label>🪙 Moedas:</label>
            <input type="number" id="adm-coins" class="adm-input" value="150">
        </div>
        <div class="adm-row">
            <label>❤️ HP Atual:</label>
            <input type="number" id="adm-hp" class="adm-input" value="100">
        </div>
        <div class="adm-row">
            <label>❤️ HP Máximo:</label>
            <input type="number" id="adm-maxhp" class="adm-input" value="100">
        </div>
        <div class="adm-row">
            <label>⚔️ Dano Atual:</label>
            <input type="number" id="adm-dmg" class="adm-input" value="18">
        </div>
        <div class="adm-row">
            <label>😈 Andar Masmorra:</label>
            <input type="number" id="adm-floor" class="adm-input" value="1">
        </div>
        <div class="adm-row">
            <label>🟫 Qtd Lotes (Max 15):</label>
            <input type="number" id="adm-plots" class="adm-input" value="6">
        </div>

        <hr style="border: 1px dashed #ff3333; margin: 10px 0;">
        <span style="font-size:10px; color:#ffcc00; display:block; text-align:center; font-weight:bold; margin-bottom:5px;">GERADOR DE RECURSOS INSTANTÂNEO</span>
       
        <div class="adm-row">
            <select id="adm-spawn-select" class="adm-input" style="width:70%;">
                <option value="maca_semente">Semente Maçã 🍎</option>
                <option value="banana_semente">Semente Banana 🍌</option>
                <option value="cenoura_semente">Semente Cenoura 🥕</option>
                <option value="estrela_semente">Semente Estrela ⭐</option>
                <option value="divina_semente">Semente Divina 👑</option>
                <option value="gold_fertilizer">Néctar Ouro ✨</option>
                <option value="ferro">Minério Ferro 🪨</option>
                <option value="ouro">Minério Ouro 🌟</option>
            </select>
            <button class="adm-btn-small" style="width:28%;" onclick="admInjetarItemSelecionado()">+5 Itens</button>
        </div>

        <hr style="border: 1px dashed #ff3333; margin: 10px 0;">

        <button class="adm-btn-block" style="background:#222; color:#ff3333; border:1px solid #ff3333;" onclick="admToggleInvencivel()">🛡️ Modo Invencível: <span id="adm-god-txt">OFF</span></button>
        <button class="adm-btn-block" style="background:#551a8b; color:#fff;" onclick="admSpawnBossImediato()">👑 Forçar Spawn de Boss Local</button>
    </div>

    <div id="hotbar-container">
        <div class="hotbar-slot selected" id="slot-0" onclick="selectSlot(0)"><span class="slot-num">1</span><span class="slot-icon"></span><span class="slot-qty"></span><span class="slot-lvl"></span></div>
        <div class="hotbar-slot" id="slot-1" onclick="selectSlot(1)"><span class="slot-num">2</span><span class="slot-icon"></span><span class="slot-qty"></span><span class="slot-lvl"></span></div>
        <div class="hotbar-slot" id="slot-2" onclick="selectSlot(2)"><span class="slot-num">3</span><span class="slot-icon"></span><span class="slot-qty"></span><span class="slot-lvl"></span></div>
        <div class="hotbar-slot" id="slot-3" onclick="selectSlot(3)"><span class="slot-num">4</span><span class="slot-icon"></span><span class="slot-qty"></span><span class="slot-lvl"></span></div>
        <div class="hotbar-slot" id="slot-4" onclick="selectSlot(4)"><span class="slot-num">5</span><span class="slot-icon"></span><span class="slot-qty"></span><span class="slot-lvl"></span></div>
        <div class="hotbar-slot" id="slot-5" onclick="selectSlot(5)"><span class="slot-num">6</span><span class="slot-icon"></span><span class="slot-qty"></span><span class="slot-lvl"></span></div>
        <div class="hotbar-slot" id="slot-6" onclick="selectSlot(6)"><span class="slot-num">7</span><span class="slot-icon"></span><span class="slot-qty"></span><span class="slot-lvl"></span></div>
        <div class="hotbar-slot" id="slot-7" onclick="selectSlot(7)"><span class="slot-num">8</span><span class="slot-icon"></span><span class="slot-qty"></span><span class="slot-lvl"></span></div>
    </div>

    <div id="game-canvas">
        <div id="player" style="top: 250px; left: 200px;">
            🤠
            <div id="weapon-render">⚔️</div>
        </div>
       
        <div id="npc-tiao" class="character" style="top: 25px; left: 360px; background: #2f4560;">🏪<span class="npc-label">Tião [E]</span></div>
        <div id="npc-mago" class="character" style="top: 25px; left: 540px; background: #50287a;">🧙‍♂️<span class="npc-label">Mago [E]</span></div>
        <div id="npc-chico" class="character" style="top: 25px; left: 720px; background: #7a431d;">👨‍💼<span class="npc-label">Chico [E]</span></div>
       
        <div id="npc-dungeon" class="character" style="top: 450px; left: 1100px; background: #14111f; border-color: #991b1b;">😈<span class="npc-label" style="border-color:#b91c1c; color:#f87171;">Masmorra [E]</span></div>
       
        <div id="farm-zones"></div>
        <div id="dungeon-entities-layer"></div>
        <div id="projectiles-layer"></div>
    </div>

    <div id="game-modal" class="modal">
        <div class="modal-header">
            <h3 id="modal-title">Painel</h3>
            <button class="close-btn" onclick="closeModal()">X</button>
        </div>
        <div class="shop-tabs" id="modal-tabs"></div>
        <div id="modal-content" class="shop-grid"></div>
    </div>

    <script>
        let player = { x: 200, y: 250, speed: 5, inDungeon: false, weapon: 'espada', hp: 100, maxHp: 100 };
        let gameState = { coins: 150, maxPlots: 6, speedMultiplier: 1.0, weightBonus: 0, mutationChance: 0.25, activeHotbarSlot: 0, godMode: false };
       
        let arsenal = {
            espada: { name: "Espada de Ferro", icon: "⚔️", level: 1, damage: 18, range: 70, speed: 200 },
            lança: { name: "Lança Pontuda", icon: "🔱", level: 1, damage: 22, range: 120, speed: 350 },
            arco: { name: "Arco de Caça", icon: "🏹", level: 1, damage: 12, range: 90, speed: 150 },
            picareta: { name: "Picareta Rústica", icon: "⛏️", level: 1, damage: 14, range: 60, speed: 250 }
        };

        let minerios = { ferro: 0, ouro: 0 };
        let backpack = {};
       
        let hotbar = [
            { id: 'maca_semente', qty: 5, type: 'semente', level: 1, data: null },
            { id: 'banana_semente', qty: 3, type: 'semente', level: 1, data: null },
            { id: 'grow_fertilizer', qty: 2, type: 'fertilizante', level: 1, data: null },
            { id: null, qty: 0, type: '', level: 0, data: null },
            { id: null, qty: 0, type: '', level: 0, data: null },
            { id: null, qty: 0, type: '', level: 0, data: null },
            { id: null, qty: 0, type: '', level: 0, data: null },
            { id: null, qty: 0, type: '', level: 0, data: null }
        ];

        let dungeon = { floor: 1, entities: [] };
        let projectiles = [];
        let keysPressed = {};
        let currentStoreSeeds = [];
        let shopTimer = 180;
        let plots = [];
        let isAttacking = false;
        let currentModalNPC = null;

        const fruitData = {
            maca: { name: "Maçã", icon: "🍎", basePrice: 15, time: 4, weightMin: 0.3 },
            banana: { name: "Banana", icon: "🍌", basePrice: 20, time: 6, weightMin: 0.2 },
            cenoura: { name: "Cenoura", icon: "🥕", basePrice: 22, time: 5, weightMin: 0.15 },
            laranja: { name: "Laranja", icon: "🍊", basePrice: 25, time: 8, weightMin: 0.4 },
            tomate: { name: "Tomate", icon: "🍅", basePrice: 30, time: 10, weightMin: 0.25 },
            melancia: { name: "Melancia", icon: "🍉", basePrice: 50, time: 16, weightMin: 2.0 },
            estrela: { name: "Fruta Estrela", icon: "⭐", basePrice: 150, time: 35, weightMin: 0.3 },
            divina: { name: "Divindade Mítica", icon: "👑", basePrice: 400, time: 50, weightMin: 1.0 }
        };

        /* MAPEAMENTO DE MULTIPLICADORES DE RARIDADE */
        const mutationConfig = {
            golden: { label: "Dourada ✨", mult: 3.0, bg: "#5e4b11" },
            frozen: { label: "Congelada ❄️", mult: 2.2, bg: "#1a3a4a" },
            divine: { label: "Divina 🌌", mult: 6.0, bg: "#4a124a" },
            radioactive: { label: "Radioativa ☢️", mult: 4.5, bg: "#1e3d11" },
            infernal: { label: "Infernal 🔥", mult: 7.0, bg: "#4d1414" },
            cosmic: { label: "Cósmica 🪐", mult: 12.0, bg: "#141c4d" },
            cyber: { label: "Cibernética ⚡", mult: 3.5, bg: "#113d42" },
            void: { label: "Abissal 🔮", mult: 9.5, bg: "#0d0517" },
            chrono: { label: "Temporal ⏳", mult: 5.0, bg: "#3d3311" },
            quantum: { label: "Quântica ⚛️", mult: 15.0, bg: "#4f4f4f" }
        };

        const fertData = {
            grow_fertilizer: { name: "Fertilizante Sônico", icon: "🧪", price: 20, desc: "Acelera o tempo atual em 50%." },
            gold_fertilizer: { name: "Néctar de Ouro", icon: "✨", price: 45, desc: "Força mutação dourada imediata." },
            super_nutrient: { name: "Super Nutriente", icon: "🧬", price: 60, desc: "Aumenta o Nível (Lvl) da Semente plantada." }
        };

        function init() {
            createPlots();
            refreshShopSeeds();
            syncAdmPanelInputs();
            updateUI();
            updateHotbarVisuals();

            setInterval(() => {
                if (shopTimer > 0) shopTimer--;
                else { refreshShopSeeds(); shopTimer = 180; }
                let mins = Math.floor(shopTimer / 60), secs = shopTimer % 60;
                let tEl = document.getElementById('shop-timer');
                if (tEl) tEl.innerText = `Loja reestoca em: ${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
            }, 1000);

            setInterval(() => {
                plots.forEach(plot => {
                    if (plot.status === 'growing') {
                        plot.timer -= 0.1;
                        let levelBonus = 1 - ((plot.seedLevel - 1) * 0.15);
                        let totalTime = fruitData[plot.fruit].time * gameState.speedMultiplier * levelBonus;
                        let progress = Math.min(((totalTime - plot.timer) / totalTime) * 100, 100);
                        let fillBar = plot.element.querySelector('.timer-fill');
                        if (fillBar) fillBar.style.width = `${progress}%`;

                        if (plot.timer <= 0) {
                            plot.status = 'ready';
                            let cropDiv = plot.element.querySelector('.crop');
                            if (cropDiv) {
                                cropDiv.classList.remove('growing');
                                if (plot.mutationType) cropDiv.classList.add(plot.mutationType);
                            }
                            let timerBar = plot.element.querySelector('.timer-bar');
                            if (timerBar) timerBar.remove();
                        }
                    }
                });

                if (player.inDungeon) {
                    dungeonLogicUpdate();
                    projectilesUpdate();
                }
            }, 100);

            window.addEventListener('keydown', e => {
                if (e.code === 'Space') {
                    if(document.activeElement.tagName === 'INPUT' || document.activeElement.tagName === 'SELECT') return;
                    e.preventDefault();
                    if (currentModalNPC === 'mochila') closeModal();
                    else openBackpackModal();
                    return;
                }
                keysPressed[e.key.toLowerCase()] = true;
                if (['1','2','3','4','5','6','7','8'].includes(e.key)) selectSlot(parseInt(e.key) - 1);
                if (e.key.toLowerCase() === 'e') handleNPCDetection();
                if (e.key.toLowerCase() === 'f') executeAttack();
            });
            window.addEventListener('keyup', e => { keysPressed[e.key.toLowerCase()] = false; });

            gameLoop();
        }

        function toggleAdmPanel() {
            let panel = document.getElementById('adm-panel');
            panel.style.display = panel.style.display === 'block' ? 'none' : 'block';
            if(panel.style.display === 'block') syncAdmPanelInputs();
        }

        function syncAdmPanelInputs() {
            document.getElementById('adm-coins').value = Math.floor(gameState.coins);
            document.getElementById('adm-hp').value = Math.floor(player.hp);
            document.getElementById('adm-maxhp').value = player.maxHp;
            document.getElementById('adm-dmg').value = arsenal[player.weapon].damage;
            document.getElementById('adm-floor').value = dungeon.floor;
            document.getElementById('adm-plots').value = gameState.maxPlots;
        }

        function aplicarAlteracoesADM() {
            gameState.coins = parseFloat(document.getElementById('adm-coins').value) || 0;
            player.maxHp = parseInt(document.getElementById('adm-maxhp').value) || 100;
            player.hp = Math.min(parseInt(document.getElementById('adm-hp').value) || 100, player.maxHp);
            arsenal[player.weapon].damage = parseInt(document.getElementById('adm-dmg').value) || 1;
           
            let oldPlots = gameState.maxPlots;
            gameState.maxPlots = Math.min(parseInt(document.getElementById('adm-plots').value) || 6, 15);
            if(oldPlots !== gameState.maxPlots) createPlots();

            let targetFloor = parseInt(document.getElementById('adm-floor').value) || 1;
            if (targetFloor !== dungeon.floor) {
                dungeon.floor = targetFloor;
                if(player.inDungeon) generateDungeonFloor();
            }

            updateUI();
            alert("🔮 Alterações injetadas com sucesso no motor gráfico do jogo!");
        }

        function admInjetarItemSelecionado() {
            let itemKey = document.getElementById('adm-spawn-select').value;
            if(itemKey === 'ferro' || itemKey === 'ouro') {
                minerios[itemKey] += 5;
                alert(`✨ Adicionado +5 de Minério de ${itemKey} na forja.`);
            } else if (itemKey.includes('semente')) {
                addItemToCollectedHotbar(itemKey, 'semente', 1, null);
                alert(`🌱 Adicionado 5 sementes de ${itemKey.split('_')[0]} na sua hotbar.`);
            } else if (itemKey.includes('fertilizer')) {
                addItemToCollectedHotbar(itemKey, 'fertilizante', 1, null);
                alert(`🧪 Adicionado fertilizante na sua hotbar.`);
            }
            updateHotbarVisuals();
            updateUI();
        }

        function admToggleInvencivel() {
            gameState.godMode = !gameState.godMode;
            let txt = document.getElementById('adm-god-txt');
            if (gameState.godMode) { txt.innerText = "ON"; txt.style.color = "#00ff00"; player.hp = player.maxHp; }
            else { txt.innerText = "OFF"; txt.style.color = "#ff3333"; }
            updateUI();
        }

        function admSpawnBossImediato() {
            if (!player.inDungeon) return alert("Entre na Masmorra primeiro para invocar!");
            let layer = document.getElementById('dungeon-entities-layer');
            layer.innerHTML = '';
            dungeon.entities = [];
            spawnEntity('Chefão Invocado ADM 👑', 600, 250, 600, "👾", true, '', 'final_boss');
        }

        function createPlots() {
            const container = document.getElementById('farm-zones');
            container.innerHTML = '';
            let oldPlots = [...plots];
            plots = [];

            for(let i = 0; i < Math.min(gameState.maxPlots, 15); i++) {
                let plotDiv = document.createElement('div');
                plotDiv.className = 'plot';
                plotDiv.id = `plot-${i}`;
                plotDiv.innerHTML = `<span style="color:#26170c; font-size:9px; position:absolute; bottom:4px; font-weight:bold;">Terra</span>`;
                container.appendChild(plotDiv);

                if (oldPlots[i]) {
                    plots.push(oldPlots[i]); plots[i].element = plotDiv;
                    if (plots[i].status !== 'empty') renderPlotAsset(plots[i]);
                } else {
                    plots.push({ element: plotDiv, status: 'empty', fruit: null, timer: 0, mutationType: null, weight: 0, seedLevel: 1 });
                }
            }
            plots.forEach((p, idx) => p.element.onclick = () => handlePlotInteraction(idx));
        }

        function renderPlotAsset(plot) {
            let data = fruitData[plot.fruit];
            let growingClass = plot.status === 'growing' ? 'growing' : '';
            let mutationClass = (plot.status === 'ready' && plot.mutationType) ? plot.mutationType : '';
           
            plot.element.innerHTML = `
                <div class="crop ${growingClass} ${mutationClass}">${data.icon}</div>
                <span style="color:#26170c; font-size:9px; position:absolute; bottom:4px; font-weight:bold;">Lvl ${plot.seedLevel}</span>
            `;
           
            if (plot.status === 'growing') {
                let timerBar = document.createElement('div');
                timerBar.className = 'timer-bar';
                timerBar.innerHTML = `<div class="timer-fill"></div>`;
                plot.element.appendChild(timerBar);
            }
        }

        function selectSlot(index) {
            gameState.activeHotbarSlot = index;
            document.querySelectorAll('.hotbar-slot').forEach((s, idx) => s.classList.toggle('selected', idx === index));
            updateUI();
        }

        function updateHotbarVisuals() {
            for (let i = 0; i < 8; i++) {
                let slot = document.getElementById(`slot-${i}`);
                let item = hotbar[i];
                if (item && item.id) {
                    let baseKey = item.id.split('_')[0];
                    if (item.type === 'semente') {
                        slot.querySelector('.slot-icon').innerText = fruitData[baseKey] ? fruitData[baseKey].icon : "🌱";
                        slot.querySelector('.slot-qty').innerText = "🌱" + item.qty;
                        slot.querySelector('.slot-lvl').innerText = "L." + item.level;
                        slot.style.background = "#362517";
                    } else if (item.type === 'fruta') {
                        slot.querySelector('.slot-icon').innerText = item.data.icon;
                        slot.querySelector('.slot-qty').innerText = "x" + item.qty;
                        slot.querySelector('.slot-lvl').innerText = "";
                       
                        if (item.data.mutation && mutationConfig[item.data.mutation]) {
                            slot.style.background = mutationConfig[item.data.mutation].bg;
                        } else {
                            slot.style.background = "#362517";
                        }
                    } else if (item.type === 'fertilizante') {
                        slot.querySelector('.slot-icon').innerText = fertData[item.id].icon;
                        slot.querySelector('.slot-qty').innerText = "x" + item.qty;
                        slot.querySelector('.slot-lvl').innerText = "";
                        slot.style.background = "#362517";
                    } else if (item.type === 'minerio') {
                        slot.querySelector('.slot-icon').innerText = item.id === 'ferro' ? "🪨" : "✨";
                        slot.querySelector('.slot-qty').innerText = "x" + item.qty;
                        slot.querySelector('.slot-lvl').innerText = "";
                        slot.style.background = "#362517";
                    }
                } else {
                    slot.querySelector('.slot-icon').innerText = '';
                    slot.querySelector('.slot-qty').innerText = '';
                    slot.querySelector('.slot-lvl').innerText = '';
                    slot.style.background = "#362517";
                }
            }
        }

        function addItemToCollectedHotbar(itemKey, type, level, dataBlock) {
            let maxLoop = type === 'semente' ? 5 : 1;
            for(let k=0; k<maxLoop; k++) {
                let inserido = false;
                for (let i = 0; i < 8; i++) {
                    if (hotbar[i].id === itemKey && hotbar[i].type === type && hotbar[i].level === level) {
                        if (type === 'fruta') hotbar[i].data.price += dataBlock.price;
                        hotbar[i].qty++;
                        inserido = true; break;
                    }
                }
                if(!inserido) {
                    for (let i = 0; i < 8; i++) {
                        if (hotbar[i].id === null) {
                            hotbar[i].id = itemKey;
                            hotbar[i].qty = 1;
                            hotbar[i].type = type;
                            hotbar[i].level = level;
                            hotbar[i].data = dataBlock ? { ...dataBlock } : null;
                            inserido = true; break;
                        }
                    }
                }
                if (!inserido) {
                    if (!backpack[itemKey]) backpack[itemKey] = { qty: 0, level: level, type: type, data: dataBlock };
                    backpack[itemKey].qty++;
                }
            }
        }

        function handlePlotInteraction(index) {
            if (player.inDungeon) return;
            let plot = plots[index];
            let activeItem = hotbar[gameState.activeHotbarSlot];

            if (plot.status === 'empty' && activeItem && activeItem.type === 'semente' && activeItem.qty > 0) {
                plot.status = 'growing';
                plot.fruit = activeItem.id.split('_')[0];
                plot.seedLevel = activeItem.level;
               
                let levelBonus = 1 - ((plot.seedLevel - 1) * 0.15);
                plot.timer = fruitData[plot.fruit].time * gameState.speedMultiplier * levelBonus;
               
                // SISTEMA EXTENDIDO DE ROLAGEM DE TODAS AS 10 MUTAÇÕES ATIVAS
                let roll = Math.random();
                plot.mutationType = null;
               
                if (roll < 0.05) plot.mutationType = 'quantum';
                else if (roll < 0.09) plot.mutationType = 'cosmic';
                else if (roll < 0.13) plot.mutationType = 'void';
                else if (roll < 0.18) plot.mutationType = 'infernal';
                else if (roll < 0.23) plot.mutationType = 'divine';
                else if (roll < 0.28) plot.mutationType = 'chrono';
                else if (roll < 0.34) plot.mutationType = 'radioactive';
                else if (roll < 0.40) plot.mutationType = 'cyber';
                else if (roll < 0.46) plot.mutationType = 'golden';
                else if (roll < 0.52) plot.mutationType = 'frozen';

                plot.weight = (Math.random() * 1.5 + fruitData[plot.fruit].weightMin) + gameState.weightBonus;
                activeItem.qty--;
                if (activeItem.qty <= 0) hotbar[gameState.activeHotbarSlot] = { id: null, qty: 0, type: '', level: 0, data: null };

                renderPlotAsset(plot);
                updateUI();
                updateHotbarVisuals();
            }
            else if (plot.status === 'growing' && activeItem && activeItem.type === 'fertilizante' && activeItem.qty > 0) {
                if (activeItem.id === 'grow_fertilizer') {
                    plot.timer /= 2;
                } else if (activeItem.id === 'gold_fertilizer') {
                    plot.mutationType = 'golden';
                } else if (activeItem.id === 'super_nutrient') {
                    if(plot.seedLevel < 5) plot.seedLevel++;
                }
                activeItem.qty--;
                if (activeItem.qty <= 0) hotbar[gameState.activeHotbarSlot] = { id: null, qty: 0, type: '', level: 0, data: null };
                renderPlotAsset(plot);
                updateHotbarVisuals();
            }
            else if (plot.status === 'ready') {
                let mult = 1.0 + (plot.seedLevel * 0.5);
                let labelAdicional = "";
               
                if (plot.mutationType && mutationConfig[plot.mutationType]) {
                    mult *= mutationConfig[plot.mutationType].mult;
                    labelAdicional = mutationConfig[plot.mutationType].label;
                }

                let calculatedPrice = fruitData[plot.fruit].basePrice * mult * plot.weight;
                let fruitUniqueKey = `${plot.fruit}_fruta`;

                addItemToCollectedHotbar(fruitUniqueKey, 'fruta', 1, {
                    icon: fruitData[plot.fruit].icon,
                    price: calculatedPrice,
                    fullName: `${fruitData[plot.fruit].name} ${labelAdicional}`,
                    mutation: plot.mutationType
                });

                plot.status = 'empty'; plot.fruit = null; plot.mutationType = null;
                plot.element.innerHTML = `<span style="color:#26170c; font-size:9px; position:absolute; bottom:4px; font-weight:bold;">Terra</span>`;
               
                updateUI();
                updateHotbarVisuals();
            }
        }

        function executeAttack() {
            if (isAttacking) return;
            isAttacking = true;
           
            let wep = arsenal[player.weapon];
            let renderWeapon = document.getElementById('weapon-render');
            renderWeapon.style.transform = "rotate(-75deg)";
           
            setTimeout(() => { renderWeapon.style.transform = "rotate(0deg)"; isAttacking = false; }, wep.speed);

            if (!player.inDungeon) return;

            dungeon.entities.forEach((ent) => {
                let dist = Math.sqrt(Math.pow(player.x - ent.x, 2) + Math.pow(player.y - ent.y, 2));
                if (dist <= wep.range) {
                    let finalDmg = wep.damage;
                    if (ent.type === 'minerio' && player.weapon === 'picareta') finalDmg *= 2.5;
                   
                    ent.hp -= finalDmg;
                   
                    let pct = Math.max(0, (ent.hp / ent.maxHp) * 100);
                    let fillBar = ent.el.querySelector('.hp-fill');
                    if (fillBar) fillBar.style.width = `${pct}%`;

                    ent.el.style.transform = "scale(1.2)";
                    setTimeout(() => { if (ent.el) ent.el.style.transform = "scale(1)"; }, 100);

                    if (ent.hp <= 0) handleEntityDeath(ent);
                }
            });

            dungeon.entities = dungeon.entities.filter(e => e.hp > 0);
            checkDungeonFloorClear();
        }

        function handleEntityDeath(ent) {
            if (ent.el) ent.el.remove();
           
            if (ent.type === 'minerio') {
                if (ent.kind === 'ferro') minerios.ferro += Math.floor(Math.random() * 3) + 2;
                if (ent.kind === 'ouro') minerios.ouro += Math.floor(Math.random() * 2) + 1;
            } else {
                let reward = ent.isBoss ? 150 : 12;
                gameState.coins += reward;
               
                if (ent.isBoss) {
                    alert(`🏆 Masmorra Limpa! Você coletou tesouros místicos!`);
                    addItemToCollectedHotbar('divina_semente', 'semente', 3, null);
                }
            }
            updateHotbarVisuals();
            updateUI();
        }

        function enterDungeon() {
            player.inDungeon = true;
            document.getElementById('game-canvas').classList.add('dungeon-mode');
            document.getElementById('farm-zones').style.display = 'none';
            document.getElementById('npc-tiao').style.display = 'none';
            document.getElementById('npc-mago').style.display = 'none';
            document.getElementById('npc-chico').style.display = 'none';
            generateDungeonFloor();
            updateUI();
        }

        function exitDungeon() {
            player.inDungeon = false;
            document.getElementById('game-canvas').classList.remove('dungeon-mode');
            document.getElementById('farm-zones').style.display = 'flex';
            document.getElementById('npc-tiao').style.display = 'flex';
            document.getElementById('npc-mago').style.display = 'flex';
            document.getElementById('npc-chico').style.display = 'flex';
            document.getElementById('dungeon-entities-layer').innerHTML = '';
            document.getElementById('projectiles-layer').innerHTML = '';
            dungeon.entities = [];
            projectiles = [];
            updateUI();
        }

        function generateDungeonFloor() {
            let layer = document.getElementById('dungeon-entities-layer');
            layer.innerHTML = '';
            dungeon.entities = [];

            if (dungeon.floor % 5 === 0) {
                spawnEntity(`Boss Andar ${dungeon.floor} 👑`, 600, 250, 150 + (dungeon.floor * 15), "👑", true, '', 'slime_boss');
            } else {
                let mobCount = 3 + Math.floor(dungeon.floor / 3);
                for (let i = 0; i < Math.min(mobCount, 6); i++) {
                    let rx = Math.random() * 600 + 350;
                    let ry = Math.random() * 300 + 160;
                    let r = Math.random();
                    if(r < 0.4) spawnEntity('Slime Esmeralda', rx, ry, 25 + (dungeon.floor * 4), "🟢", false);
                    else if(r < 0.7) spawnEntity('Esqueleto Arqueiro', rx, ry, 20 + (dungeon.floor * 3), "💀", false, 'arco');
                    else spawnEntity('Guarda Lagarto', rx, ry, 60 + (dungeon.floor * 8), "🦎", false, 'lança');
                }
            }

            for(let i=0; i<3; i++) {
                let rx = Math.random() * 600 + 350; let ry = Math.random() * 300 + 160;
                spawnEntity('Minério', rx, ry, 20, Math.random()>0.5?"🪨":"✨", false, '', '', Math.random()>0.5?'ferro':'ouro');
            }
        }

        function spawnEntity(name, x, y, hp, icon, isBoss = false, weaponType = '', bossType = '', kind = '') {
            let layer = document.getElementById('dungeon-entities-layer');
            let el = document.createElement('div');
            el.className = "dungeon-ent";
            el.style.left = x + 'px'; el.style.top = y + 'px';
            if (isBoss) el.style.fontSize = "40px";

            let wepIcon = weaponType === 'lança' ? '🔱' : (weaponType === 'arco' ? '🏹' : '');
            el.innerHTML = `${icon} ${wepIcon ? `<div class="ent-weapon">${wepIcon}</div>` : ''} <div class="hp-bar"><div class="hp-fill"></div></div>`;
            layer.appendChild(el);

            dungeon.entities.push({ name, x, y, hp, maxHp: hp, el, isBoss, weaponType, bossType, kind, type: kind ? 'minerio' : 'mob', cooldown: 0 });
        }

        function spawnProjectile(x, y, tx, ty, speed, icon) {
            let layer = document.getElementById('projectiles-layer');
            let el = document.createElement('div');
            el.className = "projectile"; el.innerText = icon;
            el.style.left = x + 'px'; el.style.top = y + 'px';
            layer.appendChild(el);

            let dx = tx - x; let dy = ty - y; let dist = Math.sqrt(dx*dx + dy*dy);
            projectiles.push({ el, x, y, vx: (dx / dist) * speed, vy: (dy / dist) * speed, isEnemy: true });
        }

        function dungeonLogicUpdate() {
            dungeon.entities.forEach(ent => {
                if (ent.type === 'minerio') return;

                let dx = player.x - ent.x; let dy = player.y - ent.y;
                let dist = Math.sqrt(dx*dx + dy*dy);
               
                if (dist > 15 && dist < 350) {
                    let sp = ent.isBoss ? 1.5 : 2.0;
                    ent.x += (dx / dist) * sp; ent.y += (dy / dist) * sp;
                    ent.el.style.left = ent.x + 'px'; ent.el.style.top = ent.y + 'px';
                }

                ent.cooldown++;
                if (ent.cooldown >= 30) {
                    ent.cooldown = 0;
                    if (ent.weaponType === 'arco' && dist < 250) spawnProjectile(ent.x, ent.y, player.x, player.y, 5, "🏹");
                }

                if (dist < 30 && !gameState.godMode) {
                    player.hp -= ent.isBoss ? 5 : 2;
                    if (player.hp <= 0) handlePlayerDefeat();
                    updateUI();
                }
            });
        }

        function projectilesUpdate() {
            projectiles.forEach((proj, idx) => {
                proj.x += proj.vx; proj.y += proj.vy;
                proj.el.style.left = proj.x + 'px'; proj.el.style.top = proj.y + 'px';

                let pDist = Math.sqrt(Math.pow(player.x - proj.x, 2) + Math.pow(player.y - proj.y, 2));
                if (pDist < 25) {
                    if (!gameState.godMode) player.hp -= 6;
                    proj.el.remove(); projectiles.splice(idx, 1);
                    if (player.hp <= 0) handlePlayerDefeat();
                    updateUI();
                }
            });
        }

        function handlePlayerDefeat() {
            alert("💔 Você desmaiou! Resgatado de volta à fazenda.");
            exitDungeon();
            player.hp = player.maxHp;
            updateUI();
        }

        function checkDungeonFloorClear() {
            let hostiles = dungeon.entities.filter(e => e.type !== 'minerio').length;
            if (hostiles === 0) {
                dungeon.floor++;
                alert(`⚔️ Subindo para o Andar: ${dungeon.floor}!`);
                generateDungeonFloor();
                updateUI();
            }
        }

        function handleNPCDetection() {
            let tiaoDiv = document.getElementById('npc-tiao');
            let magoDiv = document.getElementById('npc-mago');
            let chicoDiv = document.getElementById('npc-chico');
            let dungDiv = document.getElementById('npc-dungeon');

            if (player.inDungeon) {
                if (Math.sqrt(Math.pow(player.x - dungDiv.offsetLeft, 2) + Math.pow(player.y - dungDiv.offsetTop, 2)) < 70) exitDungeon();
                return;
            }

            if (Math.sqrt(Math.pow(player.x - tiaoDiv.offsetLeft, 2) + Math.pow(player.y - tiaoDiv.offsetTop, 2)) < 65) openNPCModal('tiao');
            else if (Math.sqrt(Math.pow(player.x - magoDiv.offsetLeft, 2) + Math.pow(player.y - magoDiv.offsetTop, 2)) < 65) openNPCModal('mago');
            else if (Math.sqrt(Math.pow(player.x - chicoDiv.offsetLeft, 2) + Math.pow(player.y - chicoDiv.offsetTop, 2)) < 65) openNPCModal('chico');
            else if (Math.sqrt(Math.pow(player.x - dungDiv.offsetLeft, 2) + Math.pow(player.y - dungDiv.offsetTop, 2)) < 65) enterDungeon();
        }

        function openNPCModal(npcType) {
            let modal = document.getElementById('game-modal');
            let title = document.getElementById('modal-title');
            let tabs = document.getElementById('modal-tabs');
            modal.style.display = 'block'; tabs.innerHTML = ''; currentModalNPC = npcType;

            if (npcType === 'tiao') {
                title.innerText = "Mercado do Seu Tião";
                tabs.innerHTML = `
                    <button class="tab-btn active" onclick="renderTiaoShop('seeds')">🌱 Sementes</button>
                    <button class="tab-btn" onclick="renderTiaoShop('fert')">🧪 Fertilizantes</button>
                `;
                renderTiaoShop('seeds');
            } else if (npcType === 'mago') {
                title.innerText = "Altar do Mago"; renderMagoShop();
            } else if (npcType === 'chico') {
                title.innerText = "Seu Chico Comprador"; renderChicoShop();
            }
        }

        function renderTiaoShop(section) {
            let content = document.getElementById('modal-content'); content.innerHTML = '';
            if (section === 'seeds') {
                currentStoreSeeds.forEach(k => {
                    let cost = Math.floor(fruitData[k].basePrice * 0.4);
                    content.innerHTML += `
                        <div class="shop-item">
                            <div class="item-info"><span>${fruitData[k].icon}</span> <b>Semente de ${fruitData[k].name}</b> <p>${cost}🪙</p></div>
                            <button class="buy-btn" onclick="buyItem('seed','${k}_semente',${cost})">Comprar</button>
                        </div>`;
                });
            } else {
                Object.keys(fertData).forEach(key => {
                    let f = fertData[key];
                    content.innerHTML += `
                        <div class="shop-item">
                            <div class="item-info"><span>${f.icon}</span> <b>${f.name}</b> <p>${f.price}🪙</p></div>
                            <button class="buy-btn" onclick="buyItem('fertilizer','${key}',${f.price})">Comprar</button>
                        </div>`;
                });
            }
        }

        function openBackpackModal() {
            let modal = document.getElementById('game-modal'); modal.style.display = 'block'; currentModalNPC = 'mochila';
            document.getElementById('modal-title').innerText = "🎒 Mochila de Carga";
            document.getElementById('modal-tabs').innerHTML = `<button class="tab-btn active" onclick="renderInventoryTab()">🎒 Recursos</button><button class="tab-btn" onclick="renderForgeTab()">🔨 Bigorna</button>`;
            renderInventoryTab();
        }

        function renderInventoryTab() {
            let content = document.getElementById('modal-content');
            content.innerHTML = `<p style='color:#ffd700; margin:0 0 8px 0;'>🪨 Ferro: ${minerios.ferro} | ✨ Ouro: ${minerios.ouro}</p><hr style='border-color:#402b1b;'>`;
            let empty = true;
            Object.keys(backpack).forEach(key => {
                let item = backpack[key];
                if (item.qty > 0) {
                    empty = false;
                    content.innerHTML += `<div class="shop-item"><div class="item-info"><b>${key}</b> (x${item.qty})</div></div>`;
                }
            });
            if(empty) content.innerHTML += "<p style='font-size:11px; color:#aaa;'>Mochila vazia.</p>";
        }

        function renderForgeTab() {
            let content = document.getElementById('modal-content'); content.innerHTML = '';
            Object.keys(arsenal).forEach(key => {
                let wep = arsenal[key];
                content.innerHTML += `
                    <div class="shop-item">
                        <div class="item-info"><b>${wep.icon} ${wep.name}</b> <p>Dano: ${wep.damage}</p></div>
                        <button class="buy-btn" onclick="equipWeapon('${key}')">Equipar</button>
                    </div>`;
            });
        }

        function equipWeapon(key) {
            player.weapon = key;
            document.getElementById('weapon-render').innerText = arsenal[key].icon;
            document.getElementById('weapon-status').innerText = arsenal[key].name;
            closeModal(); updateUI();
        }

        function renderMagoShop() {
            let content = document.getElementById('modal-content');
            let activeItem = hotbar[gameState.activeHotbarSlot];
            if (activeItem && activeItem.type === 'semente') {
                content.innerHTML = `<div class="shop-item"><div class="item-info">🔮 Evoluir Semente para Lvl ${activeItem.level+1}</div><button class="buy-btn" onclick="magicUpgrade(50)">Evoluir (50🪙)</button></div>`;
            } else {
                content.innerHTML = "<p style='color:#ff5555;'>Segure um pacote de sementes na mão primeiro.</p>";
            }
        }

        function magicUpgrade(cost) {
            let activeItem = hotbar[gameState.activeHotbarSlot];
            if (gameState.coins >= cost && activeItem.level < 5) {
                gameState.coins -= cost; activeItem.level++;
                updateUI(); updateHotbarVisuals(); closeModal();
            }
        }

        function renderChicoShop() {
            let content = document.getElementById('modal-content'); let barProfit = 0;
            for (let i = 0; i < 8; i++) { if (hotbar[i] && hotbar[i].type === 'fruta') barProfit += hotbar[i].data.price; }
            content.innerHTML = `<div style="text-align:center;"><p>Valor da carga de frutas na barra: <b>${Math.floor(barProfit)} Moedas</b></p><button class="buy-btn" onclick="chicoVenda(${barProfit})">Vender Tudo</button></div>`;
        }

        function chicoVenda(profit) {
            for (let i = 0; i < 8; i++) { if (hotbar[i] && hotbar[i].type === 'fruta') hotbar[i] = { id: null, qty: 0, type: '', level: 0, data: null }; }
            gameState.coins += profit; updateUI(); updateHotbarVisuals(); closeModal();
        }

        function refreshShopSeeds() {
            currentStoreSeeds = ['maca', 'banana', 'cenoura', 'laranja', 'tomate', 'estrela'];
        }

        function updateUI() {
            document.getElementById('coin-count').innerText = Math.floor(gameState.coins);
            let hpPct = (player.hp / player.maxHp) * 100;
            document.getElementById('hp-bar').style.width = `${hpPct}%`;
            document.getElementById('hp-text').innerText = `HP: ${Math.max(0, Math.floor(player.hp))} / ${player.maxHp}`;

            let item = hotbar[gameState.activeHotbarSlot];
            if(item && item.id) {
                if (item.type === 'semente') document.getElementById('selected-seed-text').innerText = `Semente ${item.id.split('_')[0]} Lv.${item.level} (x${item.qty})`;
                else if (item.type === 'fertilizante') document.getElementById('selected-seed-text').innerText = `${fertData[item.id].name}`;
                else if (item.type === 'fruta') document.getElementById('selected-seed-text').innerText = `${item.data.fullName}`;
                else document.getElementById('selected-seed-text').innerText = `Minério`;
            } else {
                document.getElementById('selected-seed-text').innerText = "Mão Vazia";
            }
        }

        function buyItem(type, target, cost) {
            if (gameState.coins >= cost) {
                addItemToCollectedHotbar(target, type === 'seed' ? 'semente' : 'fertilizante', 1, null);
                gameState.coins -= cost; updateUI(); updateHotbarVisuals();
            }
        }

        function closeModal() { document.getElementById('game-modal').style.display = 'none'; currentModalNPC = null; }

        function gameLoop() {
            let sp = player.speed;
            if(document.activeElement.tagName !== 'INPUT') {
                if (keysPressed['a'] || keysPressed['arrowleft']) player.x -= sp;
                if (keysPressed['d'] || keysPressed['arrowright']) player.x += sp;
                if (keysPressed['w'] || keysPressed['arrowup']) player.y -= sp;
                if (keysPressed['s'] || keysPressed['arrowdown']) player.y += sp;
            }
           
            player.x = Math.max(0, Math.min(window.innerWidth - 45, player.x));
            player.y = Math.max(0, Math.min(window.innerHeight - 135, player.y));

            let pEl = document.getElementById('player');
            if (pEl) { pEl.style.left = player.x + 'px'; pEl.style.top = player.y + 'px'; }

            requestAnimationFrame(gameLoop);
        }

        window.onload = init;
    </script>
</body>
</html>
