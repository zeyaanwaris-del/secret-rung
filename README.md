# secret-rung<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Secret Rung - Complete Game</title>
    <style>
        :root {
            --bg-color: #0f172a;
            --panel-bg: #1e293b;
            --panel-border: #334155;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
            --accent: #3b82f6;
            --accent-hover: #2563eb;
            --danger: #ef4444;
            --success: #22c55e;
            --warning: #f59e0b;
            font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 15px;
        }

        header {
            text-align: center;
            margin-bottom: 8px;
            width: 100%;
            max-width: 650px;
        }

        header h1 { font-size: 1.4rem; color: var(--accent); margin-bottom: 2px; }
        header p { font-size: 0.75rem; color: var(--text-muted); }

        .game-container {
            width: 100%;
            max-width: 650px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            padding-bottom: 30px;
        }

        .card {
            background: white;
            color: #1e293b;
            border-radius: 6px;
            width: 46px;
            height: 68px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 4px;
            font-weight: bold;
            font-size: 0.75rem;
            box-shadow: 0 3px 5px rgba(0,0,0,0.3);
            cursor: pointer;
            transition: transform 0.2s;
            user-select: none;
            border: 1px solid #cbd5e1;
            position: relative;
        }

        .card.red { color: #dc2626; }
        .card.black { color: #0f172a; }
        .card:hover { transform: translateY(-3px); }
        .card.selected { transform: translateY(-6px); border: 2px solid var(--accent); box-shadow: 0 0 8px var(--accent); }

        .panel {
            background-color: var(--panel-bg);
            border: 1px solid var(--panel-border);
            border-radius: 10px;
            padding: 10px;
        }

        .score-board {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 0.85rem;
        }

        .score-val { font-size: 1.1rem; font-weight: bold; color: var(--warning); }

        .table-area {
            background: radial-gradient(circle, #065f46 0%, #047857 100%);
            border-radius: 20px;
            height: 280px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 12px;
            position: relative;
            border: 2px solid #059669;
        }

        .table-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 5px;
        }

        .seat-box {
            background: rgba(0, 0, 0, 0.45);
            padding: 6px 10px;
            border-radius: 6px;
            text-align: center;
            border: 1px solid rgba(255,255,255,0.15);
            font-size: 0.75rem;
            min-width: 65px;
            z-index: 2;
        }

        .seat-box.active { border-color: var(--warning); background: rgba(245, 158, 11, 0.35); color: #fde68a; font-weight: bold; }

        .trick-center {
            display: flex;
            gap: 6px;
            justify-content: center;
            align-items: center;
            flex-wrap: wrap;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 70%;
        }

        .played-card-slot {
            text-align: center;
            font-size: 0.6rem;
            color: #e2e8f0;
        }

        .status-text {
            text-align: center;
            font-size: 0.8rem;
            font-weight: 500;
            color: #a7f3d0;
            min-height: 1.2rem;
        }

        .hand-container {
            display: flex;
            gap: 6px;
            overflow-x: auto;
            padding: 6px 2px;
            min-height: 84px;
            align-items: center;
        }

        .controls-area {
            display: flex;
            gap: 6px;
            justify-content: center;
            flex-wrap: wrap;
            align-items: center;
        }

        button {
            background-color: var(--accent);
            color: white;
            border: none;
            padding: 7px 14px;
            border-radius: 6px;
            font-size: 0.8rem;
            font-weight: 600;
            cursor: pointer;
        }

        button:hover { background-color: var(--accent-hover); }
        button:disabled { background-color: var(--panel-border); color: var(--text-muted); cursor: not-allowed; }
        button.secondary { background-color: #475569; }
        button.danger { background-color: var(--danger); }

        select {
            background: var(--panel-bg);
            color: var(--text-main);
            border: 1px solid var(--panel-border);
            padding: 6px 10px;
            border-radius: 6px;
            font-size: 0.8rem;
            cursor: pointer;
        }

        .modal-overlay {
            position: fixed;
            top: 0; left: 0; width: 100vw; height: 100vh;
            background: rgba(0,0,0,0.8);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 100;
            padding: 16px;
        }

        .modal-content {
            background: var(--panel-bg);
            border: 1px solid var(--panel-border);
            border-radius: 12px;
            padding: 16px;
            width: 100%;
            max-width: 360px;
            text-align: center;
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .modal-content h2 { color: var(--accent); font-size: 1.1rem; }
        .modal-content p { font-size: 0.8rem; color: var(--text-muted); }

        .grid-options {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 6px;
        }
    </style>
</head>
<body>

    <header>
        <h1>Secret Rung Companion</h1>
        <p>Seating Order: A1 → B1 → A2 → B2 → A3 → B3</p>
    </header>

    <div class="game-container">
        <!-- Scoreboard & Settings Panel -->
        <div class="panel score-board">
            <div>
                <div style="font-size:0.7rem; color:var(--text-muted);">Team Debt</div>
                <div class="score-val"><span id="scoreVal">0</span> / 100</div>
            </div>
            <div>
                <div style="font-size:0.7rem; color:var(--text-muted);">Mode</div>
                <select id="gameModeSelect" onchange="changeGameMode(this.value)">
                    <option value="SINGLE">Single Sir</option>
                    <option value="DOUBLE">Double Sir</option>
                </select>
            </div>
            <div style="text-align: right;">
                <div style="font-size:0.7rem; color:var(--text-muted);">Dealer</div>
                <div id="dealerDisplay" style="font-weight: bold; color:var(--text-main);">A1</div>
            </div>
        </div>

        <!-- Hexagonal Table Layout matching A1 -> B1 -> A2 -> B2 -> A3 -> B3 -->
        <div class="table-area">
            <div class="table-row">
                <div id="seat-A3" class="seat-box">A3</div>
                <div id="seat-B2" class="seat-box">B2</div>
            </div>

            <div class="trick-center" id="trickCenter">
                <div style="color: #d1fae5; font-size: 0.75rem;">Table Ready</div>
            </div>

            <div class="table-row">
                <div id="seat-B3" class="seat-box">B3</div>
                <div id="seat-A2" class="seat-box">A2</div>
            </div>

            <div class="table-row">
                <div id="seat-A1" class="seat-box active">You (A1)</div>
                <div id="seat-B1" class="seat-box">B1</div>
            </div>
        </div>

        <div class="status-text" id="statusText">Select Mode and press Start New Deal.</div>

        <!-- Player Hand Panel -->
        <div class="panel">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 6px;">
                <span style="font-size: 0.8rem; font-weight: 600;">Your Hand (A1)</span>
                <span id="rungStatusIndicator" style="font-size: 0.75rem; color: var(--warning);">Rung: Hidden</span>
            </div>
            <div class="hand-container" id="playerHand"></div>
        </div>

        <!-- Action Controls -->
        <div class="controls-area" id="controlsArea">
            <button onclick="startBiddingPhase()">Start New Deal</button>
        </div>
    </div>

    <!-- Modals -->
    <div id="biddingModal" class="modal-overlay" style="display: none;">
        <div class="modal-content">
            <h2>Place Bid</h2>
            <p id="biddingPromptText">Select your bid or Pass.</p>
            <div class="grid-options" id="bidOptionsGrid"></div>
            <button class="secondary" onclick="playerPassBid()">Pass</button>
        </div>
    </div>

    <div id="rungModal" class="modal-overlay" style="display: none;">
        <div class="modal-content">
            <h2>Select Secret Rung</h2>
            <p>Choose your hidden trump suit for this round.</p>
            <div class="grid-options">
                <button onclick="selectRung('♠')">♠ Spades</button>
                <button onclick="selectRung('♥')" style="color:#ef4444;">♥ Hearts</button>
                <button onclick="selectRung('♦')" style="color:#ef4444;">♦ Diamonds</button>
                <button onclick="selectRung('♣')">♣ Clubs</button>
            </div>
        </div>
    </div>

    <script>
        const PLAYERS = [
            { id: 0, name: 'A1', team: 'A', isAI: false },
            { id: 1, name: 'B1', team: 'B', isAI: true },
            { id: 2, name: 'A2', team: 'A', isAI: true },
            { id: 3, name: 'B2', team: 'B', isAI: true },
            { id: 4, name: 'A3', team: 'A', isAI: true },
            { id: 5, name: 'B3', team: 'B', isAI: true }
        ];

        const SUITS = ['♠', '♥', '♦', '♣'];
        const VALUES = ['3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A'];
        const RANK_MAP = { '3':3, '4':4, '5':5, '6':6, '7':7, '8':8, '9':9, '10':10, 'J':11, 'Q':12, 'K':13, 'A':14 };

        let gameState = {
            score: 0,
            dealerSeqIdx: 0,
            gameMode: 'SINGLE',
            phase: 'IDLE',
            hands: [[],[],[],[],[],[]],
            currentBid: 5,
            highBidderSeqIdx: null,
            passedSequence: [],
            biddingSeqIdx: 0,
            secretRung: null,
            rungAsked: false,
            turnSeqIdx: 0,
            currentTrick: [],
            leadSuit: null,
            tricksWon: { A: 0, B: 0 },
            selectedCardIdx: null,
            doubleSirPile: [],
            lastTrickWinnerTeam: null
        };

        function changeGameMode(mode) {
            if (gameState.phase !== 'IDLE' && gameState.phase !== 'ROUND_OVER') {
                alert("Cannot change mode in the middle of a round!");
                document.getElementById('gameModeSelect').value = gameState.gameMode;
                return;
            }
            gameState.gameMode = mode;
            setStatus(`Mode changed to ${mode === 'SINGLE' ? 'Single Sir' : 'Double Sir'}.`);
        }

        function createDeck() {
            let deck = [];
            for (let s of SUITS) {
                for (let v of VALUES) {
                    deck.push({ suit: s, val: v, rank: RANK_MAP[v] });
                }
            }
            for (let i = deck.length - 1; i > 0; i--) {
                let j = Math.floor(Math.random() * (i + 1));
                [deck[i], deck[j]] = [deck[j], deck[i]];
            }
            return deck;
        }

        function startBiddingPhase() {
            let deck = createDeck();
            gameState.hands = [[],[],[],[],[],[]];
            for (let i = 0; i < 8; i++) {
                for (let p = 0; p < 6; p++) {
                    gameState.hands[p].push(deck.pop());
                }
            }
            gameState.hands[0].sort((a,b) => SUITS.indexOf(a.suit) - SUITS.indexOf(b.suit) || a.rank - b.rank);

            gameState.phase = 'BIDDING';
            gameState.currentBid = 5;
            gameState.highBidderSeqIdx = null;
            gameState.passedSequence = [];
            gameState.biddingSeqIdx = (gameState.dealerSeqIdx + 1) % 6;
            gameState.doubleSirPile = [];
            gameState.lastTrickWinnerTeam = null;
            
            updateUI();
            setStatus(`Bidding started (${gameState.gameMode === 'SINGLE' ? 'Single Sir' : 'Double Sir'}). Dealer is ${PLAYERS[gameState.dealerSeqIdx].name}.`);
            processBiddingTurn();
        }

        function processBiddingTurn() {
            if (gameState.passedSequence.length >= 5 && gameState.highBidderSeqIdx !== null) {
                let winner = PLAYERS[gameState.highBidderSeqIdx];
                if (winner.id === 0) {
                    document.getElementById('rungModal').style.display = 'flex';
                } else {
                    gameState.secretRung = SUITS[Math.floor(Math.random() * SUITS.length)];
                    startPlayingPhase();
                }
                return;
            }

            if (gameState.passedSequence.includes(gameState.biddingSeqIdx)) {
                gameState.biddingSeqIdx = (gameState.biddingSeqIdx + 1) % 6;
                processBiddingTurn();
                return;
            }

            let player = PLAYERS[gameState.biddingSeqIdx];
            if (!player.isAI) {
                openBiddingModal();
            } else {
                setTimeout(() => {
                    let minAllowed = (player.id === PLAYERS[gameState.dealerSeqIdx].id) ? 5 : 6;
                    let targetBid = Math.max(gameState.currentBid + (Math.random() > 0.3 ? 1 : 0), minAllowed);
                    
                    if (targetBid <= 8 && Math.random() > 0.2 && targetBid > gameState.currentBid) {
                        gameState.currentBid = targetBid;
                        gameState.highBidderSeqIdx = gameState.biddingSeqIdx;
                        setStatus(`${player.name} bids ${targetBid}.`);
                    } else {
                        gameState.passedSequence.push(gameState.biddingSeqIdx);
                        setStatus(`${player.name} passes.`);
                    }
                    
                    gameState.biddingSeqIdx = (gameState.biddingSeqIdx + 1) % 6;
                    processBiddingTurn();
                }, 400);
            }
        }

        function openBiddingModal() {
            let grid = document.getElementById('bidOptionsGrid');
            grid.innerHTML = '';
            let player = PLAYERS[gameState.biddingSeqIdx];
            let minBid = (player.id === PLAYERS[gameState.dealerSeqIdx].id) ? 5 : 6;
            let start = Math.max(gameState.currentBid + 1, minBid);

            for (let b = start; b <= 8; b++) {
                let btn = document.createElement('button');
                btn.innerText = `Bid ${b}`;
                btn.onclick = () => {
                    gameState.currentBid = b;
                    gameState.highBidderSeqIdx = gameState.biddingSeqIdx;
                    document.getElementById('biddingModal').style.display = 'none';
                    setStatus(`You bid ${b}.`);
                    gameState.biddingSeqIdx = (gameState.biddingSeqIdx + 1) % 6;
                    processBiddingTurn();
                };
                grid.appendChild(btn);
            }
            document.getElementById('biddingModal').style.display = 'flex';
        }

        function playerPassBid() {
            gameState.passedSequence.push(gameState.biddingSeqIdx);
            document.getElementById('biddingModal').style.display = 'none';
            setStatus(`You passed the bid.`);
            gameState.biddingSeqIdx = (gameState.biddingSeqIdx + 1) % 6;
            processBiddingTurn();
        }

        function selectRung(suit) {
            gameState.secretRung = suit;
            document.getElementById('rungModal').style.display = 'none';
            startPlayingPhase();
        }

        function startPlayingPhase() {
            gameState.phase = 'PLAYING';
            gameState.rungAsked = false;
            gameState.tricksWon = { A: 0, B: 0 };
            gameState.turnSeqIdx = (gameState.highBidderSeqIdx + 1) % 6;
            gameState.currentTrick = [];
            gameState.leadSuit = null;

            updateUI();
            let winnerName = PLAYERS[gameState.highBidderSeqIdx].name;
            setStatus(`Bidding won by ${winnerName} at ${gameState.currentBid}. Play started!`);
            processPlayTurn();
        }

        function processPlayTurn() {
            if (gameState.currentTrick.length === 6) {
                resolveTrick();
                return;
            }

            let player = PLAYERS[gameState.turnSeqIdx];
            updateUI();

            if (player.isAI) {
                setTimeout(() => {
                    let hand = gameState.hands[player.id];
                    let playable = hand;
                    if (gameState.leadSuit) {
                        let matching = hand.filter(c => c.suit === gameState.leadSuit);
                        if (matching.length > 0) playable = matching;
                    }
                    let chosenCard = playable[Math.floor(Math.random() * playable.length)];
                    playCardAction(player.id, chosenCard);
                }, 500);
            } else {
                setStatus(`Your turn to play. Select a card.`);
            }
        }

        function selectCard(idx) {
            if (gameState.phase !== 'PLAYING' || PLAYERS[gameState.turnSeqIdx].id !== 0) return;
            gameState.selectedCardIdx = idx;
            updateUI();
        }

        function confirmPlayCard() {
            if (gameState.selectedCardIdx === null) return;
            let card = gameState.hands[0][gameState.selectedCardIdx];

            if (gameState.leadSuit) {
                let hasLead = gameState.hands[0].some(c => c.suit === gameState.leadSuit);
                if (hasLead && card.suit !== gameState.leadSuit && !gameState.rungAsked) {
                    alert(`Must follow suit (${gameState.leadSuit}) or ask for the Rung!`);
                    return;
                }
            }

            gameState.hands[0].splice(gameState.selectedCardIdx, 1);
            gameState.selectedCardIdx = null;
            playCardAction(0, card);
        }

        function askRungAction() {
            if (gameState.rungAsked) {
                alert("Secret Rung already revealed this round!");
                return;
            }
            gameState.rungAsked = true;
            document.getElementById('rungStatusIndicator').innerText = `Rung: ${gameState.secretRung} (Revealed)`;
            setStatus(`Secret Rung revealed as ${gameState.secretRung}!`);
            updateUI();
        }

        function playCardAction(playerId, card) {
            if (gameState.currentTrick.length === 0) {
                gameState.leadSuit = card.suit;
            }

            gameState.currentTrick.push({ playerId, card });
            gameState.turnSeqIdx = (gameState.turnSeqIdx + 1) % 6;

            let biddingTeam = PLAYERS[gameState.highBidderSeqIdx].team;
            let tricksPlayedTotal = (8 - gameState.hands[0].length);
            let maxPossibleBiddingTeamTricks = gameState.tricksWon[biddingTeam] + (8 - tricksPlayedTotal);

            if (maxPossibleBiddingTeamTricks < gameState.currentBid && gameState.currentTrick.length === 0) {
                endRoundScoring(false, true);
                return;
            }

            updateUI();
            processPlayTurn();
        }

        function resolveTrick() {
            let winningPlay = gameState.currentTrick[0];
            let leadSuit = gameState.leadSuit;

            for (let play of gameState.currentTrick) {
                let isRung = (play.card.suit === gameState.secretRung && gameState.rungAsked);
                let currentWinningIsRung = (winningPlay.card.suit === gameState.secretRung && gameState.rungAsked);

                if (isRung && !currentWinningIsRung) {
                    winningPlay = play;
                } else if (isRung && currentWinningIsRung) {
                    if (play.card.rank > winningPlay.card.rank) winningPlay = play;
                } else if (!isRung && !currentWinningIsRung) {
                    if (play.card.suit === leadSuit && winningPlay.card.suit === leadSuit) {
                        if (play.card.rank > winningPlay.card.rank) winningPlay = play;
                    }
                }
            }

            let winnerPlayer = PLAYERS.find(p => p.id === winningPlay.playerId);
            let winningTeam = winnerPlayer.team;

            if (gameState.gameMode === 'SINGLE') {
                gameState.tricksWon[winningTeam]++;
                setStatus(`Trick won by ${winnerPlayer.name} (${winningTeam})!`);
            } else {
                gameState.doubleSirPile.push(...gameState.currentTrick.map(p => p.card));
                
                if (gameState.lastTrickWinnerTeam === winningTeam) {
                    let pileSizeCount = gameState.doubleSirPile.length / 6;
                    gameState.tricksWon[winningTeam] += pileSizeCount;
                    gameState.doubleSirPile = [];
                    gameState.lastTrickWinnerTeam = null;
                    setStatus(`${winnerPlayer.name} won 2 in a row! Swept ${pileSizeCount} tricks for Team ${winningTeam}!`);
                } else {
                    gameState.lastTrickWinnerTeam = winningTeam;
                    setStatus(`Trick won by ${winnerPlayer.name} (${winningTeam}). Pile stacks in center.`);
                }
            }

            setTimeout(() => {
                gameState.currentTrick = [];
                gameState.leadSuit = null;
                gameState.turnSeqIdx = PLAYERS.findIndex(p => p.id === winnerPlayer.id);

                let biddingTeam = PLAYERS[gameState.highBidderSeqIdx].team;
                let totalTricksSoFar = gameState.tricksWon.A + gameState.tricksWon.B;
                let maxPossibleBidding = gameState.tricksWon[biddingTeam] + (8 - totalTricksSoFar);

                if (maxPossibleBidding < gameState.currentBid) {
                    endRoundScoring(false, true);
                } else if (gameState.hands[0].length === 0) {
                    if (gameState.gameMode === 'DOUBLE' && gameState.doubleSirPile.length > 0) {
                        let remainingTricks = gameState.doubleSirPile.length / 6;
                        gameState.tricksWon[winningTeam] += remainingTricks;
                        gameState.doubleSirPile = [];
                    }
                    let won = gameState.tricksWon[biddingTeam] >= gameState.currentBid;
                    endRoundScoring(won, false);
                } else {
                    processPlayTurn();
                }
            }, 1000);
        }

        function endRoundScoring(won, earlyCutoff) {
            gameState.phase = 'ROUND_OVER';
            let biddingTeam = PLAYERS[gameState.highBidderSeqIdx].team;
            let targetBid = gameState.currentBid;

            if (won) {
                gameState.score = Math.max(0, gameState.score - targetBid);
                setStatus(`Team ${biddingTeam} made bid ${targetBid}! Score reduced.`);
            } else {
                gameState.score += targetBid * 2;
                let reason = earlyCutoff ? " (Early Cutoff: Defending team locked out contract!)" : "";
                setStatus(`Team ${biddingTeam} failed bid! Penalty added.${reason}`);
            }

            gameState.dealerSeqIdx = (gameState.dealerSeqIdx + 1) % 6;
            updateUI();
        }

        function updateUI() {
            document.getElementById('scoreVal').innerText = gameState.score;
            document.getElementById('dealerDisplay').innerText = PLAYERS[gameState.dealerSeqIdx].name;
            document.getElementById('gameModeSelect').value = gameState.gameMode;

            PLAYERS.forEach((p, idx) => {
                let seat = document.getElementById(`seat-${p.name}`);
                if (seat) {
                    seat.className = 'seat-box' + (gameState.turnSeqIdx === idx && gameState.phase === 'PLAYING' ? ' active' : '');
                }
            });

            let trickCenter = document.getElementById('trickCenter');
            if (gameState.currentTrick.length === 0) {
                let pileInfo = (gameState.gameMode === 'DOUBLE' && gameState.doubleSirPile.length > 0) ? ` | Center Pile: ${gameState.doubleSirPile.length / 6} tricks` : '';
                trickCenter.innerHTML = `<div style="color: #d1fae5; font-size: 0.75rem;">Tricks - A: ${gameState.tricksWon.A} | B: ${gameState.tricksWon.B}${pileInfo}</div>`;
            } else {
                trickCenter.innerHTML = gameState.currentTrick.map(p => {
                    let playerName = PLAYERS.find(pl => pl.id === p.playerId).name;
                    return `
                        <div class="played-card-slot">
                            <div class="card ${['♥','♦'].includes(p.card.suit)?'red':'black'}" style="width:34px; height:48px; font-size:0.6rem;">
                                <span>${p.card.val}${p.card.suit}</span>
                            </div>
                            <span>${playerName}</span>
                        </div>
                    `;
                }).join('');
            }

            let handContainer = document.getElementById('playerHand');
            handContainer.innerHTML = gameState.hands[0].map((c, idx) => `
                <div class="card ${['♥','♦'].includes(c.suit)?'red':'black'} ${gameState.selectedCardIdx === idx ? 'selected' : ''}" onclick="selectCard(${idx})">
                    <span>${c.val}${c.suit}</span>
                    <span style="align-self: center; font-size: 1rem;">${c.suit}</span>
                    <span style="align-self: flex-end;">${c.val}${c.suit}</span>
                </div>
            `).join('');

            let controls = document.getElementById('controlsArea');
            if (gameState.phase === 'IDLE' || gameState.phase === 'ROUND_OVER') {
                controls.innerHTML = `<button onclick="startBiddingPhase()">Start New Deal</button>`;
            } else if (gameState.phase === 'PLAYING' && PLAYERS[gameState.turnSeqIdx].id === 0) {
                controls.innerHTML = `
                    <button onclick="confirmPlayCard()">Play Card</button>
                    <button class="secondary" onclick="askRungAction()" ${gameState.rungAsked?'disabled':''}>Ask Rung</button>
                `;
            } else {
                controls.innerHTML = `<span style="font-size:0.75rem; color:var(--text-muted);">Waiting for opponent...</span>`;
            }
        }

        function setStatus(msg) {
            document.getElementById('statusText').innerText = msg;
        }

        updateUI();
    </script>
</body>
</html>
