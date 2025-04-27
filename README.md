<!DOCTYPE html>
<html lang="es" class="scroll-smooth">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QR Enterprise Pro</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --primary: #2563eb;
      --secondary: #1e40af;
      --accent: #3b82f6;
    }
    
    body {
      font-family: 'Inter', sans-serif;
      background: #f8fafc;
    }

    .luxury-shadow {
      box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
    }

    .btn-primary {
      background: linear-gradient(135deg, var(--primary) 0%, var(--secondary) 100%);
      color: white;
      padding: 1rem 2rem;
      border-radius: 12px;
      font-weight: 600;
      transition: all 0.3s ease;
      border: none;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: 0.5rem;
    }

    .btn-primary:hover {
      transform: translateY(-1px);
      box-shadow: 0 4px 12px rgba(37, 99, 235, 0.3);
    }

    .qr-card {
      transition: all 0.3s ease;
      border: 2px solid transparent;
      position: relative;
    }

    .qr-card:hover {
      border-color: var(--primary);
      transform: translateY(-3px);
    }

    .delete-btn {
      position: absolute;
      top: 0.5rem;
      right: 0.5rem;
      background: #ef4444;
      width: 2rem;
      height: 2rem;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      cursor: pointer;
      transition: all 0.3s ease;
      border: none;
    }

    .delete-btn:hover {
      background: #dc2626;
      transform: scale(1.1);
    }
  </style>
</head>
<body class="min-h-screen flex flex-col">
  <header class="bg-white fixed w-full top-0 z-50 luxury-shadow">
    <div class="container mx-auto px-6 py-4 flex items-center justify-between">
      <div class="flex items-center gap-2">
        <i class="fas fa-qrcode text-3xl text-blue-600"></i>
        <h1 class="text-2xl font-bold text-gray-800">
          
        </h1>
      </div>
      <nav class="flex gap-4 items-center">
        <button onclick="showSection('generator')" class="btn-primary">
          <i class="fas fa-plus"></i>
          Nuevo QR
        </button>
        <button onclick="showSection('history')" class="btn-primary">
          <i class="fas fa-history"></i>
          Historial
        </button>
      </nav>
    </div>
  </header>

  <main class="container mx-auto mt-24 px-6 flex-grow">
    <!-- Sección Generador -->
    <section id="generator" class="bg-white rounded-2xl p-8 mb-16 luxury-shadow">
      <div class="max-w-3xl mx-auto">
        <h2 class="text-3xl font-bold mb-8 text-gray-800">Generador Profesional</h2>
        <div class="space-y-6">
          <div>
            <label class="block text-lg font-medium mb-2 text-gray-700">Contenido</label>
            <textarea 
              id="qrContent"
              class="w-full px-4 py-3 rounded-xl border-2 border-gray-200 focus:border-blue-500 focus:ring-2 focus:ring-blue-200 resize-none"
              rows="5"
              placeholder="Ingrese texto, URL, datos de contacto..."
            ></textarea>
          </div>
          
          <div class="grid grid-cols-2 lg:grid-cols-4 gap-4">
            <button onclick="insertTemplate('wifi')" class="btn-primary">
              <i class="fas fa-wifi"></i>
              WiFi
            </button>
            <button onclick="insertTemplate('contact')" class="btn-primary">
              <i class="fas fa-user"></i>
              Contacto
            </button>
            <button onclick="insertTemplate('location')" class="btn-primary">
              <i class="fas fa-map-marker"></i>
              Ubicación
            </button>
            <button onclick="insertTemplate('custom')" class="btn-primary">
              <i class="fas fa-cog"></i>
              Personalizar
            </button>
          </div>

          <div class="flex justify-end gap-4">
            <button onclick="generateQR()" class="btn-primary px-8 py-3 text-lg">
              Generar QR
              <i class="fas fa-arrow-right"></i>
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- Sección Historial -->
    <section id="history" class="hidden bg-white rounded-2xl p-8 mb-16 luxury-shadow">
      <h2 class="text-3xl font-bold mb-8 text-gray-800">Historial de QR</h2>
      <div id="qrHistory" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        <!-- QR generados aparecen aquí -->
      </div>
    </section>
  </main>

  <script>
    let qrHistory = JSON.parse(localStorage.getItem('qrHistory')) || [];

    function showSection(sectionId) {
      document.querySelectorAll('section').forEach(section => {
        section.classList.add('hidden');
      });
      document.getElementById(sectionId).classList.remove('hidden');
    }

    function insertTemplate(type) {
      const content = {
        wifi: 'WIFI:S:MyNetwork;T:WPA;P:Password;;',
        contact: 'BEGIN:VCARD\nVERSION:3.0\nFN:John Doe\nTEL:+123456789\nEND:VCARD',
        location: 'geo:40.7128,-74.0060',
        custom: 'Personaliza tu contenido aquí'
      };
      document.getElementById('qrContent').value = content[type];
    }

    function generateQR() {
      const content = document.getElementById('qrContent').value.trim();
      if (!content) {
        alert('Por favor ingrese contenido válido');
        return;
      }

      const qrData = {
        id: Date.now(),
        content: content,
        date: new Date().toLocaleString(),
        url: `https://api.qrserver.com/v1/create-qr-code/?size=300x300&data=${encodeURIComponent(content)}`
      };

      qrHistory.unshift(qrData);
      localStorage.setItem('qrHistory', JSON.stringify(qrHistory));
      renderQR(qrData);
      showSection('history');
    }

    function renderQR(qr) {
      const qrCard = document.createElement('div');
      qrCard.className = 'qr-card bg-white rounded-xl p-6 luxury-shadow relative';
      qrCard.innerHTML = `
        <button onclick="deleteQR(${qr.id})" class="delete-btn">
          <i class="fas fa-times"></i>
        </button>
        <div class="flex flex-col items-center gap-4">
          <img src="${qr.url}" alt="QR Code" class="w-full h-64 object-contain">
          <p class="text-gray-600 text-sm truncate w-full">${qr.content}</p>
          <div class="flex gap-2 w-full">
            <button onclick="downloadQR('${qr.url}')" class="btn-primary w-full">
              <i class="fas fa-download"></i>
              Descargar
            </button>
            <button onclick="shareQR('${qr.url}')" class="btn-primary w-full">
              <i class="fas fa-share"></i>
              Compartir
            </button>
          </div>
          <small class="text-gray-400">${qr.date}</small>
        </div>
      `;
      document.getElementById('qrHistory').prepend(qrCard);
    }

    function deleteQR(id) {
      if (confirm('¿Eliminar este código QR permanentemente?')) {
        qrHistory = qrHistory.filter(item => item.id !== id);
        localStorage.setItem('qrHistory', JSON.stringify(qrHistory));
        document.getElementById('qrHistory').innerHTML = '';
        qrHistory.forEach(renderQR);
      }
    }

    function downloadQR(url) {
      const link = document.createElement('a');
      link.href = url;
      link.download = `qr-${Date.now()}.png`;
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }

    async function shareQR(url) {
      try {
        await navigator.share({
          title: 'Mi código QR profesional',
          url: url
        });
      } catch (err) {
        prompt('Copiar enlace:', url);
      }
    }

    window.onload = () => {
      qrHistory.forEach(renderQR);
      showSection('generator');
    };
  </script>
</body>
</html>
