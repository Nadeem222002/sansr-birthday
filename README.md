<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Happy Birthday Sansrita!</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="background">
        <img src="sansrita.jpg" alt="Sansrita" class="bg-image">
    </div>
    <div class="container">
        <div class="candles">
            <img src="candle-lit.png" alt="Candle 1" class="candle" id="candle1">
            <img src="candle-lit.png" alt="Candle 2" class="candle" id="candle2">
            <img src="candle-lit.png" alt="Candle 3" class="candle" id="candle3">
        </div>
        <div class="balloons">
            <img src="balloon-inflated.png" alt="Balloon" class="balloon" id="balloon">
        </div>
        <div class="message" id="birthdayMessage"></div>
    </div>
    <audio id="birthdaySong" src="happy-birthday.mp3"></audio>
    <script src="script.js"></script>
</body>
</html>
body {
    margin: 0;
    overflow: hidden; /* Prevent scrollbars */
    background-color: black;
}

.body.birthday-active{
    background-color: lightyellow;
}

.container {
    position: relative;
    width: 100vw;
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.candles {
    display: flex;
}

.candle {
    width: 80px; /* Adjust as needed */
    margin: 0 20px;
    transition: opacity 0.5s;
}

.balloons {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    display: none; /* Initially hidden */
}

.balloon {
    width: 200px; /* Adjust as needed */
}

.message {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 24px;
    color: white;
    display: none; /* Initially hidden */
    text-align: center;
}

.background{
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
}

.bg-image{
    width: 100%;
    height: 100%;
    object-fit: cover;
}
const candles = document.querySelectorAll('.candle');
const balloon = document.getElementById('balloon');
const birthdayMessage = document.getElementById('birthdayMessage');
const birthdaySong = document.getElementById('birthdaySong');
const body = document.querySelector('body');
let blowCount = 0;

navigator.mediaDevices.getUserMedia({ audio: true })
    .then(stream => {
        const audioContext = new AudioContext();
        const analyser = audioContext.createAnalyser();
        const microphone = audioContext.createMediaStreamSource(stream);
        microphone.connect(analyser);
        analyser.fftSize = 256;
        const bufferLength = analyser.frequencyBinCount;
        const dataArray = new Uint8Array(bufferLength);

        function detectBlow() {
            analyser.getByteFrequencyData(dataArray);
            let average = dataArray.reduce((a, b) => a + b) / bufferLength;

            if (average > 100) { // Adjust threshold as needed
                blowCount++;
                if (blowCount === 1) {
                    candles[0].src = 'candle-unlit.png';
                } else if (blowCount === 2) {
                    candles[1].src = 'candle-unlit.png';
                } else if (blowCount === 3) {
                    candles[2].src = 'candle-unlit.png';
                    setTimeout(() => {
                        balloon.src = 'balloon-popped.png';
                        balloon.style.display = 'block';
                        birthdayMessage.textContent = "Happy Birthday, Sansrita! May your day be filled with joy!";
                        birthdayMessage.style.display = 'block';
                        birthdaySong.play();
                        body.classList.add("birthday-active");
                    }, 500);
                }
            }
            requestAnimationFrame(detectBlow);
        }
        detectBlow();
    })
    .catch(err => {
        console.error('Microphone access denied:', err);
        alert("Please allow microphone access for the full experience.");
    });
