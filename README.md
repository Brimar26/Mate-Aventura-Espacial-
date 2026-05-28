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
            padding: 10px; /* Reducido para mejor margen en celulares */
            overflow-x: hidden;
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
            padding: 12px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid rgba(56, 189, 248, 0.2);
            color: var(--text-light);
            font-size: clamp(14px, 4vw, 18px);
            font-weight: bold;
        }

        /* Pantallas de Control de Flujo */
        .pantalla {
            padding: 25px 15px;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        #escenario-juego { display: none; }
        #escenario-final { display: none; }

        .titulo-neon {
            font-size: clamp(24px, 7vw, 38px);
            color: var(--text-light);
            text-shadow: 0 0 10px var(--neon-blue), 0 0 20px var(--neon-blue);
            margin-bottom: 5px;
        }

        .texto-descripcion {
            color: #94A3B8;
            font-size: clamp(14px, 3.5vw, 16px);
            max-width: 500px;
            line-height: 1.5;
        }

        /* Selector de Tipo de Operación Adaptable */
        .cuadricula-operaciones {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
            gap: 12px;
            width: 100%;
            max-width: 500px;
        }

        .btn-modo {
            background: rgba(255, 255, 255, 0.03);
            color: var(--text-light);
            border: 2px solid rgba(255, 255, 255, 0.2);
            padding: 15px 10px;
            border-radius: 16px;
            font-size: clamp(16px, 4.5vw, 20px);
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

        .btn-modo span { font-size: clamp(24px, 6vw, 32px); }

        /* Área de Combate 2D Adaptable */
        .cabina-operacion {
            background: linear-gradient(180deg, rgba(30, 41, 59, 0.8) 0%, rgba(15, 23, 42, 0.9) 100%);
            border: 3px solid rgba(255, 255, 255, 0.1);
            border-radius: 24px;
            width: 100%;
            max-width: 480px;
            padding: 15px;
            font-size: clamp(34px, 10vw, 52px);
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
            margin: 15px 0;
            position: relative;
            gap: 15px;
        }

        /* Avatar del Navegante Cósmico */
        .avatar-astronauta {
            font-size: clamp(45px, 12vw, 70px);
            width: clamp(75px, 18vw, 110px);
            height: clamp(75px, 18vw, 110px);
            background: rgba(255,255,255,0.05);
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            border: 3px dashed var(--neon-blue);
            animation: flotar 3s ease-in-out infinite;
            flex-shrink: 0;
        }

        @keyframes flotar {
            0%, 100% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-8px) rotate(3deg); }
        }

        .disparar-animacion { animation: laserFuego 0.3s ease-out; }
        @keyframes laserFuego {
            0% { transform: scale(1); filter: brightness(1.5); }
            50% { transform: scale(0.9) translateX(10px); }
            100% { transform: scale(1); filter: brightness(1); }
        }

        /* Cuadrícula de Asteroides de Respuesta Cósmica */
        .zona-asteroides {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
            width: 100%;
            max-width: 400px;
        }

        .asteroide {
            background: linear-gradient(135deg, #334155 0%, #1E293B 100%);
            border: 3px solid #475569;
            color: var(--text-light);
            font-size: clamp(20px, 5.5vw, 28px);
            font-weight: bold;
            padding: clamp(12px, 3.5vw, 20px);
            border-radius: 50% 40% 50% 45%; 
            cursor: pointer;
            box-shadow: 0 4px 0 #0F172A, 0 6px 12px rgba(0,0,0,0.4);
            transition: all 0.1s ease;
            text-align: center;
            width: 100%;
        }

        .asteroide:hover {
            transform: scale(1.03);
            border-color: var(--neon-blue);
            box-shadow: 0 4px 0 #0F172A, 0 0 12px rgba(56, 189, 248, 0.4);
        }

        .asteroide:active {
            transform: translateY(3px);
            box-shadow: 0 1px 0 #0F172A;
        }

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
            pointer-events: none;
        }

        .btn-unificado {
            background: var(--neon-green);
            color: #0F172A;
            font-size: clamp(18px, 5vw, 22px);
            font-weight: bold;
            padding: 12px 35px;
            border: none;
            border-radius: 14px;
            cursor: pointer;
            box-shadow: 0 5px 0 #15803D;
            transition: all 0.1s;
        }
        .btn-unificado:active { transform: translateY(4px); box-shadow: none; }

        .marcador-puntuacion-final {
            font-size: clamp(48px, 15vw, 72px);
            color: var(--neon-green);
            font-weight: 900;
            text-shadow: 0 0 15px rgba(74,222,128,0.3);
            margin: 5px 0;
        }

        #consola-feedback {
            min-height: 25px; 
            font-weight: bold; 
            font-size: clamp(14px, 4vw, 18px);
            padding: 0 10px;
        }

        /* ========================================== */
        /* RESPONSIVIDAD OPTIMIZADA PARA CELULARES */
        /* ========================================== */
        @media (max-width: 600px) {
            .arena-espacial {
                flex-direction: column; /* Cambia de horizontal a vertical en móviles */
                gap: 20px;
            }
            .avatar-astronauta {
                animation: flotarMovil 3s ease-in-out infinite;
            }
            .zona-asteroides {
                max-width: 100%; /* Permite que use todo el ancho cómodo en celular */
            }
        }

        @keyframes flotarMovil {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-6px); }
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
            <h3 class="titulo-neon">Academia Galáctica 👾</h3>
            <div class="cuadricula-operaciones">
                <button class="btn-modo" data-tipo="suma" onclick="comenzarMision('suma')">
                    <span>➕</span> Sumas 
                </button>
                <button class="btn-modo" data-tipo="resta" onclick="comenzarMision('resta')">
                    <span>➖</span> Restas 
                </button>
                <button class="btn-modo" data-tipo="multi" onclick="comenzarMision('multi')">
                    <span>✖️</span> Multiplicar
                </button>
                <button class="btn-modo" data-tipo="divi" onclick="comenzarMision('divi')">
                    <span>➗</span> Divisiones 
                </button>
            </div>
        </div>

        <div id="escenario-juego" class="pantalla">
            <div style="color: #94A3B8; font-weight: bold; font-size: 14px;">
                Misión Progresiva: <span id="ronda-numero">1</span> / 30
            </div>

            <div class="cabina-operacion" id="visor-operacion">
                0 + 0
            </div>

            <div class="arena-espacial">
                <div class="avatar-astronauta" id="navegante">🧑‍🚀</div>
                <div class="zona-asteroides" id="bloque-asteroides"></div>
            </div>

            <div id="consola-feedback"></div>
        </div>

        <div id="escenario-final" class="pantalla">
            <h2 class="titulo-neon">¡Sistema Defendido! 🌌</h2>
            <div class="marcador-puntuacion-final" id="porcentaje-eficiencia">0%</div>
            <p class="texto-descripcion" id="resumen-combate"></p>
            <button class="btn-unificado" onclick="regresarAlMenu()">Volver a la Base 🔄</button>
        </div>
    </div>

    <script>
        let modoJuego = ''; 
        let listaOperaciones = [];
        let indicePregunta = 0;
        const totalRondasMaximas = 30;

        let respuestasCorrectasSinFallo = 0;
        let fallosPorPregunta = 0;
        let segundero = 0;
        let idReloj = null;
        let ejercicioActual = null;

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
                osc.frequency.setValueAtTime(587.33, ctxAudio.currentTime);
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

        function generarMisionesGalacticas(tipo) {
            let misiones = [];
            for (let k = 0; k < 60; k++) {
                let num1 = 0, num2 = 0, respuesta = 0, textoOp = '';

                switch(tipo) {
                    case 'suma':
                        num1 = Math.floor(Math.random() * 80) + 15;
                        num2 = Math.floor(Math.random() * 80) + 10;
                        respuesta = num1 + num2;
                        textoOp = `${num1} + ${num2}`;
                        break;
                    case 'resta':
                        num1 = Math.floor(Math.random() * 90) + 30;
                        num2 = Math.floor(Math.random() * (num1 - 5)) + 5;
                        respuesta = num1 - num2;
                        textoOp = `${num1} − ${num2}`;
                        break;
                    case 'multi':
                        num1 = Math.floor(Math.random() * 11) + 2;  
                        num2 = Math.floor(Math.random() * 10) + 1;   
                        respuesta = num1 * num2;
                        textoOp = `${num1} × ${num2}`;
                        break;
                    case 'divi':
                        num2 = Math.floor(Math.random() * 8) + 2;   
                        respuesta = Math.floor(Math.random() * 10) + 1; 
                        num1 = num2 * respuesta; 
                        textoOp = `${num1} ÷ ${num2}`;
                        break;
                }
                misiones.push({ texto: textoOp, resultado: respuesta });
            }
            return misiones.sort(() => Math.random() - 0.5).slice(0, totalRondasMaximas);
        }

        function arrancarCronometro() {
            clearInterval(idReloj);
            idReloj = setInterval(() => {
                segundero++;
                document.getElementById('reloj-digital').innerText = segundero + 's';
            }, 1000);
        }

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

            robot.classList.remove('disparar-animacion');
            void robot.offsetWidth; 
            robot.classList.add('disparar-animacion');

            if (numeroElegido === ejercicioActual.resultado) {
                sonidoEfectoLaser('laser_exito');
                elementoHTML.classList.add('asteroide-destruido');
                robot.innerText = '😎';
                consola.innerText = '¡ASTEROIDE DESTRUIDO! 💥';
                consola.style.color = 'var(--neon-green)';

                if (fallosPorPregunta === 0) respuestasCorrectasSinFallo++;

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
                consola.innerText = '¡Fallo en el cálculo! Inténtalo de nuevo. 🛡️';
                consola.style.color = 'var(--neon-red)';
                fallosPorPregunta++;
                
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

            let textoFinal = `Has completado la defensa del sector en <b>${segundero} segundos</b>. `;
            if (porcentajeFinal === 100) {
                textoFinal += "¡Nivel Almirante Galáctico! Tu precisión matemática ha sido perfecta. 🏆👽🛸";
            } else if (porcentajeFinal >= 75) {
                textoFinal += "¡Piloto Excelente! Tienes un cálculo mental ultra rápido. ⚡✨";
            } else {
                textoFinal += "¡Misión cumplida, recluta! Sigue entrenando para ser el más rápido del universo. 🚀📚";
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
