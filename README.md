
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aventura Espacial Matemática 2D</title>
    <style>
        :root {
            --space-bg: #0F172A;
            --neon-blue: #38BDF8;
            --neon-green: #4ADE80;
            --neon-red: #F87171;
            --neon-purple: #C084FC;
            --text-light: #F8FAFC;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }

        body {
            background-color: var(--space-bg);
            background-image: 
                radial-gradient(white, rgba(255,255,255,.2) 2px, transparent 40px),
                radial-gradient(white, rgba(255,255,255,.15) 1px, transparent 30px),
                radial-gradient(white, rgba(255,255,255,.1) 2px, transparent 40px);
            background-size: 550px 550px, 350px 350px, 250px 250px;
            background-position: 0 0, 40px 60px, 130px 270px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
            overflow: hidden;
        }

        .contenedor-videojuego {
            background: rgba(15, 23, 42, 0.9);
            width: 100%;
            max-width: 750px;
            border-radius: 24px;
            border: 4px solid var(--neon-blue);
            box-shadow: 0 0 30px rgba(56, 189, 248, 0.3);
            overflow: hidden;
            position: relative;
        }

        .barra-energia {
            background: rgba(255, 255, 255, 0.05);
            padding: 15px 25px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid rgba(56, 189, 248, 0.2);
            color: var(--text-light);
            font-size: 18px;
            font-weight: bold;
        }

        /* Pantallas de Control de Flujo */
        .pantalla {
            padding: 40px 30px;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 25px;
        }

        #escenario-juego { display: none; }
        #escenario-final { display: none; }

        .titulo-neon {
            font-size: 38px;
            color: var(--text-light);
            text-shadow: 0 0 10px var(--neon-blue), 0 0 20px var(--neon-blue);
            margin-bottom: 10px;
        }

        .texto-descripcion {
            color: #94A3B8;
            font-size: 16px;
            max-width: 500px;
            line-height: 1.6;
        }

        /* Selector de Tipo de Operación Creado para Niños */
        .cuadricula-operaciones {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            width: 100%;
            max-width: 500px;
        }

        .btn-modo {
            background: rgba(255, 255, 255, 0.03);
            color: var(--text-light);
            border: 2px solid rgba(255, 255, 255, 0.2);
            padding: 20px;
            border-radius: 16px;
            font-size: 20px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s ease;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 8px;
        }

        .btn-modo[data-tipo="suma"]:hover { border-color: var(--neon-green); box-shadow: 0 0 15px rgba(74, 222, 128, 0.4); background: rgba(74, 222, 128, 0.05); }
        .btn-modo[data-tipo="resta"]:hover { border-color: var(--neon-purple); box-shadow: 0 0 15px rgba(192, 132, 252, 0.4); background: rgba(192, 132, 252, 0.05); }
        .btn-modo[data-tipo="multi"]:hover { border-color: var(--neon-blue); box-shadow: 0 0 15px rgba(56, 189, 248, 0.4); background: rgba(56, 189, 248, 0.05); }
        .btn-modo[data-tipo="divi"]:hover { border-color: var(--neon-red); box-shadow: 0 0 15px rgba(248, 113, 113, 0.4); background: rgba(248, 113, 113, 0.05); }

        .btn-modo span { font-size: 32px; }

        /* Área de Combate 2D */
        .cabina-operacion {
            background: linear-gradient(180deg, rgba(30, 41, 59, 0.8) 0%, rgba(15, 23, 42, 0.9) 100%);
            border: 3px solid rgba(255, 255, 255, 0.1);
            border-radius: 24px;
            width: 100%;
            max-width: 480px;
            padding: 20px;
            font-size: 52px;
            color: var(--text-light);
            text-shadow: 0 0 15px rgba(255,255,255,0.4);
            letter-spacing: 2px;
            box-shadow: inset 0 0 20px rgba(0,0,0,0.6);
        }

        .arena-espacial {
            display: flex;
            justify-content: space-between;
            align-items: center;
            width: 100%;
            margin: 25px 0;
            position: relative;
        }

        /* Avatar del Navegante Cósmico */
        .avatar-astronauta {
            font-size: 70px;
            width: 110px;
            height: 110px;
            background: rgba(255,255,255,0.05);
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            border: 3px dashed var(--neon-blue);
            animation: flotar 3s ease-in-out infinite;
        }

        @keyframes flotar {
            0%, 100% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-12px) rotate(3deg); }
        }

        .disparar-animacion { animation: laserFuego 0.3s ease-out; }
        @keyframes laserFuego {
            0% { transform: scale(1); filter: brightness(1.5); }
            50% { transform: scale(0.9) translateX(10px); }
            100% { transform: scale(1); filter: brightness(1); }
        }

        /* Asteroides de Respuesta Cósmica */
        .zona-asteroides {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            width: 100%;
            max-width: 400px;
        }

        .asteroide {
            background: linear-gradient(135deg, #334155 0%, #1E293B 100%);
            border: 3px solid #475569;
            color: var(--text-light);
            font-size: 28px;
            font-weight: bold;
            padding: 22px;
            border-radius: 50% 40% 50% 45%; /* Forma irregular de roca espacial */
            cursor: pointer;
            box-shadow: 0 6px 0 #0F172A, 0 10px 20px rgba(0,0,0,0.4);
            transition: all 0.1s ease;
            text-align: center;
        }

        .asteroide:hover {
            transform: scale(1.04);
            border-color: var(--neon-blue);
            box-shadow: 0 6px 0 #0F172A, 0 0 15px rgba(56, 189, 248, 0.4);
        }

        .asteroide:active {
            transform: translateY(4px);
            box-shadow: 0 2px 0 #0F172A;
        }

        /* Impactos Láser Visuales */
        .asteroide-destruido {
            background: var(--neon-green) !important;
            border-color: #22C55E !important;
            box-shadow: 0 0 25px var(--neon-green) !important;
            transform: scale(0.9);
            opacity: 0;
            transition: all 0.4s ease-out;
            pointer-events: none;
        }

        .asteroide-fallido {
            background: var(--neon-red) !important;
            border-color: #EF4444 !important;
            box-shadow: 0 0 20px var(--neon-red) !important;
            transform: shake 0.3s ease;
            pointer-events: none;
        }

        /* Botón de control de flujo estándar */
        .btn-unificado {
            background: var(--neon-green);
            color: #0F172A;
            font-size: 22px;
            font-weight: bold;
            padding: 16px 45px;
            border: none;
            border-radius: 14px;
            cursor: pointer;
            box-shadow: 0 5px 0 #15803D;
            transition: all 0.1s;
        }
        .btn-unificado:active { transform: translateY(4px); box-shadow: none; }

        .marcador-puntuacion-final {
            font-size: 72px;
            color: var(--neon-green);
            font-weight: 900;
            text-shadow: 0 0 15px rgba(74,222,128,0.3);
            margin: 10px 0;
        }
    </style>
</head>
<body>

    <div class="contenedor-videojuego">
        <div class="barra-energia">
            <span id="txt-modo-activo">🚀 Misión Espacial</span>
            <span>⏱️ Tiempo: <span id="reloj-digital">0s</span></span>
        </div>

        <div id="escenario-inicio" class="pantalla">
            <h1 class="titulo-neon">Academia Galáctica 👾</h1>
            <p class="texto-descripcion">
                ¡Atención cadete de 3ro y 4to grado! Los asteroides numéricos amenazan la nave espacial. Selecciona una operación para activar tus cañones láser automáticos. ¡Debes destruir 30 objetivos!
            </p>

            <div class="cuadricula-operaciones">
                <button class="btn-modo" data-tipo="suma" onclick="comenzarMision('suma')">
                    <span>➕</span> Sumas Estelares
                </button>
                <button class="btn-modo" data-tipo="resta" onclick="comenzarMision('resta')">
                    <span>➖</span> Restas Cuánticas
                </button>
                <button class="btn-modo" data-tipo="multi" onclick="comenzarMision('multi')">
                    <span>✖️</span> Hiper-Multiplicación
                </button>
                <button class="btn-modo" data-tipo="divi" onclick="comenzarMision('divi')">
                    <span>➗</span> Divisiones Gamma
                </button>
            </div>
        </div>

        <div id="escenario-juego" class="pantalla">
            <div style="color: #94A3B8; font-weight: bold; font-size: 16px;">
                Misión Progresiva: <span id="ronda-numero">1</span> / 30
            </div>

            <div class="cabina-operacion" id="visor-operacion">
                0 + 0
            </div>

            <div class="arena-espacial">
                <div class="avatar-astronauta" id="navegante">🧑‍🚀</div>

                <div class="zona-asteroides" id="bloque-asteroides">
                    </div>
            </div>

            <div style="min-height: 25px; font-weight: bold; font-size: 18px;" id="consola-feedback"></div>
        </div>

        <div id="escenario-final" class="pantalla">
            <h2 class="titulo-neon">¡Sistema Defendido! 🌌</h2>
            <div class="marcador-puntuacion-final" id="porcentaje-eficiencia">0%</div>
            <p class="texto-descripcion" id="resumen-combate"></p>
            <button class="btn-unificado" onclick="regresarAlMenu()">Volver a la Base 🔄</button>
        </div>
    </div>

    <script>
        // Configuración centralizada del juego
        let modoJuego = ''; 
        let listaOperaciones = [];
        let indicePregunta = 0;
        const totalRondasMaximas = 30;

        let respuestasCorrectasSinFallo = 0;
        let fallosPorPregunta = 0;
        let segundero = 0;
        let idReloj = null;
        let ejercicioActual = null;

        // Contexto de Audio Web nativo espacial
        let ctxAudio = null;
        function iniciarAudioEspacial() {
            if (!ctxAudio) ctxAudio = new (window.AudioContext || window.webkitAudioContext)();
        }

        function sonidoEfectoLaser(tipo) {
            iniciarAudioEspacial();
            if (!ctxAudio) return;

            const osc = ctxAudio.createOscillator();
            const gainNode = ctxAudio.createGain();
            osc.connect(gainNode);
            gainNode.connect(ctxAudio.destination);

            if (tipo === 'laser_exito') {
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(587.33, ctxAudio.currentTime); // Nota Re5
                osc.frequency.exponentialRampToValueAtTime(1174.66, ctxAudio.currentTime + 0.25);
                gainNode.gain.setValueAtTime(0.3, ctxAudio.currentTime);
                gainNode.gain.exponentialRampToValueAtTime(0.01, ctxAudio.currentTime + 0.25);
                osc.start(); osc.stop(ctxAudio.currentTime + 0.25);
            } else if (tipo === 'laser_error') {
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(180, ctxAudio.currentTime);
                osc.frequency.linearRampToValueAtTime(60, ctxAudio.currentTime + 0.35);
                gainNode.gain.setValueAtTime(0.4, ctxAudio.currentTime);
                gainNode.gain.exponentialRampToValueAtTime(0.01, ctxAudio.currentTime + 0.35);
                osc.start(); osc.stop(ctxAudio.currentTime + 0.35);
            }
        }

        // Fábrica matemática adaptada para niveles pedagógicos de 3ro y 4to primaria
        function generarMisionesGalacticas(tipo) {
            let misiones = [];
            for (let k = 0; k < 60; k++) { // Generamos un lote grande para barajar aleatoriamente
                let num1 = 0, num2 = 0, respuesta = 0, textoOp = '';

                switch(tipo) {
                    case 'suma':
                        num1 = Math.floor(Math.random() * 80) + 15; // Rango de 15 a 95
                        num2 = Math.floor(Math.random() * 80) + 10;
                        respuesta = num1 + num2;
                        textoOp = `${num1} + ${num2}`;
                        break;
                    case 'resta':
                        num1 = Math.floor(Math.random() * 90) + 30;
                        num2 = Math.floor(Math.random() * (num1 - 5)) + 5; // Evita respuestas negativas
                        respuesta = num1 - num2;
                        textoOp = `${num1} − ${num2}`;
                        break;
                    case 'multi':
                        num1 = Math.floor(Math.random() * 11) + 2;  // Tablas del 2 al 12
                        num2 = Math.floor(Math.random() * 10) + 1;  
                        respuesta = num1 * num2;
                        textoOp = `${num1} × ${num2}`;
                        break;
                    case 'divi':
                        num2 = Math.floor(Math.random() * 8) + 2;   // Divisor del 2 al 9
                        respuesta = Math.floor(Math.random() * 10) + 1; // Cociente del 1 al 10
                        num1 = num2 * respuesta; // Construcción inversa para asegurar división exacta
                        textoOp = `${num1} ÷ ${num2}`;
                        break;
                }
                misiones.push({ texto: textoOp, resultado: respuesta });
            }
            // Barajado completo aleatorio cada vez que inicia una nueva partida
            return misiones.sort(() => Math.random() - 0.5).slice(0, totalRondasMaximas);
        }

        function arrancarCronometro() {
            clearInterval(idReloj);
            idReloj = setInterval(() => {
                segundero++;
                document.getElementById('reloj-digital').innerText = segundero + 's';
            }, 1000);
        }

        // Activación de la pantalla de combate matemático
        function comenzarMision(tipoSeleccionado) {
            iniciarAudioEspacial();
            modoJuego = tipoSeleccionado;
            
            const etiquetas = { suma: "Suma Estelar 🟢", resta: "Resta Cuántica 🟣", multi: "Hiper-Multiplicación 🔵", divi: "División Gamma 🔴" };
            document.getElementById('txt-modo-activo').innerText = etiquetas[tipoSeleccionado];

            listaOperaciones = generarMisionesGalacticas(tipoSeleccionado);
            indicePregunta = 0;
            respuestasCorrectasSinFallo = 0;
            segundero = 0;

            document.getElementById('escenario-inicio').style.display = 'none';
            document.getElementById('escenario-juego').style.display = 'flex';
            
            cargarRondaCombate();
            arrancarCronometro();
        }

        function producirResultadosFalsos(verdadero) {
            let opcionesSet = new Set([verdadero]);
            while (opcionesSet.size < 4) {
                let variacion = Math.floor(Math.random() * 14) - 7;
                let falsoNum = verdadero + (variacion === 0 ? 3 : variacion);
                if (falsoNum >= 0 && falsoNum <= 200) {
                    opcionesSet.add(falsoNum);
                }
            }
            return Array.from(opcionesSet).sort(() => Math.random() - 0.5);
        }

        function cargarRondaCombate() {
            fallosPorPregunta = 0;
            document.getElementById('consola-feedback').innerText = '';
            
            const robot = document.getElementById('navegante');
            robot.innerText = '🧑‍🚀';
            robot.classList.remove('disparar-animacion');

            ejercicioActual = listaOperaciones[indicePregunta];
            document.getElementById('ronda-numero').innerText = (indicePregunta + 1);
            document.getElementById('visor-operacion').innerText = ejercicioActual.texto;

            const alternativas = producirResultadosFalsos(ejercicioActual.resultado);
            const gridAsteroides = document.getElementById('bloque-asteroides');
            gridAsteroides.innerHTML = '';

            alternativas.forEach(val => {
                const ast = document.createElement('button');
                ast.className = 'asteroide';
                ast.innerText = val;
                ast.onclick = () => verificarImpactoLaser(val, ast);
                gridAsteroides.appendChild(ast);
            });
        }

        function verificarImpactoLaser(numeroElegido, elementoHTML) {
            const robot = document.getElementById('navegante');
            const consola = document.getElementById('consola-feedback');

            // Efecto visual 2D de fuego láser desde la izquierda
            robot.classList.remove('disparar-animacion');
            void robot.offsetWidth; // Truco CSS para resetear la animación instantáneamente
            robot.classList.add('disparar-animacion');

            if (numeroElegido === ejercicioActual.resultado) {
                sonidoEfectoLaser('laser_exito');
                elementoHTML.classList.add('asteroide-destruido');
                robot.innerText = '😎';
                consola.innerText = '¡ASTEROIDE DESTRUIDO! 💥';
                consola.style.color = 'var(--neon-green)';

                if (fallosPorPregunta === 0) respuestasCorrectasSinFallo++;

                // Bloqueo temporal para evitar doble disparo antes de avanzar
                document.querySelectorAll('.asteroide').forEach(a => a.style.pointerEvents = 'none');

                setTimeout(() => {
                    if (indicePregunta < totalRondasMaximas - 1) {
                        indicePregunta++;
                        cargarRondaCombate();
                    } else {
                        finalizarMisionEspacial();
                    }
                }, 1100);
            } else {
                sonidoEfectoLaser('laser_error');
                elementoHTML.classList.add('asteroide-fallido');
                robot.innerText = '😰';
                consola.innerText = '¡Fallo en el cálculo! El escudo resiste. ¡Inténtalo de nuevo! 🛡️';
                consola.style.color = 'var(--neon-red)';
                fallosPorPregunta++;
                
                // Quitar clase roja tras un momento para permitir otro intento cómodo
                setTimeout(() => {
                    elementoHTML.classList.remove('asteroide-fallido');
                }, 600);
            }
        }

        function finalizarMisionEspacial() {
            clearInterval(idReloj);
            document.getElementById('escenario-juego').style.display = 'none';
            document.getElementById('escenario-final').style.display = 'flex';

            const porcentajeFinal = Math.round((respuestasCorrectasSinFallo / totalRondasMaximas) * 100);
            document.getElementById('porcentaje-eficiencia').innerText = porcentajeFinal + '%';

            let textoFinal = `Has completado la defensa del sector en un tiempo de <b>${segundero} segundos</b>. `;
            if (porcentajeFinal === 100) {
                textoFinal += "¡Nivel Almirante Galáctico! Tu precisión matemática en los 30 objetivos ha sido perfecta. La galaxia está a salvo gracias a ti. 🏆👽🛸";
            } else if (porcentajeFinal >= 75) {
                textoFinal += "¡Piloto Excelente! Tienes un cálculo mental ultra rápido y muy seguro. ¡Gran trabajo en el espacio! ⚡✨";
            } else {
                textoFinal += "¡Misión cumplida, recluta! Lograste limpiar los 30 asteroides. Sigue entrenando tus cañones para ser el más rápido del universo. 🚀📚";
            }
            document.getElementById('resumen-combate').innerHTML = textoFinal;
        }

        function regresarAlMenu() {
            clearInterval(idReloj);
            segundero = 0;
            document.getElementById('reloj-digital').innerText = '0s';
            document.getElementById('txt-modo-activo').innerText = "🚀 Misión Espacial";
            
            document.getElementById('escenario-final').style.display = 'none';
            document.getElementById('escenario-juego').style.display = 'none';
            document.getElementById('escenario-inicio').style.display = 'flex';
        }
    </script>
</body>
</html>
