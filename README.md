
<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>FEELGOOD AI Voice Studio</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#11111b;color:#fff;font-family:Arial,sans-serif;padding:20px}
main{max-width:600px;margin:20px auto}
h1{color:#c4b5fd;font-size:27px}
p{color:#cbd5e1;line-height:1.5}
textarea,select,button{width:100%;padding:14px;margin:8px 0;border-radius:12px;font-size:16px}
textarea,select{background:#242436;color:white;border:1px solid #494960}
textarea{height:190px;resize:vertical}
button{border:0;background:#8b5cf6;color:white;font-weight:bold}
button.secondary{background:#343448}
#status{font-size:14px;color:#c4b5fd}
</style>
</head>
<body>
<main>
<h1>🎙️ FEELGOOD AI VOICE STUDIO</h1>
<p>अपनी हिंदी कहानी को आवाज़ में सुनें।</p>

<label for="script">आपकी स्क्रिप्ट</label>
<textarea id="script" maxlength="5000"
placeholder="यहाँ अपनी हिंदी कहानी लिखें..."></textarea>
<p id="count">0 / 5000 अक्षर</p>

<label for="voice">उपलब्ध हिंदी आवाज़</label>
<select id="voice">
<option value="">आवाज़ें लोड हो रही हैं...</option>
</select>

<label for="speed">बोलने की गति</label>
<select id="speed">
<option value="0.8">धीमी और भावुक</option>
<option value="0.95" selected>सामान्य नैरेशन</option>
<option value="1.1">थोड़ी तेज़</option>
</select>

<button id="speak">▶ आवाज़ सुनें</button>
<button id="stop" class="secondary">■ आवाज़ रोकें</button>
<p id="status">तैयार है।</p>
<p>नोट: यह आपके ब्राउज़र की उपलब्ध आवाज़ इस्तेमाल करता है। MP3 डाउनलोड और उन्नत AI voice के लिए अगला चरण आवश्यक है।</p>
</main>

<script>
const textBox = document.getElementById("script");
const voiceBox = document.getElementById("voice");
const statusBox = document.getElementById("status");
let voices = [];

function loadVoices(){
  if(!("speechSynthesis" in window)){
    statusBox.textContent = "इस ब्राउज़र में voice सुविधा उपलब्ध नहीं है।";
    return;
  }
  voices = speechSynthesis.getVoices();
  const hindi = voices
    .map((v,i)=>({v,i}))
    .filter(x=>x.v.lang.toLowerCase().startsWith("hi"));

  voiceBox.innerHTML = "";
  if(!hindi.length){
    voiceBox.add(new Option("हिंदी आवाज़ उपलब्ध नहीं मिली",""));
    statusBox.textContent =
      "हिंदी आवाज़ नहीं मिली। फोन की Text-to-Speech सेटिंग जाँचें।";
    return;
  }
  voiceBox.add(new Option("फोन की डिफ़ॉल्ट हिंदी आवाज़",""));
  hindi.forEach(x=>{
    voiceBox.add(new Option(x.v.name+" ("+x.v.lang+")",String(x.i)));
  });
  statusBox.textContent = "हिंदी आवाज़ उपलब्ध है।";
}

textBox.addEventListener("input",()=>{
  document.getElementById("count").textContent =
    textBox.value.length+" / 5000 अक्षर";
});

document.getElementById("speak").addEventListener("click",()=>{
  const text = textBox.value.trim();
  if(!text){
    statusBox.textContent="पहले अपनी स्क्रिप्ट लिखें।";
    return;
  }
  if(!("speechSynthesis" in window)){
    statusBox.textContent="यह ब्राउज़र speech सपोर्ट नहीं करता।";
    return;
  }
  speechSynthesis.cancel();
  const utterance = new SpeechSynthesisUtterance(text);
  utterance.lang="hi-IN";
  utterance.rate=Number(document.getElementById("speed").value);
  if(voiceBox.value!==""){
    utterance.voice=voices[Number(voiceBox.value)];
  } else {
    const v=voices.find(x=>x.lang.toLowerCase().startsWith("hi"));
    if(v) utterance.voice=v;
  }
  utterance.onstart=()=>statusBox.textContent="आवाज़ चल रही है...";
  utterance.onend=()=>statusBox.textContent="पढ़ना पूरा हुआ।";
  utterance.onerror=()=>statusBox.textContent="आवाज़ नहीं चल पाई।";
  speechSynthesis.speak(utterance);
});

document.getElementById("stop").addEventListener("click",()=>{
  speechSynthesis.cancel();
  statusBox.textContent="आवाज़ रोक दी गई।";
});

if("speechSynthesis" in window){
  loadVoices();
  speechSynthesis.onvoiceschanged=loadVoices;
}
</script>
</body>
</html>
