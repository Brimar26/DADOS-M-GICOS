<div id="matevida-completo">
    <div class="header-game">
        <span id="score">Puntos: 0</span>
        <span id="progress">Reto: 1 / 15</span>
    </div>

    <div class="dice-container">
        <div id="d1" class="dice">?</div>
        <div id="op-display" class="op-sign">?</div>
        <div id="d2" class="dice">?</div>
    </div>

    <div id="challenge-area">
        <p id="instruction">¡Circuito de Operaciones! Lanza los dados.</p>
        <div class="input-group">
            <input type="number" id="answer" placeholder="?">
            <button onclick="checkResult()" id="btn-check" disabled>Validar</button>
        </div>
    </div>

    <button onclick="animateDice()" id="btn-roll">Lanzar Dados 🎲</button>
    <button onclick="resetGame()" id="btn-reset" class="hidden">Reiniciar Todo 🔄</button>
    
    <p id="msg"></p>
</div>

<style>
    @keyframes shake {
        0% { transform: translate(1px, 1px) rotate(0deg); }
        20% { transform: translate(-3px, 0px) rotate(1deg); }
        40% { transform: translate(3px, 2px) rotate(-1deg); }
        60% { transform: translate(-3px, 1px) rotate(0deg); }
        80% { transform: translate(3px, 1px) rotate(-1deg); }
        100% { transform: translate(1px, -2px) rotate(1deg); }
    }
    .shaking { animation: shake 0.5s infinite; }
    #matevida-completo {
        background: #ffffff; border: 4px solid #ffd700; border-radius: 20px;
        padding: 25px; max-width: 450px; margin: 20px auto; text-align: center;
        font-family: 'Arial', sans-serif; box-shadow: 0 10px 20px rgba(0,0,0,0.1);
    }
    .header-game { display: flex; justify-content: space-between; font-weight: bold; margin-bottom: 15px; }
    .dice-container { display: flex; justify-content: center; align-items: center; gap: 15px; margin: 20px 0; }
    .dice {
        width: 70px; height: 70px; background: #2196F3; color: white;
        font-size: 32px; line-height: 70px; border-radius: 12px;
        box-shadow: 0 5px #1565C0; font-weight: bold;
    }
    .op-sign { font-size: 30px; font-weight: bold; color: #E91E63; }
    input { padding: 10px; width: 90px; font-size: 20px; border-radius: 8px; border: 2px solid #ccc; text-align: center; }
    button { padding: 12px 25px; font-size: 16px; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; margin: 5px; }
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
    let n1, n2, result, currentOp;

    function speak(text) {
        const speech = new SpeechSynthesisUtterance(text);
        speech.lang = 'es-ES';
        window.speechSynthesis.speak(speech);
    }

    function animateDice() {
        const diceElements = document.querySelectorAll('.dice');
        diceElements.forEach(d => d.classList.add('shaking'));
        document.getElementById('btn-roll').disabled = true;
        document.getElementById('msg').innerText = "Mezclando operaciones...";
        setTimeout(() => {
            diceElements.forEach(d => d.classList.remove('shaking'));
            startTurn();
        }, 700);
    }

    function startTurn() {
        document.getElementById('msg').innerText = "";
        const operations = ['+', '-', 'x', '÷'];
        const opNames = ['Suma', 'Resta', 'Multiplicación', 'División'];
        
        // Selección aleatoria de operación
        const randIndex = Math.floor(Math.random() * 4);
        currentOp = operations[randIndex];
        let opName = opNames[randIndex];

        n1 = Math.floor(Math.random() * 15) + 1;
        n2 = Math.floor(Math.random() * 12) + 1;

        // Lógica de control para Resta y División
        if (currentOp === '-') {
            if (n1 < n2) [n1, n2] = [n2, n1];
            result = n1 - n2;
        } else if (currentOp === '÷') {
            n1 = n1 * n2; // Garantiza división exacta
            result = n1 / n2;
        } else if (currentOp === 'x') {
            result = n1 * n2;
        } else {
            result = n1 + n2;
        }

        document.getElementById('d1').innerText = n1;
        document.getElementById('d2').innerText = n2;
        document.getElementById('op-display').innerText = currentOp;
        
        let instructionText = `Reto ${currentExercise}: ¿Cuánto es ${n1} ${currentOp} ${n2}?`;
        document.getElementById('instruction').innerText = instructionText;
        document.getElementById('btn-check').disabled = false;
        document.getElementById('answer').focus();
        speak(`Reto ${currentExercise}. ¿Cuánto es ${n1} ${opName} ${n2}?`);
    }

    function checkResult() {
        let userAns = parseInt(document.getElementById('answer').value);
        
        if(userAns === result) {
            score += 200;
            document.getElementById('msg').style.color = "green";
            document.getElementById('msg').innerText = "¡Excelente! +200 puntos";
            speak("¡Correcto!");
            
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
            document.getElementById('msg').innerText = "Casi... intenta de nuevo";
            speak("Inténtalo otra vez.");
        }
        document.getElementById('score').innerText = `Puntos: ${score}`;
        document.getElementById('answer').value = "";
    }

    function endGame() {
        document.getElementById('instruction').innerHTML = "<h3>🏆 ¡MAESTRO DE LAS 4 OPERACIONES! 🏆</h3>";
        document.getElementById('btn-roll').classList.add('hidden');
        document.getElementById('btn-check').classList.add('hidden');
        document.getElementById('btn-reset').classList.remove('hidden');
        speak(`¡Felicidades! Has dominado las cuatro operaciones con un puntaje de ${score}.`);
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
        document.getElementById('op-display').innerText = "?";
        document.getElementById('msg').innerText = "";
    }
</script>