<div id="matevida-facil">
    <div class="header-game">
        <span id="score">Puntos: 0</span>
        <span id="progress">Reto: 1 / 15</span>
    </div>

    <div class="dice-container">
        <div id="d1" class="dice">?</div>
        <div class="op-sign">+</div>
        <div id="d2" class="dice">?</div>
        <div class="op-sign">x</div>
        <div id="d3" class="dice" style="background:#4CAF50;">2</div>
    </div>

    <div id="challenge-area">
        <p id="instruction">¡Nivel Intermedio Amistoso! Lanza los dados.</p>
        <div class="input-group">
            <input type="number" id="answer" placeholder="?">
            <button onclick="checkResult()" id="btn-check" disabled>Validar</button>
        </div>
    </div>

    <button onclick="startTurn()" id="btn-roll">Lanzar Dados 🎲</button>
    <button onclick="resetGame()" id="btn-reset" class="hidden">Jugar de Nuevo 🔄</button>
    
    <p id="msg"></p>
</div>

<style>
    #matevida-facil {
        background: #ffffff;
        border: 4px solid #ffd700;
        border-radius: 20px;
        padding: 25px;
        max-width: 450px;
        margin: 20px auto;
        text-align: center;
        font-family: 'Arial', sans-serif;
    }
    .header-game { display: flex; justify-content: space-between; font-weight: bold; margin-bottom: 15px; }
    .dice-container { display: flex; justify-content: center; align-items: center; gap: 10px; margin: 20px 0; }
    .dice {
        width: 60px; height: 60px; background: #2196F3; color: white;
        font-size: 28px; line-height: 60px; border-radius: 10px;
        box-shadow: 0 4px #1565C0; font-weight: bold;
    }
    .op-sign { font-size: 24px; font-weight: bold; color: #555; }
    input { padding: 10px; width: 90px; font-size: 20px; border-radius: 8px; border: 2px solid #ccc; text-align: center; }
    button {
        padding: 12px 25px; font-size: 16px; border: none; border-radius: 8px;
        cursor: pointer; font-weight: bold; margin: 5px;
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
        
        // Números pequeños (1-10) para facilitar el cálculo mental
        n1 = Math.floor(Math.random() * 9) + 1;
        n2 = Math.floor(Math.random() * 5) + 1;
        n3 = (currentExercise <= 7) ? 2 : 3; // Multiplicadores fáciles (2 y 3)

        document.getElementById('d1').innerText = n1;
        document.getElementById('d2').innerText = n2;
        document.getElementById('d3').innerText = n3;

        result = (n1 + n2) * n3;
        let instructionText = `Suma ${n1} más ${n2} y el resultado multiplícalo por ${n3}`;

        document.getElementById('instruction').innerText = `Reto ${currentExercise}: (${n1} + ${n2}) x ${n3}`;
        document.getElementById('btn-check').disabled = false;
        document.getElementById('btn-roll').disabled = true;
        document.getElementById('answer').focus();
        speak(instructionText);
    }

    function checkResult() {
        let userAns = parseInt(document.getElementById('answer').value);
        
        if(userAns === result) {
            score += 150;
            document.getElementById('msg').style.color = "green";
            document.getElementById('msg').innerText = "¡Muy bien! Sigue así.";
            speak("¡Excelente respuesta!");
            
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
            document.getElementById('msg').innerText = "Prueba otra vez, tú puedes";
            speak("Casi lo logras, intenta de nuevo.");
        }
        document.getElementById('score').innerText = `Puntos: ${score}`;
        document.getElementById('answer').value = "";
    }

    function endGame() {
        document.getElementById('instruction').innerHTML = "<h3>¡FELICIDADES, LO LOGRASTE! ✨</h3>";
        document.getElementById('btn-roll').classList.add('hidden');
        document.getElementById('btn-check').classList.add('hidden');
        document.getElementById('btn-reset').classList.remove('hidden');
        speak(`¡Buen trabajo! Completaste los 15 retos con ${score} puntos.`);
    }

    function resetGame() {
        score = 0; currentExercise = 1;
        document.getElementById('score').innerText = "Puntos: 0";
        document.getElementById('progress').innerText = "Reto: 1 / 15";
        document.getElementById('btn-roll').classList.remove('hidden');
        document.getElementById('btn-roll').disabled = false;
        document.getElementById('btn-check').classList.remove('hidden');
        document.getElementById('btn-reset').classList.add('hidden');
        document.getElementById('instruction').innerText = "¡Lanza los dados!";
        document.getElementById('d1').innerText = "?";
        document.getElementById('d2').innerText = "?";
        document.getElementById('msg').innerText = "";
    }
</script>