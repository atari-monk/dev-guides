[Back](index.md)

# Memory

## MVP

### index.html

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Memory Match Game</title>
        <link rel="stylesheet" href="styles.css" />
    </head>
    <body>
        <div>
            <h1>Memory Match Game</h1>
            <div class="game-container">
                <div class="card hidden" data-card="1"></div>
                <div class="card hidden" data-card="2"></div>
                <div class="card hidden" data-card="1"></div>
                <div class="card hidden" data-card="2"></div>
                <div class="card hidden" data-card="3"></div>
                <div class="card hidden" data-card="4"></div>
                <div class="card hidden" data-card="3"></div>
                <div class="card hidden" data-card="4"></div>
            </div>
        </div>

        <script src="script.js"></script>
    </body>
</html>
```

### styles.css

```css
body {
    font-family: Arial, sans-serif;
    background-color: #f7f7f7;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    color: #333;
}

.game-container {
    display: grid;
    grid-template-columns: repeat(4, 100px);
    grid-gap: 10px;
    justify-content: center;
}

.card {
    width: 100px;
    height: 100px;
    background-color: #f5a623;
    text-align: center;
    line-height: 100px;
    font-size: 24px;
    color: white;
    border-radius: 10px;
    cursor: pointer;
    user-select: none;
}

.card.flipped {
    background-color: #4caf50;
}

.hidden {
    background-color: #ccc;
}

h1 {
    text-align: center;
    font-size: 28px;
    margin-bottom: 20px;
}
```

### script.js

```javascript
let flippedCards = [];
let matchedCards = 0;
const cards = document.querySelectorAll(".card");

function flipCard(card) {
    card.classList.add("flipped");
    card.innerText = card.dataset.card;
}

function unflipCard(card) {
    card.classList.remove("flipped");
    card.innerText = "";
}

function checkMatch() {
    const [firstCard, secondCard] = flippedCards;
    if (firstCard.dataset.card === secondCard.dataset.card) {
        matchedCards += 2;
        if (matchedCards === cards.length) {
            alert("You win! Play again.");
        }
    } else {
        setTimeout(() => {
            unflipCard(firstCard);
            unflipCard(secondCard);
        }, 1000);
    }
    flippedCards = [];
}

cards.forEach((card) => {
    card.addEventListener("click", () => {
        if (!card.classList.contains("flipped") && flippedCards.length < 2) {
            flipCard(card);
            flippedCards.push(card);
            if (flippedCards.length === 2) {
                checkMatch();
            }
        }
    });
});

// Shuffle cards
function shuffleCards() {
    const cardsArray = Array.from(cards);
    cardsArray.sort(() => Math.random() - 0.5);
    cardsArray.forEach((card) =>
        document.querySelector(".game-container").appendChild(card)
    );
}

shuffleCards();
```

##

[Back](index.md)
