<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>JOGO DA MEMÓRIA</title>
  <style>
    * {
      box-sizing: border-box;
      font-family: 'Segoe UI', sans-serif;
    }
    body {
      background-color: #121212;
      color: #ffffff;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 2rem;
    }
    h1 {
      color: #00e5ff;
      margin-bottom: 1rem;
    }
    .game-board {
      display: grid;
      grid-template-columns: repeat(4, 100px);
      gap: 12px;
      margin-bottom: 1.5rem;
    }
    .card {
      width: 100px;
      height: 100px;
      background-color: #1e1e1e;
      border-radius: 10px;
      cursor: pointer;
      perspective: 1000px;
      position: relative;
      transform-style: preserve-3d;
      transition: transform 0.5s;
    }
    .card.flip .card-inner {
      transform: rotateY(180deg);
    }
    .card-inner {
      position: relative;
      width: 100%;
      height: 100%;
      transition: transform 0.5s;
      transform-style: preserve-3d;
    }
    .card-front, .card-back {
      position: absolute;
      width: 100%;
      height: 100%;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 2rem;
      backface-visibility: hidden;
    }
    .card-front {
      background-color: #2c2c2c;
    }
    .card-back {
      background-color: #00e5ff;
      color: #121212;
      transform: rotateY(180deg);
    }
    .status {
      font-size: 1.2rem;
      margin-bottom: 1rem;
    }
    button {
      background-color: #00e5ff;
      color: #121212;
      padding: 0.5rem 1rem;
      border: none;
      border-radius: 8px;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover {
      background-color: #00bcd4;
    }
  </style>
</head>
<body>
  <h1>JOGO DA MEMÓRIA</h1>
  <div id="game-board" class="game-board"></div>
  <div id="status" class="status"></div>
  <button onclick="reiniciarJogo()">Reiniciar</button>
  <audio id="flipSound" src="https://cdn.pixabay.com/audio/2022/03/01/audio_d2b96f3b84.mp3"></audio>
  <audio id="matchSound" src="https://cdn.pixabay.com/audio/2022/03/15/audio_c0b7f8aa5b.mp3"></audio>
  <script>
    const emojis = ['🍕', '🎮', '🐱', '🎧', '🚀', '🌈', '📚', '🧠'];
    let board, status, flipSound, matchSound;
    let firstCard = null;
    let secondCard = null;
    let lockBoard = false;
    let matchedPairs = 0;
    document.addEventListener('DOMContentLoaded', () => {
      board = document.getElementById('game-board');
      status = document.getElementById('status');
      flipSound = document.getElementById('flipSound');
      matchSound = document.getElementById('matchSound');
      iniciarJogo();
    });
    function iniciarJogo() {
      const cards = embaralhar([...emojis, ...emojis]);
      board.innerHTML = '';
      matchedPairs = 0;
      firstCard = null;
      secondCard = null;
      lockBoard = false;
      status.textContent = '';
      cards.forEach(emoji => {
        const card = criarCarta(emoji);
        board.appendChild(card);
      });
    }
    function criarCarta(emoji) {
      const card = document.createElement('div');
      card.className = 'card';
      const inner = document.createElement('div');
      inner.className = 'card-inner';
      const front = document.createElement('div');
      front.className = 'card-front';
      front.textContent = '?';
      const back = document.createElement('div');
      back.className = 'card-back';
      back.textContent = emoji;
      inner.appendChild(front);
      inner.appendChild(back);
      card.appendChild(inner);
      card.dataset.emoji = emoji;
      card.addEventListener('click', () => virarCarta(card));
      return card;
    }
    function virarCarta(card) {
      if (lockBoard || card === firstCard || card.classList.contains('flip')) return;
      flipSound.play();
      card.classList.add('flip');
      if (!firstCard) {
        firstCard = card;
        return;
      }
      secondCard = card;
      lockBoard = true;
      if (firstCard.dataset.emoji === secondCard.dataset.emoji) {
        matchSound.play();
        matchedPairs++;
        resetarVirada();
        if (matchedPairs === emojis.length) {
          status.textContent = 'Parabéns! Você venceu! 🏆';
        }
      } else {
        setTimeout(() => {
          firstCard.classList.remove('flip');
          secondCard.classList.remove('flip');
          resetarVirada();
        }, 1000);
      }
    }
    function resetarVirada() {
      [firstCard, secondCard] = [null, null];
      lockBoard = false;
    }
    function embaralhar(array) {
      return array.sort(() => Math.random() - 0.5);
    }
    function reiniciarJogo() {
      iniciarJogo();
    }
  </script>
</body>
</html>
