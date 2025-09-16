<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hiragana Flashcards</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
        }

        .card-container {
            perspective: 1000px;
            height: 20rem; /* Fixed height to prevent layout shift */
        }

        .flashcard {
            width: 100%;
            height: 100%;
            transform-style: preserve-3d;
            transition: transform 0.6s;
            cursor: pointer;
            position: relative;
        }

        .flashcard.is-flipped {
            transform: rotateY(180deg);
        }

        .flashcard-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 2rem;
            box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
            border-radius: 1rem;
        }

        .flashcard-front {
            background-color: #ffffff;
            color: #333;
        }

        .flashcard-back {
            background-color: #4CAF50; /* A pleasant green color */
            color: white;
            transform: rotateY(180deg);
            background-image: linear-gradient(135deg, #4CAF50, #8BC34A);
        }

        .no-select {
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">

    <div class="text-center w-full max-w-2xl">
        <h1 class="text-3xl sm:text-4xl font-bold text-gray-800 mb-6">Hiragana Flashcards</h1>

        <div class="card-container w-full h-80 mb-6">
            <div id="flashcard" class="flashcard">
                <div class="flashcard-face flashcard-front no-select">
                    <div id="japanese-text" class="text-5xl font-bold text-center"></div>
                </div>
                <div class="flashcard-face flashcard-back no-select">
                    <div id="translation-text" class="text-3xl text-center"></div>
                </div>
            </div>
        </div>

        <div class="flex flex-col sm:flex-row justify-center items-center gap-4">
            <button id="prev-button" class="bg-gray-400 hover:bg-gray-500 text-white font-bold py-3 px-6 rounded-full transition duration-300 transform hover:scale-105 shadow-lg">
                Previous
            </button>
            <button id="shuffle-button" class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-6 rounded-full transition duration-300 transform hover:scale-105 shadow-lg">
                Shuffle Deck
            </button>
            <button id="next-button" class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-6 rounded-full transition duration-300 transform hover:scale-105 shadow-lg">
                Next
            </button>
        </div>
        <div id="card-counter" class="text-gray-600 text-lg font-medium mt-4"></div>

    </div>

    <script>
        const cardData = [
            { japanese: "あ", translation: "a" },
            { japanese: "い", translation: "i" },
            { japanese: "う", translation: "u" },
            { japanese: "え", translation: "e" },
            { japanese: "お", translation: "o" },
            { japanese: "か", translation: "ka" },
            { japanese: "き", translation: "ki" },
            { japanese: "く", translation: "ku" },
            { japanese: "け", translation: "ke" },
            { japanese: "こ", translation: "ko" },
            { japanese: "さ", translation: "sa" },
            { japanese: "し", translation: "shi" },
            { japanese: "す", translation: "su" },
            { japanese: "せ", translation: "se" },
            { japanese: "そ", translation: "so" },
            { japanese: "た", translation: "ta" },
            { japanese: "ち", translation: "chi" },
            { japanese: "つ", translation: "tsu" },
            { japanese: "て", translation: "te" },
            { japanese: "と", translation: "to" },
            { japanese: "な", translation: "na" },
            { japanese: "に", translation: "ni" },
            { japanese: "ぬ", translation: "nu" },
            { japanese: "ね", translation: "ne" },
            { japanese: "の", translation: "no" },
            { japanese: "は", translation: "ha" },
            { japanese: "ひ", translation: "hi" },
            { japanese: "ふ", translation: "fu" },
            { japanese: "へ", translation: "he" },
            { japanese: "ほ", translation: "ho" },
            { japanese: "ま", translation: "ma" },
            { japanese: "み", translation: "mi" },
            { japanese: "む", translation: "mu" },
            { japanese: "め", translation: "me" },
            { japanese: "も", translation: "mo" },
            { japanese: "や", translation: "ya" },
            { japanese: "ゆ", translation: "yu" },
            { japanese: "よ", translation: "yo" },
            { japanese: "ら", translation: "ra" },
            { japanese: "り", translation: "ri" },
            { japanese: "る", translation: "ru" },
            { japanese: "れ", translation: "re" },
            { japanese: "ろ", translation: "ro" },
            { japanese: "わ", translation: "wa" },
            { japanese: "を", translation: "o" },
            { japanese: "ん", translation: "n" },
            { japanese: "が", translation: "ga" },
            { japanese: "ぎ", translation: "gi" },
            { japanese: "ぐ", translation: "gu" },
            { japanese: "げ", translation: "ge" },
            { japanese: "ご", translation: "go" },
            { japanese: "ざ", translation: "za" },
            { japanese: "じ", translation: "ji" },
            { japanese: "ず", translation: "zu" },
            { japanese: "ぜ", translation: "ze" },
            { japanese: "ぞ", translation: "zo" },
            { japanese: "だ", translation: "da" },
            { japanese: "ぢ", translation: "ji" },
            { japanese: "づ", translation: "zu" },
            { japanese: "で", translation: "de" },
            { japanese: "ど", translation: "do" },
            { japanese: "ば", translation: "ba" },
            { japanese: "び", translation: "bi" },
            { japanese: "ぶ", translation: "bu" },
            { japanese: "べ", translation: "be" },
            { japanese: "ぼ", translation: "bo" },
            { japanese: "ぱ", translation: "pa" },
            { japanese: "ぴ", translation: "pi" },
            { japanese: "ぷ", translation: "pu" },
            { japanese: "ぺ", translation: "pe" },
            { japanese: "ぽ", translation: "po" },
            { japanese: "きゃ", translation: "kya" },
            { japanese: "きゅ", translation: "kyu" },
            { japanese: "きょ", translation: "kyo" },
            { japanese: "しゃ", translation: "sha" },
            { japanese: "しゅ", translation: "shu" },
            { japanese: "しょ", translation: "sho" },
            { japanese: "ちゃ", translation: "cha" },
            { japanese: "ちゅ", translation: "chu" },
            { japanese: "ちょ", translation: "cho" },
            { japanese: "にゃ", translation: "nya" },
            { japanese: "にゅ", translation: "nyu" },
            { japanese: "にょ", translation: "nyo" },
            { japanese: "ひゃ", translation: "hya" },
            { japanese: "ひゅ", translation: "hyu" },
            { japanese: "ひょ", translation: "hyo" },
            { japanese: "みゃ", translation: "mya" },
            { japanese: "みゅ", translation: "myu" },
            { japanese: "みょ", translation: "myo" },
            { japanese: "りゃ", translation: "rya" },
            { japanese: "りゅ", translation: "ryu" },
            { japanese: "りょ", translation: "ryo" },
            { japanese: "ぎゃ", translation: "gya" },
            { japanese: "ぎゅ", translation: "gyu" },
            { japanese: "ぎょ", translation: "gyo" },
            { japanese: "じゃ", translation: "ja" },
            { japanese: "じゅ", translation: "ju" },
            { japanese: "じょ", translation: "jo" },
            { japanese: "びゃ", translation: "bya" },
            { japanese: "びゅ", translation: "byu" },
            { japanese: "びょ", translation: "byo" },
            { japanese: "ぴゃ", translation: "pya" },
            { japanese: "ぴゅ", translation: "pyu" },
            { japanese: "ぴょ", translation: "pyo" }
        ];

        const flashcardElement = document.getElementById('flashcard');
        const japaneseTextElement = document.getElementById('japanese-text');
        const translationTextElement = document.getElementById('translation-text');
        const shuffleButton = document.getElementById('shuffle-button');
        const nextButton = document.getElementById('next-button');
        const prevButton = document.getElementById('prev-button');
        const cardCounter = document.getElementById('card-counter');

        let currentDeck = [...cardData]; // Create a copy to shuffle
        let currentIndex = 0;

        /**
         * Shuffles the cards using the Fisher-Yates (Knuth) shuffle algorithm.
         * @param {Array} array The array to shuffle.
         */
        function shuffle(array) {
            let currentIndex = array.length;
            let randomIndex;

            while (currentIndex !== 0) {
                randomIndex = Math.floor(Math.random() * currentIndex);
                currentIndex--;
                [array[currentIndex], array[randomIndex]] = [
                    array[randomIndex], array[currentIndex]];
            }
            return array;
        }

        /**
         * Displays the card at the given index.
         * @param {number} index The index of the card to display.
         */
        function showCard(index) {
            if (index < 0 || index >= currentDeck.length) {
                console.error("Invalid card index.");
                return;
            }
            
            // Only flip the card back if it's currently flipped
            if (flashcardElement.classList.contains('is-flipped')) {
                flashcardElement.classList.remove('is-flipped');
                // Use a timeout to ensure the flip animation completes before changing content
                setTimeout(() => {
                    const card = currentDeck[index];
                    japaneseTextElement.textContent = card.japanese;
                    translationTextElement.textContent = card.translation;
                    cardCounter.textContent = `${index + 1} / ${currentDeck.length}`;
                }, 300); // 300ms is half of the transition duration
            } else {
                const card = currentDeck[index];
                japaneseTextElement.textContent = card.japanese;
                translationTextElement.textContent = card.translation;
                cardCounter.textContent = `${index + 1} / ${currentDeck.length}`;
            }
        }

        // Event listener for the card click - now only flips the card
        flashcardElement.addEventListener('click', () => {
            flashcardElement.classList.toggle('is-flipped');
        });

        // Event listener for the "Next" button click
        nextButton.addEventListener('click', () => {
            currentIndex = (currentIndex + 1) % currentDeck.length;
            showCard(currentIndex);
        });

        // Event listener for the "Previous" button click
        prevButton.addEventListener('click', () => {
            currentIndex = (currentIndex - 1 + currentDeck.length) % currentDeck.length;
            showCard(currentIndex);
        });

        // Event listener for the shuffle button click
        shuffleButton.addEventListener('click', () => {
            currentDeck = shuffle(currentDeck);
            currentIndex = 0;
            showCard(currentIndex);
        });
        
        // Initial setup on page load
        document.addEventListener('DOMContentLoaded', () => {
            showCard(currentIndex);
        });
    </script>
</body>
</html>
