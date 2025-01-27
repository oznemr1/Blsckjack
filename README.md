<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blackjack</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #2c3e50;
            color: white;
            text-align: center;
        }
        #gameContainer {
            display: none;
            padding-top: 20px;
        }
        .button {
            background-color: #3498db;
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 18px;
            cursor: pointer;
            margin: 10px;
            border-radius: 5px;
        }
        .button:hover {
            background-color: #2980b9;
        }
        input {
            padding: 10px;
            font-size: 16px;
            margin: 5px;
            border-radius: 5px;
        }
        .header {
            font-size: 24px;
            margin-top: 20px;
        }
        .result {
            font-size: 30px;
            margin-top: 20px;
        }
    </style>
</head>
<body>

    <!-- Giriş Ekranı -->
    <div id="loginContainer">
        <h2>Blackjack - Giriş</h2>
        <input type="text" id="username" placeholder="Kullanıcı Adı" required>
        <input type="password" id="password" placeholder="Şifre" required>
        <button class="button" onclick="login()">Giriş Yap</button>
        <p id="error" style="color: red;"></p>
    </div>

    <!-- Oyun Alanı -->
    <div id="gameContainer">
        <h2>Blackjack Oyunu</h2>
        <div id="gameInfo">
            <p id="playerBalance">Bakiye: 1000</p>
            <button class="button" onclick="startGame()">Oyunu Başlat</button>
        </div>
        <div id="cardsArea" style="display: none;">
            <p id="playerCards">Kartlarınız: </p>
            <p id="dealerCards">Krupiyer Kartları: </p>
            <button class="button" onclick="playerAction('hit')">Kart Çek</button>
            <button class="button" onclick="playerAction('stand')">Bekle</button>
        </div>
        <div id="resultArea" style="display: none;">
            <p id="gameResult" class="result"></p>
            <button class="button" onclick="restartGame()">Yeniden Başlat</button>
        </div>
    </div>

    <script>
        let users = {
            "emir": "12345",
            "yasin.xw": "78910",
            "alper.cbn": "09876"
        };
        let balance = 1000;
        let gameStarted = false;
        let playerCards = [];
        let dealerCards = [];
        
        function login() {
            let username = document.getElementById("username").value;
            let password = document.getElementById("password").value;
            let errorMessage = document.getElementById("error");

            if (users[username] === password) {
                document.getElementById("loginContainer").style.display = "none";
                document.getElementById("gameContainer").style.display = "block";
                gameStarted = false;
                resetGame();
            } else {
                errorMessage.innerText = "Geçersiz kullanıcı adı veya şifre!";
            }
        }

        function startGame() {
            if (gameStarted) {
                return;
            }

            gameStarted = true;
            balance -= 50; // Oyuna başlamak için bahis
            document.getElementById("playerBalance").innerText = "Bakiye: " + balance;
            document.getElementById("gameInfo").style.display = "none";
            document.getElementById("cardsArea").style.display = "block";
            document.getElementById("resultArea").style.display = "none";

            // Kartları dağıt
            playerCards = [getCard(), getCard()];
            dealerCards = [getCard(), getCard()];
            updateCardsDisplay();
        }

        function getCard() {
            const cards = [2, 3, 4, 5, 6, 7, 8, 9, 10, 10, 10, 10, 11]; // Kart değerleri
            return cards[Math.floor(Math.random() * cards.length)];
        }

        function updateCardsDisplay() {
            document.getElementById("playerCards").innerText = "Kartlarınız: " + playerCards.join(", ");
            document.getElementById("dealerCards").innerText = "Krupiyer Kartları: " + dealerCards[0] + " ve [Gizli]";
        }

        function playerAction(action) {
            if (action === "hit") {
                playerCards.push(getCard());  // Kart çek
                updateCardsDisplay();

                // Eğer oyuncunun toplamı 21'i geçerse oyun biter
                if (getTotal(playerCards) > 21) {
                    evaluateGame();
                }
            } else if (action === "stand") {
                // Bekle, Krupiyer Kartları
                dealerPlay();
            }
        }

        function dealerPlay() {
            while (getTotal(dealerCards) < 17) {
                dealerCards.push(getCard());
            }
            evaluateGame();
        }

        function evaluateGame() {
            let playerTotal = getTotal(playerCards);
            let dealerTotal = getTotal(dealerCards);

            let result = "";
            if (playerTotal > 21) {
                result = "Kaybettiniz! (21'i geçtiniz)";
            } else if (dealerTotal > 21) {
                result = "Kazandınız! (Krupiyer 21'i geçti)";
            } else if (playerTotal > dealerTotal) {
                result = "Kazandınız!";
            } else if (playerTotal < dealerTotal) {
                result = "Kaybettiniz!";
            } else {
                result = "Beraberlik!";
            }

            // Sonuçları göster
            document.getElementById("gameResult").innerText = result;
            document.getElementById("cardsArea").style.display = "none";
            document.getElementById("resultArea").style.display = "block";
        }

        function getTotal(cards) {
            let total = 0;
            let aceCount = 0;

            for (let i = 0; i < cards.length; i++) {
                total += cards[i];
                if (cards[i] === 11) aceCount++; // As kartlarını say
            }

            // Eğer toplam 21'den fazla olursa, Asları 11'den 1'e çevir
            while (total > 21 && aceCount > 0) {
                total -= 10;
                aceCount--;
            }

            return total;
        }

        function restartGame() {
            gameStarted = false;
            resetGame();
            document.getElementById("gameInfo").style.display = "block";
            document.getElementById("resultArea").style.display = "none";
        }

        function resetGame() {
            playerCards = [];
            dealerCards = [];
            document.getElementById("playerCards").innerText = "Kartlarınız: ";
            document.getElementById("dealerCards").innerText = "Krupiyer Kartları: ";
            document.getElementById("playerBalance").innerText = "Bakiye: " + balance;
        }
    </script>

</body>
</html>
