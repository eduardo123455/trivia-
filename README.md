# trivia- el que tenga menos puntos gana
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Juego de Arquitectura</title>
  <link href="https://fonts.googleapis.com/css2?family=Fredoka+One&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Fredoka One', cursive;
      margin: 0;
      padding: 0;
      background-color: #f2f2f2;
      text-align: center;
      overflow: hidden;
    }
    .game-container {
      max-width: 800px;
      margin: 30px auto;
      background: white;
      padding: 30px;
      border-radius: 20px;
      box-shadow: 0 0 20px rgba(0,0,0,0.1);
    }
    .level {
      display: none;
    }
    .visible {
      display: block;
    }
    .question {
      font-size: 24px;
      margin-bottom: 20px;
    }
    .answers button {
      display: block;
      margin: 10px auto;
      padding: 15px;
      font-size: 18px;
      border-radius: 10px;
      border: none;
      cursor: pointer;
      width: 60%;
      background-color: #f06;
      color: white;
    }
    .answers button:hover {
      background-color: #c04;
    }
    .status {
      margin-top: 20px;
      font-size: 20px;
    }
    .image-question {
      width: 100%;
      max-height: 300px;
      object-fit: contain;
      margin-bottom: 20px;
      border-radius: 15px;
    }
    .final {
      font-size: 28px;
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <div class="game-container">
    <h1>🏛️ Aventura Arquitectónica 🧱</h1>
    <p>¡Responde correctamente para avanzar! Si fallas, retrocedes. ¿Listo para convertirte en un maestro de la historia de la arquitectura?</p>
    <div id="game"></div>
    <div class="status" id="status"></div>
  </div>
  
  <audio id="bg-music" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_f7b2b56a40.mp3?filename=epic-cinematic-trailer-112054.mp3" autoplay loop></audio>
  <audio id="correct-sound" src="https://cdn.pixabay.com/download/audio/2021/09/02/audio_d25fae46c2.mp3?filename=correct-answer-2-36058.mp3"></audio>
  <audio id="wrong-sound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_d8bcecbfe8.mp3?filename=wrong-47985.mp3"></audio>

  <script>
    const preguntas = [
      {
        pregunta: "¿En qué siglo se desarrolló principalmente el estilo Barroco?",
        opciones: ["Siglo XV", "Siglo XVII", "Siglo XIX", "Siglo XXI"],
        respuesta: 1,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/5/5d/St._Nicholas_Church%2C_Interior_1%2C_Prague.jpg"
      },
      {
        pregunta: "¿Qué característica es típica del Barroco?",
        opciones: ["Líneas rectas y sobrias", "Ornamentación excesiva y teatralidad", "Uso del hormigón expuesto", "Funcionalidad y minimalismo"],
        respuesta: 1,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/2/23/Church_of_the_Ges%C3%B9_-_Rome%2C_Italy_-_interior.jpg"
      },
      {
        pregunta: "¿Cuál de estos arquitectos es un exponente del Neoclásico?",
        opciones: ["Le Corbusier", "Gian Lorenzo Bernini", "Étienne-Louis Boullée", "Antoni Gaudí"],
        respuesta: 2,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/f/ff/Etienne-Louis_Boullee_Cenotaph_for_Newton_1784.jpg"
      },
      {
        pregunta: "¿Qué estilo buscaba volver a los valores de la Antigua Grecia y Roma?",
        opciones: ["Modernismo", "Barroco", "Neoclásico", "Brutalismo"],
        respuesta: 2,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/f/f0/Pantheon2006.jpg"
      },
      {
        pregunta: "¿Qué elemento es común en la arquitectura neoclásica?",
        opciones: ["Vidrio y acero", "Columnas, frontones y simetría", "Decoración orgánica", "Hormigón sin recubrimiento"],
        respuesta: 1,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/e/e7/Madeleinechurch.jpg"
      },
      {
        pregunta: "¿El Modernismo se caracteriza por:",
        opciones: ["La eliminación de toda decoración", "Ornamentación basada en formas naturales", "Uso exclusivo del concreto", "Estructuras de acero puro"],
        respuesta: 1,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/5/5c/Casa_Batll%C3%B3.jpg"
      },
      {
        pregunta: "¿Cuál de estos edificios fue diseñado por Antoni Gaudí?",
        opciones: ["Villa Savoye", "Sagrada Familia", "Palacio de Versalles", "Biblioteca Nacional de Francia"],
        respuesta: 1,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/1/11/Sagrada_Familia_01.jpg"
      },
      {
        pregunta: "¿Qué país fue cuna del Modernismo arquitectónico?",
        opciones: ["Alemania", "España", "Inglaterra", "Japón"],
        respuesta: 1,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/9/9b/Parc_G%C3%BCell_07.jpg"
      },
      {
        pregunta: "¿El Brutalismo se desarrolló sobre todo en:",
        opciones: ["El Renacimiento", "La Edad Media", "El siglo XX", "El siglo XVIII"],
        respuesta: 2,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/2/2f/BostonCityHall.jpeg"
      },
      {
        pregunta: "¿Qué material se asocia con el Brutalismo?",
        opciones: ["Mármol", "Hormigón crudo", "Piedra volcánica", "Vidrio"],
        respuesta: 1,
        imagen: "https://upload.wikimedia.org/wikipedia/commons/6/67/Robarts_Library.jpg"
      }
    ];

    let nivel = 0;
    let puntaje = 0;
    const gameDiv = document.getElementById('game');
    const statusDiv = document.getElementById('status');

    function cargarNivel() {
      gameDiv.innerHTML = '';
      const q = preguntas[nivel];
      const img = document.createElement('img');
      img.src = q.imagen;
      img.className = 'image-question';
      gameDiv.appendChild(img);

      const pregunta = document.createElement('div');
      pregunta.className = 'question';
      pregunta.textContent = q.pregunta;
      gameDiv.appendChild(pregunta);

      const contenedorOpciones = document.createElement('div');
      contenedorOpciones.className = 'answers';

      q.opciones.forEach((op, index) => {
        const btn = document.createElement('button');
        btn.textContent = op;
        btn.onclick = () => evaluar(index);
        contenedorOpciones.appendChild(btn);
      });

      gameDiv.appendChild(contenedorOpciones);
    }

    function evaluar(respuestaSeleccionada) {
      const sonido = document.getElementById(
        respuestaSeleccionada === preguntas[nivel].respuesta
          ? 'correct-sound'
          : 'wrong-sound'
      );
      sonido.play();
      
      if (respuestaSeleccionada === preguntas[nivel].respuesta) {
        puntaje++;
        nivel++;
        statusDiv.textContent = "¡Correcto! Avanzas al siguiente nivel.";
      } else {
        nivel = Math.max(0, nivel - 1);
        statusDiv.textContent = "Incorrecto. Retrocedes un nivel.";
      }

      if (nivel >= preguntas.length) {
        mostrarFinal();
      } else {
        setTimeout(cargarNivel, 1500);
      }
    }

    function mostrarFinal() {
      gameDiv.innerHTML = `
        <div class="final">🎉 ¡Has completado el juego con ${puntaje} puntos!</div>
      `;
    }

    cargarNivel();
  </script>
</body>
</html>
