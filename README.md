<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>TECLADO Entry Scanner</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <script src="https://unpkg.com/html5-qrcode@2.3.8/html5-qrcode.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap" rel="stylesheet">
  <style>
    /* SAME STYLING AS BEFORE - JUST ADDING PERMISSION PROMPTS */
    body { font-family: 'Orbitron', sans-serif; background: linear-gradient(135deg, #0f0c29, #302b63, #24243e); color: #fff; padding: 10px; }
    .container { max-width: 100%; margin: 0 auto; text-align: center; background: rgba(10,10,20,0.95); border-radius: 20px; padding: 20px; border: 2px solid #ff4444; }
    /* ... rest of your existing styles ... */
    
    /* PERMISSION HELPERS */
    .permission-guide { 
      background: linear-gradient(90deg, #ff6b6b, #ffd93d); 
      border-radius: 15px; padding: 20px; margin: 15px 0; 
      font-size: 16px; text-align: left; 
    }
    .steps { display: flex; flex-direction: column; gap: 10px; }
    .step { display: flex; align-items: flex-start; gap: 10px; }
    .step-number { background: rgba(0,0,0,0.7); color: #fff; width: 30px; height: 30px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-weight: bold; flex-shrink: 0; }
  </style>
</head>
<body>
  <div class="container">
    <h1>🎟 ENTRY SCANNER</h1>
    <p>TECLADO 2026 • CCSIT PALAKKAD</p>
    
    <!-- PERMISSION TROUBLESHOOTER -->
    <div id="permissionGuide" class="permission-guide">
      <h3>📱 CAMERA NOT WORKING?</h3>
      <div class="steps">
        <div class="step">
          <div class="step-number">1</div>
          <div><strong>Tap site info</strong> (🔒 next to URL) → Camera → <strong>Allow</strong></div>
        </div>
        <div class="step">
          <div class="step-number">2</div>
          <div><strong>Phone Settings</strong> → Apps → Chrome → Permissions → <strong>Camera ON</strong></div>
        </div>
        <div class="step">
          <div class="step-number">3</div>
          <div><strong>Try incognito</strong> or restart browser</div>
        </div>
      </div>
      <button onclick="hidePermissionGuide()" style="margin-top: 15px; padding: 10px 20px; background: #00ff99; color: #000; border: none; border-radius: 10px; font-weight: bold;">✅ I FIXED IT</button>
    </div>

    <!-- YOUR SCANNER -->
    <div id="qr-reader" style="width: 100%; max-width: 500px; margin: 20px auto;"></div>
    
    <div style="display: flex; gap: 15px; justify-content: center; flex-wrap: wrap;">
      <button id="startScan" style="padding: 18px; background: linear-gradient(45deg, #00ff99, #00cc88); color: #000; border: none; border-radius: 15px; font-weight: bold; font-size: 18px; cursor: pointer;">🚀 START CAMERA</button>
      <button id="stopScan" class="hidden" style="padding: 18px; background: linear-gradient(45deg, #ff4444, #cc3333); color: #fff; border: none; border-radius: 15px; font-weight: bold; font-size: 18px; cursor: pointer;">⏹ STOP</button>
      <button id="fileScan" style="padding: 18px; background: linear-gradient(45deg, #ffd700, #ffaa00); color: #000; border: none; border-radius: 15px; font-weight: bold; font-size: 18px; cursor: pointer;">📸 PHOTO SCAN</button>
    </div>
    
    <!-- REST OF YOUR SCANNER HTML -->
    <div id="result" style="margin: 25px 0; padding: 25px; border-radius: 20px; font-size: 20px; min-height: 80px; background: rgba(255,255,255,0.1);">👆 Tap START CAMERA (allow permission!)</div>
  </div>

  <script>
    const html5QrCode = new Html5Qrcode("qr-reader");
    let isScanning = false;
    
    // BETTER PERMISSION HANDLING
    async function startScanning() {
      try {
        // FIRST check HTTPS/localhost (required for camera)
        if (location.protocol !== 'https:' && location.hostname !== 'localhost' && location.hostname !== '127.0.0.1') {
          alert('⚠️ Camera needs HTTPS or localhost. Use GitHub Pages/Netlify!');
          return;
        }
        
        result.innerHTML = '🔄 Requesting camera permission...';
        
        const config = { 
          fps: 10, 
          qrbox: { width: 250, height: 250 },
          videoConstraints: {
            facingMode: 'environment', // BACK CAMERA
            width: { ideal: 1280 },
            height: { ideal: 720 }
          }
        };
        
        await html5QrCode.start(
          config.videoConstraints,
          config,
          (decodedText) => {
            // YOUR SCAN LOGIC HERE (same as before)
            console.log('✅ SCANNED:', decodedText);
            result.innerHTML = `✅ QR SCANNED: ${decodedText.substring(0, 50)}...`;
            html5QrCode.stop().then(() => {
              setTimeout(startScanning, 2000); // Auto restart
            });
          },
          () => {} // Silent fail
        );
        
        isScanning = true;
        document.getElementById('startScan').classList.add('hidden');
        document.getElementById('stopScan').classList.remove('hidden');
        
      } catch (err) {
        console.error('Camera error:', err);
        result.innerHTML = `❌ Camera denied<br><small>🔒 Check permission guide above</small>`;
        result.style.background = 'rgba(255,68,68,0.3)';
        result.style.border = '3px solid #ff4444';
      }
    }
    
    function hidePermissionGuide() {
      document.getElementById('permissionGuide').style.display = 'none';
    }
    
    // EVENT LISTENERS
    document.getElementById('startScan').onclick = startScanning;
  </script>
</body>
</html>
