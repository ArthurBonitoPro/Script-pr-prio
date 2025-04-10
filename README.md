<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Informações do Dispositivo</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #1a1a1a;
      color: #fff;
      padding: 20px;
    }

    h1 {
      text-align: center;
      font-size: 2em;
      margin-bottom: 30px;
    }

    .info {
      background: #2c2c2c;
      margin: 15px 0;
      padding: 15px;
      border-radius: 10px;
      box-shadow: 0 0 10px #000;
    }

    video {
      width: 100%;
      max-width: 300px;
      border-radius: 10px;
      margin-top: 10px;
    }

    .aviso {
      margin-top: 30px;
      text-align: center;
      font-size: 0.9em;
      color: #ccc;
    }
  </style>
</head>
<body>

  <h1>Informações do Celular</h1>

  <div class="info" id="bateria">
    <strong>Bateria:</strong> <span id="nivelBateria">Carregando...</span><br>
    <strong>Status:</strong> <span id="statusBateria">...</span>
  </div>

  <div class="info">
    <strong>IP Público:</strong> <span id="ip">Carregando...</span>
  </div>

  <div class="info">
    <strong>Dispositivo:</strong> <span id="dispositivo"></span>
  </div>

  <div class="info" id="localizacao">
    <strong>Localização:</strong> <span id="estado">Detectando...</span>
  </div>

  <div class="info">
    <strong>Câmera:</strong>
    <br>
    <video id="camera" autoplay muted playsinline></video>
  </div>

  <div class="aviso">(Aviso: Nenhum dado é compartilhado!)</div>

  <script>
    // Bateria
    navigator.getBattery().then(battery => {
      function atualizarBateria() {
        document.getElementById('nivelBateria').textContent = Math.round(battery.level * 100) + '%';
        document.getElementById('statusBateria').textContent = battery.charging ? 'Carregando' : 'Não está carregando';
      }
      atualizarBateria();
      battery.addEventListener('levelchange', atualizarBateria);
      battery.addEventListener('chargingchange', atualizarBateria);
    });

    // IP
    fetch('https://api.ipify.org?format=json')
      .then(res => res.json())
      .then(data => {
        document.getElementById('ip').textContent = data.ip;
      }).catch(() => {
        document.getElementById('ip').textContent = 'Erro ao obter IP';
      });

    // Dispositivo
    const agente = navigator.userAgent;
    document.getElementById('dispositivo').textContent = agente;

    // Localização
    if ('geolocation' in navigator) {
      navigator.geolocation.getCurrentPosition(pos => {
        const lat = pos.coords.latitude;
        const lon = pos.coords.longitude;

        fetch(`https://geocode.maps.co/reverse?lat=${lat}&lon=${lon}`)
          .then(res => res.json())
          .then(data => {
            const estado = data.address.state || 'Localização desconhecida';
            document.getElementById('estado').textContent = estado;
          }).catch(() => {
            document.getElementById('estado').textContent = 'Erro ao localizar';
          });
      }, () => {
        document.getElementById('estado').textContent = 'Permissão negada';
      });
    } else {
      document.getElementById('estado').textContent = 'Geolocalização não suportada';
    }

    // Câmera
    async function iniciarCamera() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
        const video = document.getElementById('camera');
        video.srcObject = stream;
      } catch (e) {
        document.getElementById('camera').outerHTML = '<p>Permissão da câmera negada.</p>';
      }
    }

    iniciarCamera();
  </script>
</body>
</html>