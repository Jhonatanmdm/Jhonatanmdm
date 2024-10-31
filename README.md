<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jogos de Apostas</title>
    <style>
        body { font-family: Arial, sans-serif; }
        .game { margin: 20px; padding: 10px; border: 1px solid #ccc; }
    </style>
</head>
<body>

    <h1>Jogos de Apostas</h1>

    <div id="minesGame" class="game">
        <h2>Mines</h2>
        <input type="number" id="minesBet" placeholder="Aposta" min="1" />
        <button onclick="playMines()">Jogar</button>
        <p id="minesResult"></p>
        <button onclick="cashOutMines()">Sacar</button>
        <p>Saldo: <span id="minesBalance">0</span></p>
    </div>

    <div id="aviatorGame" class="game">
        <h2>Aviator</h2>
        <input type="number" id="aviatorBet" placeholder="Aposta" min="1" />
        <button onclick="startAviator()">Decolar</button>
        <p id="aviatorResult"></p>
        <button onclick="cashOutAviator()">Sacar</button>
        <p>Saldo: <span id="aviatorBalance">0</span></p>
    </div>

    <div id="animalSlotGame" class="game">
        <h2>Animal Slot</h2>
        <input type="number" id="animalBet" placeholder="Aposta" min="1" />
        <button onclick="playAnimalSlot()">Jogar</button>
        <p id="animalResult"></p>
        <button onclick="cashOutAnimalSlot()">Sacar</button>
        <p>Saldo: <span id="animalBalance">0</span></p>
    </div>

    <div id="imbaxadinhaGame" class="game">
        <h2>Imbaxadinha</h2>
        <input type="number" id="imbaxadinhaBet" placeholder="Aposta" min="1" />
        <button onclick="startImbaxadinha()">Jogar</button>
        <p id="imbaxadinhaResult"></p>
        <button onclick="cashOutImbaxadinha()">Sacar</button>
        <p>Saldo: <span id="imbaxadinhaBalance">0</span></p>
    </div>

    <script>
        // Mines Game
        let minesGame = {
            totalMines: 40,
            playerMoney: 0,
            minefield: [],

            init: function() {
                this.minefield = Array(this.totalMines).fill(false);
                for (let i = 0; i < 20; i++) {
                    let index = Math.floor(Math.random() * this.totalMines);
                    this.minefield[index] = true; // true means bomb
                }
            },

            play: function(bet) {
                const hitBomb = Math.random() < 0.5; // 50% chance to hit mine
                if (hitBomb) {
                    this.playerMoney -= bet; // lose the bet
                    return "Você atingiu uma bomba! Você perdeu sua aposta.";
                } else {
                    this.playerMoney += bet; // win the bet
                    return "Você encontrou uma mina! Você ganhou sua aposta.";
                }
            },

            cashOut: function() {
                let amount = this.playerMoney;
                this.playerMoney = 0;
                return amount;
            }
        };

        // Aviator Game
        let aviatorGame = {
            playerMoney: 0,
            multiplier: 1,
            isFlying: false,
            interval: null,

            startFlight: function(bet) {
                this.playerMoney = bet;
                this.isFlying = true;
                this.multiplier = 1;

                this.interval = setInterval(() => {
                    this.multiplier += 0.1; // Increase multiplier
                    if (Math.random() < 0.05) { // 5% chance of crashing
                        this.isFlying = false;
                        clearInterval(this.interval);
                        this.playerMoney = 0; // Lost all money
                        document.getElementById('aviatorResult').innerText = "O avião caiu! Você perdeu sua aposta.";
                    }
                }, 1000);
            },

            cashOut: function() {
                if (this.isFlying) {
                    this.isFlying = false;
                    clearInterval(this.interval);
                    const cashOutAmount = this.playerMoney * this.multiplier; // Cash out amount
                    this.playerMoney = 0; // Reset money after cashing out
                    return cashOutAmount;
                } else {
                    return "O avião já caiu.";
                }
            }
        };

        // Animal Slot Game
        let animalSlotGame = {
            playerMoney: 0,

            play: function(bet) {
                const animals = ['leão', 'tigre', 'urso', 'elefante'];
                const results = [];
                for (let i = 0; i < 3; i++) {
                    results.push(animals[Math.floor(Math.random() * animals.length)]);
                }

                const isWin = results[0] === results[1] && results[1] === results[2];

                if (isWin) {
                    this.playerMoney += bet * 2; // Win double the bet
                    return `Você ganhou! Animais: ${results.join(', ')}`;
                } else {
                    this.playerMoney -= bet; // Lose the bet
                    return `Você perdeu! Animais: ${results.join(', ')}`;
                }
            },

            cashOut: function() {
                let amount = this.playerMoney;
                this.playerMoney = 0; // reset money after cashing out
                return amount;
            }
        };

        // Imbaxadinha Game
        let imbaxadinhaGame = {
            playerMoney: 0,

            startGame: function(bet) {
                this.playerMoney = bet;
                const result = Math.random() < 0.9; // 90% chance to keep the ball

                if (result) {
                    this.playerMoney += bet; // Win
                    return "Você manteve a bola! Você ganhou sua aposta.";
                } else {
                    this.playerMoney = 0; // Lose all money
                    return "Você deixou a bola cair! Você perdeu sua aposta.";
                }
            },

            cashOut: function() {
                let amount = this.playerMoney;
                this.playerMoney = 0; // reset money after cashing out
                return amount;
            }
        };

        // Integrar as interações com a interface
        function playMines() {
            const bet = parseFloat(document.getElementById('minesBet').value);
            if (!minesGame.playerMoney) {
                minesGame.init();
            }
            const result = minesGame.play(bet);
            document.getElementById('minesResult').innerText = result;
            document.getElementById('minesBalance').innerText = minesGame.playerMoney;
        }

        function cashOutMines() {
            const amount = minesGame.cashOut();
            document.getElementById('minesResult').innerText = `Você sacou: R$ ${amount}`;
            document.getElementById('minesBalance').innerText = minesGame.playerMoney;
        }

        function startAviator() {
            const bet = parseFloat(document.getElementById('aviatorBet').value);
            aviatorGame.startFlight(bet);
            document.getElementById('aviatorResult').innerText = "O avião está voando!";
        }

        function cashOutAviator() {
            const amount = aviatorGame.cashOut();
            document.getElementById('aviatorResult').innerText = amount !== 0 ? `Você sacou: R$ ${amount}` : "O avião já caiu.";
        }

        function playAnimalSlot() {
            const bet = parseFloat(document.getElementById('animalBet').value);
            const result = animalSlotGame.play(bet);
            document.getElementById('animalResult').innerText = result;
            document.getElementById('animalBalance').innerText = animalSlotGame.playerMoney;
        }

        function cashOutAnimalSlot() {
            const amount = animalSlotGame.cashOut();
            document.getElementById('animalResult').innerText = `Você sacou: R$ ${amount}`;
            document.getElementById('animalBalance').innerText = animalSlotGame.playerMoney;
        }

        function startImbaxadinha() {
            const bet = parseFloat(document.getElementById('imbaxadinhaBet').value);
            const result = imbaxadinhaGame.startGame(bet);
            document.getElementById('imbaxadinhaResult').innerText = result;
            document.getElementById('imbaxadinhaBalance').innerText = imbaxadinhaGame.playerMoney;
        }

        function cashOutImbaxadinha() {
            const amount = imbaxadinhaGame.cash
