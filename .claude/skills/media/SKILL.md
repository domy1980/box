---
name: media
description: カメラ・モーションキャプチャ・画像取り込み・音声入力の実装リファレンス
allowed-tools: Read, WebFetch, WebSearch
---

# メディア機能 実装リファレンス

このSKILLはbuildエージェントが参照する知識ベースです。
実装時は以下のCDNとコードパターンを活用してください。

---

## 1. カメラ（Webカメラ映像取得）

### 基本実装（getUserMedia）
ライブラリ不要。ブラウザ標準APIで動く。

```html
<video id="video" autoplay playsinline></video>
<button onclick="startCamera()">カメラ起動</button>

<script>
async function startCamera() {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      video: { width: 640, height: 480 },
      audio: false
    });
    document.getElementById('video').srcObject = stream;
  } catch(e) {
    alert('カメラのアクセスを許可してください: ' + e.message);
  }
}
</script>
```

### 写真撮影（canvas使用）
```javascript
function takePhoto() {
  const video = document.getElementById('video');
  const canvas = document.createElement('canvas');
  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;
  canvas.getContext('2d').drawImage(video, 0, 0);
  const dataUrl = canvas.toDataURL('image/jpeg');
  // dataUrlをimgタグのsrcに設定 or localStorageに保存
  return dataUrl;
}
```

---

## 2. モーションキャプチャ・ポーズ検出

### MediaPipe（最強・Google製）
手・顔・全身ポーズをリアルタイム検出。CDNで使用可。

```html
<!-- 手のランドマーク検出 -->
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision/vision_bundle.mjs" 
        type="module"></script>

<script type="module">
import { HandLandmarker, FilesetResolver } from 
  "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision/vision_bundle.mjs";

const vision = await FilesetResolver.forVisionTasks(
  "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@latest/wasm"
);
const handLandmarker = await HandLandmarker.createFromOptions(vision, {
  baseOptions: {
    modelAssetPath: "https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/1/hand_landmarker.task"
  },
  numHands: 2,
  runningMode: "VIDEO"
});

// 動画フレームごとに検出
function detect() {
  const results = handLandmarker.detectForVideo(videoElement, Date.now());
  // results.landmarks に21個の手の関節座標が入る
  requestAnimationFrame(detect);
}
</script>
```

### MediaPipe対応機能一覧
| 機能 | 検出ポイント数 | 用途 |
|---|---|---|
| Hand Landmarker | 21点/手 | ジェスチャー操作 |
| Face Landmarker | 468点 | 表情・顔追跡 |
| Pose Landmarker | 33点 | 全身モーション |
| Gesture Recognizer | - | 👍✌️などジェスチャー認識 |

GitHub: https://github.com/google-ai-edge/mediapipe

---

## 3. 画像取り込み（ファイル選択）

```html
<input type="file" id="fileInput" accept="image/*" onchange="loadImage(event)">
<canvas id="canvas"></canvas>

<script>
function loadImage(event) {
  const file = event.target.files[0];
  const reader = new FileReader();
  reader.onload = (e) => {
    const img = new Image();
    img.onload = () => {
      const canvas = document.getElementById('canvas');
      canvas.width = img.width;
      canvas.height = img.height;
      canvas.getContext('2d').drawImage(img, 0, 0);
    };
    img.src = e.target.result;
  };
  reader.readAsDataURL(file);
}
</script>
```

### ドラッグ＆ドロップでの画像取り込み
```javascript
document.addEventListener('dragover', e => e.preventDefault());
document.addEventListener('drop', e => {
  e.preventDefault();
  const file = e.dataTransfer.files[0];
  if (file && file.type.startsWith('image/')) {
    const reader = new FileReader();
    reader.onload = (ev) => { /* 画像処理 */ };
    reader.readAsDataURL(file);
  }
});
```

---

## 4. 音声入力（音声認識）

### Web Speech API（ライブラリ不要・Chrome推奨）
```javascript
const SpeechRecognition = 
  window.SpeechRecognition || window.webkitSpeechRecognition;

if (!SpeechRecognition) {
  alert('このブラウザは音声認識に対応していません。Chromeを使ってください。');
} else {
  const recognition = new SpeechRecognition();
  recognition.lang = 'ja-JP';        // 日本語
  recognition.continuous = true;     // 継続認識
  recognition.interimResults = true; // 途中結果も表示

  recognition.onresult = (event) => {
    const transcript = Array.from(event.results)
      .map(r => r[0].transcript)
      .join('');
    document.getElementById('output').textContent = transcript;
  };

  recognition.onerror = (e) => console.error('音声認識エラー:', e.error);

  document.getElementById('micBtn').onclick = () => recognition.start();
  document.getElementById('stopBtn').onclick = () => recognition.stop();
}
```

### 注意点
- Chromeのみ完全対応（Edge部分対応）
- ファイルを直接開く場合（file://）は動作しない場合あり
- その場合 `python3 -m http.server 8080` でローカルサーバーを使う

---

## 5. 音声出力（テキスト読み上げ）

```javascript
function speak(text) {
  const utterance = new SpeechSynthesisUtterance(text);
  utterance.lang = 'ja-JP';
  utterance.rate = 1.0;
  utterance.pitch = 1.0;
  window.speechSynthesis.speak(utterance);
}
```

---

## 実装のコツ

### カメラ+MediaPipeの組み合わせ（最強セット）
```
getUserMedia → video要素 → requestAnimationFrame →
MediaPipe.detectForVideo → canvas描画
```

### 音声入力+テキスト処理の組み合わせ
```
SpeechRecognition → テキスト取得 → 処理・表示
```

### ファイル：サーバーが必要な場合
音声認識がfile://で動かない場合：
```bash
cd ~/Desktop/WebApp/current
python3 -m http.server 8080
# → http://localhost:8080 をブラウザで開く
```
