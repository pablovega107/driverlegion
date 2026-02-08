<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Taxímetro Legion Gaming</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <style>
    body{
      margin:0;
      font-family: Arial, Helvetica, sans-serif;
      background:#05070c;
      color:white;
      display:flex;
      justify-content:center;
      align-items:center;
      min-height:100vh;
    }
    .app{
      width:360px;
      background:#0b1220;
      border-radius:18px;
      padding:20px;
      box-shadow:0 0 30px rgba(0,255,255,.25);
    }
    .logo{
      text-align:center;
      margin-bottom:10px;
    }
    .logo img{
      max-width:220px;
    }
    h2{
      text-align:center;
      color:#22d3ee;
      margin:10px 0;
    }
    label{
      font-size:14px;
    }
    input{
      width:100%;
      padding:10px;
      margin:6px 0 12px;
      border-radius:8px;
      border:none;
      outline:none;
    }
    button{
      width:100%;
      padding:12px;
      border:none;
      border-radius:10px;
      font-weight:bold;
      cursor:pointer;
      margin-top:6px;
      background:linear-gradient(135deg,#22d3ee,#14b8a6);
    }
    .stop{
      background:linear-gradient(135deg,#ef4444,#f97316);
      color:white;
    }
    .hidden{display:none;}

    .meter{
      background:#020617;
      border-radius:12px;
      padding:15px;
      margin-top:15px;
      text-align:center;
    }
    .price{
      font-size:36px;
      color:#22d3ee;
      font-weight:bold;
    }
    .time{
      font-size:14px;
      opacity:.8;
    }

    .admin{
      margin-top:20px;
      background:#020617;
      border-radius:12px;
      padding:15px;
    }
    .log{
      background:#020617;
      margin-top:10px;
      padding:8px;
      border-radius:8px;
      max-height:140px;
      overflow-y:auto;
      font-size:12px;
    }
  </style>
</head>

<body>
<div class="app">

  <div class="logo">
    <img src="legion_logo.png" alt="Legion Gaming">
  </div>

  <h2>Taxímetro</h2>

  <label>Nombre del Taxista</label>
  <input id="taxista" placeholder="Ej. Vega">

  <label>Nombre del Cliente</label>
  <input id="cliente" placeholder="Ej. Juan">

  <button onclick="iniciar()">Iniciar viaje</button>
  <button id="btnStop" class="stop hidden" onclick="detener()">Finalizar viaje</button>

  <div class="meter">
    <div class="price" id="precio">$0</div>
    <div class="time" id="tiempo">00:00</div>
  </div>

  <div class="admin">
    <h2>Panel Admin</h2>
    <input id="adminPass" type="password" placeholder="Contraseña">
    <button onclick="loginAdmin()">Entrar</button>

    <div id="adminPanel" class="hidden">
      <p><b>Activos:</b> <span id="activos">0</span></p>
      <div class="log" id="logs"></div>
    </div>
  </div>

</div>

<script>
  let segundos = 0;
  let precio = 0;
  let intervalo;
  let activos = 0;

  const TARIFA_POR_SEGUNDO = 8.5;

  let registros = JSON.parse(localStorage.getItem("registros_legion")) || [];

  function iniciar(){
    const t = taxista.value;
    const c = cliente.value;
    if(!t || !c){
      alert("Pon taxista y cliente");
      return;
    }

    btnStop.classList.remove("hidden");
    activos++;
    actualizarActivos();

    registros.push({
      tipo:"inicio",
      taxista:t,
      cliente:c,
      hora:new Date().toLocaleString()
    });
    guardar();

    intervalo = setInterval(()=>{
      segundos++;
      precio += TARIFA_POR_SEGUNDO;
      precioEl.textContent = "$" + Math.round(precio);
      tiempoEl.textContent = formato(segundos);
    },1000);
  }

  function detener(){
    clearInterval
