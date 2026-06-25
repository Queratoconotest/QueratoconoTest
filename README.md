<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>Image Processing Lab v1.0</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:Arial,Helvetica,sans-serif;
}

body{
    background:#121212;
    color:#fff;
    display:flex;
    flex-direction:column;
    align-items:center;
    min-height:100vh;
    padding:16px;
}

h1{
    margin:12px 0 18px;
    font-size:28px;
    text-align:center;
}

#container{
    position:relative;
    width:100%;
    max-width:720px;
    border-radius:14px;
    overflow:hidden;
    background:#000;
}

video{
    width:100%;
    display:block;
}

canvas{
    position:absolute;
    inset:0;
    width:100%;
    height:100%;
    pointer-events:none;
    mix-blend-mode:screen;
}

button{
    margin-top:18px;
    padding:14px 24px;
    border:none;
    border-radius:10px;
    background:#2196F3;
    color:white;
    font-size:18px;
    cursor:pointer;
}

button:disabled{
    background:#555;
    cursor:default;
}

.controls{
    width:100%;
    max-width:720px;
    margin-top:20px;
}

.control{
    margin-bottom:18px;
}

label{
    display:flex;
    justify-content:space-between;
    margin-bottom:8px;
    font-size:15px;
}

input[type=range]{
    width:100%;
}
</style>
</head>

<body>

<h1>Image Processing Lab v1.0</h1>

<div id="container">
    <video id="video" autoplay playsinline muted></video>
    <canvas id="overlay"></canvas>
</div>

<button id="startBtn">📷 Abrir cámara</button>

<div class="controls">

    <div class="control">
        <label>
            <span>Desplazamiento Vertical</span>
            <span id="offsetValue">20 px</span>
        </label>
        <input id="offset" type="range" min="0" max="60" value="20">
    </div>

    <div class="control">
        <label>
            <span>Opacidad</span>
            <span id="opacityValue">60%</span>
        </label>
        <input id="opacity" type="range" min="0" max="100" value="60">
    </div>

</div>

<script>

const video=document.getElementById("video");
const canvas=document.getElementById("overlay");
const ctx=canvas.getContext("2d");

const offset=document.getElementById("offset");
const opacity=document.getElementById("opacity");

const offsetValue=document.getElementById("offsetValue");
const opacityValue=document.getElementById("opacityValue");

const startBtn=document.getElementById("startBtn");

offset.addEventListener("input",()=>{
    offsetValue.textContent=offset.value+" px";
});

opacity.addEventListener("input",()=>{
    opacityValue.textContent=opacity.value+"%";
});

async function startCamera(){

    if(!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia){
        alert("Este navegador no soporta acceso a la cámara.");
        return;
    }

    try{

        const stream=await navigator.mediaDevices.getUserMedia({
            video:{
                facingMode:"environment"
            },
            audio:false
        });

        video.srcObject=stream;

        startBtn.disabled=true;
        startBtn.textContent="✅ Cámara iniciada";

    }catch(e){

        console.error(e);

        alert(
            "No fue posible acceder a la cámara.\n\n" +
            "Error: " + e.name + "\n\n" +
            "Si abriste este archivo desde Descargas, subilo a un sitio HTTPS (por ejemplo GitHub Pages) o ejecutalo desde un servidor local."
        );
    }
}

startBtn.addEventListener("click",startCamera);

video.addEventListener("loadedmetadata",()=>{

    canvas.width=video.videoWidth;
    canvas.height=video.videoHeight;

    render();

});

function render(){

    requestAnimationFrame(render);

    if(video.readyState<2) return;

    ctx.clearRect(0,0,canvas.width,canvas.height);

    const w=canvas.width;
    const h=canvas.height;
    const half=h/2;

    ctx.save();

    ctx.filter="blur(6px)";
    ctx.globalAlpha=opacity.value/100;

    ctx.drawImage(
        video,
        0,
        half,
        w,
        half,
        0,
        half-Number(offset.value),
        w,
        half
    );

    ctx.restore();

}

</script>

</body>
</html>
