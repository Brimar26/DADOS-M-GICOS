<div id="matevida-game">
    <div class="header-game">
        <span id="score">Puntos: 0</span>
        <span id="progress">Reto: 1 / 15</span>
    </div>

    <div class="dice-container">
        <div id="dado1" class="dice">?</div>
        <div id="dado2" class="dice">?</div>
    </div>

    <div id="challenge-area">
        <p id="instruction">¡Haz clic en Lanzar para empezar!</p>
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
    #matevida-game {
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
    .dice-container { display: flex; justify-content: center; gap: 15px; margin: 20px 0; }
    .dice {
        width: 70px; height: 70px; background: #2196F3; color: white;
        font-size: 35px; line-height: 70px; border-radius: 12px;
        box-shadow: 0 5px #1565C0; transition: transform 0.2s;
    }
    input { padding: 10px; width: 80px; font-size: 20px; border-radius: 8px; border: 2px solid #ccc; text-align: center; }
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
    let n1, n2, result, currentOpName;

    function speak(text) {
        const speech = new SpeechSynthesisUtterance(text);
        speech.lang = 'es-ES';
        window.speechSynthesis.speak(speech);
    }

    function startTurn() {
        document.getElementById('msg').innerText = "";
        
        // Determinar operación según el avance
        let opIndex;
        if (currentExercise <= 4) opIndex = 0;      // 1-4: Suma
        else if (currentExercise <= 8) opIndex = 1; // 5-8: Resta
        else if (currentExercise <= 12) opIndex = 2;// 9-12: Multi
        else opIndex = 3;                           // 13-15: Div

        const ops = ['+', '-', 'x', '÷'];
        const names = ['Suma', 'Resta', 'Multiplicación', 'División'];
        currentOpName = names[opIndex];

        n1 = Math.floor(Math.random() * 12) + 1;
        n2 = Math.floor(Math.random() * 12) + 1;

        if(opIndex === 1 && n1 < n2) [n1, n2] = [n2, n1];
        if(opIndex === 3) { n1 = n1 * n2; } 

        document.getElementById('dado1').innerText = n1;
        document.getElementById('dado2').innerText = n2;
        document.getElementById('instruction').innerText = `Reto ${currentExercise}: ¿Cuánto es ${n1} ${ops[opIndex]} ${n2}?`;
        document.getElementById('btn-check').disabled = false;
        document.getElementById('btn-roll').disabled = true;
        document.getElementById('answer').focus();
        speak(`Reto ${currentExercise}. ¿Cuánto es ${n1} ${currentOpName} ${n2}?`);
    }

    function checkResult() {
        let userAns = parseInt(document.getElementById('answer').value);
        
        if (currentExercise <= 4) result = n1 + n2;
        else if (currentExercise <= 8) result = n1 - n2;
        else if (currentExercise <= 12) result = n1 * n2;
        else result = n1 / n2;

        if(userAns === result) {
            score += 100;
            document.getElementById('msg').style.color = "green";
            document.getElementById('msg').innerText = "¡Correcto! Siguiente reto...";
            speak("¡Excelente!");
            
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
            document.getElementById('msg').innerText = "Error, intenta de nuevo";
            speak("Intenta otra vez.");
        }
        document.getElementById('score').innerText = `Puntos: ${score}`;
        document.getElementById('answer').value = "";
    }

    function endGame() {
        document.getElementById('instruction').innerHTML = "<h3>¡COMPLETASTE LOS 15 RETOS! 🏆</h3>";
        document.getElementById('btn-roll').classList.add('hidden');
        document.getElementById('btn-check').classList.add('hidden');
        document.getElementById('btn-reset').classList.remove('hidden');
        speak(`¡Felicidades Profe Mario! Has completado el circuito de 15 ejercicios con ${score} puntos.`);
    }

    function resetGame() {
        score = 0;
        currentExercise = 1;
        document.getElementById('score').innerText = "Puntos: 0";
        document.getElementById('progress').innerText = "Reto: 1 / 15";
        document.getElementById('btn-roll').classList.remove('hidden');
        document.getElementById('btn-roll').disabled = false;
        document.getElementById('btn-check').classList.remove('hidden');
        document.getElementById('btn-reset').classList.add('hidden');
        document.getElementById('instruction').innerText = "¡Haz clic en Lanzar para empezar!";
        document.getElementById('dado1').innerText = "?";
        document.getElementById('dado2').innerText = "?";
        document.getElementById('msg').innerText = "";
    }
</script>
