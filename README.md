<div id="matevida-game-hard">
    <div class="header-game">
        <span id="score">Puntos: 0</span>
        <span id="progress">Reto: 1 / 15</span>
    </div>

    <div class="dice-container">
        <div id="dado1" class="dice">?</div>
        <div id="dado2" class="dice">?</div>
        <div id="dado3" class="dice" style="background:#E91E63;">?</div>
    </div>

    <div id="challenge-area">
        <p id="instruction">¡Nivel Difícil Activado! Lanza los dados.</p>
        <div class="input-group">
            <input type="number" id="answer" placeholder="?">
            <button onclick="checkResult()" id="btn-check" disabled>Validar</button>
        </div>
    </div>

    <button onclick="startTurn()" id="btn-roll">Lanzar Dados 🎲</button>
    <button onclick="resetGame()" id="btn-reset" class="hidden">Reiniciar Desafío 🔄</button>
    
    <p id="msg"></p>
</div>

<style>
    #matevida-game-hard {
        background: #ffffff;
        border: 4px solid #ffd700;
        border-radius: 20px;
        padding: 25px;
        max-width: 450px;
        margin: 20px auto;
        text-align: center;
        font-family: 'Arial', sans-serif;
        box-shadow: 0 10px 20px rgba(0,0,0,0.1);
    }
    .header-game { display: flex; justify-content: space-between; font-weight: bold; color: #2c3e50; margin-bottom: 15px; }
    .dice-container { display: flex; justify-content: center; gap: 10px; margin: 20px 0; }
    .dice {
        width: 65px; height: 65px; background: #2196F3; color: white;
        font-size: 30px; line-height: 65px; border-radius: 12px;
        box-shadow: 0 5px #1565C0; font-weight: bold;
    }
    input { padding: 10px; width: 100px; font-size: 20px; border-radius: 8px; border: 2px solid #ccc; text-align: center; }
    button {
        padding: 12px 25px; font-size: 16px; border: none; border-radius: 8px;
        cursor: pointer; font-weight: bold; transition: 0.2s; margin: 5px;
    }
    #btn-roll { background: #4CAF50; color: white; }
    #btn-check { background: #ffd700; color: #333; }
    #btn-reset { background: #e74c3c; color: white; }
    .hidden { display: none; }
    #msg { height: 25px; font-weight: bold; margin-top: 15px; }
</style>

<script>
    let score = 0;
    let currentExercise = 1;
    const totalExercises = 15;
    let n1, n2, n3, result;

    function speak(text) {
        const speech = new SpeechSynthesisUtterance(text);
        speech.lang = 'es-ES';
        window.speechSynthesis.speak(speech);
    }

    function startTurn() {
        document.getElementById('msg').innerText = "";
        n1 = Math.floor(Math.random() * 20) + 10; // Números más grandes (10-30)
        n2 = Math.floor(Math.random() * 15) + 5;
        n3 = Math.floor(Math.random() * 5) + 2;

        document.getElementById('dado1').innerText = n1;
        document.getElementById('dado2').innerText = n2;
        document.getElementById('dado3').innerText = n3;

        let instructionText = "";
        
        if (currentExercise <= 5) {
            // Operaciones combinadas: (A + B) x C
            result = (n1 + n2) * n3;
            instructionText = `¿Cuánto es (${n1} + ${n2}) x ${n3}?`;
        } else if (currentExercise <= 10) {
            // Jerarquía: A x B - C
            result = (n1 * n3) - n2;
            instructionText = `¿Cuánto es ${n1} x ${n3} - ${n2}?`;
        } else {
            // Potencias básicas: A² + B
            n1 = Math.floor(Math.random() * 10) + 2;
            document.getElementById('dado1').innerText = n1;
            document.getElementById('dado2').innerText = "²";
            document.getElementById('dado3').innerText = n2;
            result = Math.pow(n1, 2) + n2;
            instructionText = `¿Cuánto es ${n1} al cuadrado más ${n2}?`;
        }

        document.getElementById('instruction').innerText = `Reto ${currentExercise}: ${instructionText}`;
        document.getElementById('btn-check').disabled = false;
        document.getElementById('btn-roll').disabled = true;
        document.getElementById('answer').focus();
        speak(`Reto ${currentExercise}. ${instructionText}`);
    }

    function checkResult() {
        let userAns = parseInt(document.getElementById('answer').value);
        
        if(userAns === result) {
            score += 250; // Más puntos por dificultad
            document.getElementById('msg').style.color = "green";
            document.getElementById('msg').innerText = "¡Nivel Experto! +250 puntos";
            speak("¡Excelente razonamiento!");
            
            if(currentExercise < totalExercises) {
                currentExercise++;
                document.getElementById('progress').innerText = `Reto: ${currentExercise} / 15`;
                document.getElementById('btn-roll').disabled = false;
                document.getElementById('btn-check').disabled = true;
            } else {
                endGame();
            }
        } else {
            document.getElementById('msg').style.color = "red";
            document.getElementById('msg').innerText = "Revisa la jerarquía de operaciones";
            speak("Analiza bien el orden de las operaciones.");
        }
        document.getElementById('score').innerText = `Puntos: ${score}`;
        document.getElementById('answer').value = "";
    }

    function endGame() {
        document.getElementById('instruction').innerHTML = "<h3>🏆 ¡MAESTRO DE LAS MATEMÁTICAS! 🏆</h3>";
        document.getElementById('btn-roll').classList.add('hidden');
        document.getElementById('btn-check').classList.add('hidden');
        document.getElementById('btn-reset').classList.remove('hidden');
        speak(`Increíble. Has superado el nivel difícil con ${score} puntos.`);
    }

    function resetGame() {
        score = 0; currentExercise = 1;
        document.getElementById('score').innerText = "Puntos: 0";
        document.getElementById('progress').innerText = "Reto: 1 / 15";
        document.getElementById('btn-roll').classList.remove('hidden');
        document.getElementById('btn-roll').disabled = false;
        document.getElementById('btn-check').classList.remove('hidden');
        document.getElementById('btn-reset').classList.add('hidden');
        document.getElementById('instruction').innerText = "¡Nivel Difícil Activado! Lanza los dados.";
        document.getElementById('dado1').innerText = "?";
        document.getElementById('dado2').innerText = "?";
        document.getElementById('dado3').innerText = "?";
        document.getElementById('msg').innerText = "";
    }
</script>
