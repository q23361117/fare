# fare<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>維尼中彰投大台中車隊｜車資試算</title>
<style>
body{
  font-family: Arial, sans-serif;
  background:#f5f5f5;
  margin:0;
  padding:20px;
  text-align:center;
}
.card{
  background:white;
  padding:25px;
  border-radius:15px;
  max-width:400px;
  margin:auto;
  box-shadow:0 3px 10px rgba(0,0,0,0.1);
}
h2{
  margin-bottom:20px;
}
input, select{
  width:90%;
  padding:12px;
  margin:10px 0;
  font-size:16px;
  border-radius:8px;
  border:1px solid #ccc;
}
button{
  width:95%;
  padding:14px;
  margin-top:15px;
  font-size:18px;
  border:none;
  border-radius:10px;
  background:#ff9800;
  color:white;
  cursor:pointer;
}
button:hover{
  background:#e68900;
}
.result{
  font-size:18px;
  margin-top:20px;
  font-weight:bold;
  color:#e65100;
  text-align:left;
}
.note{
  margin-top:15px;
  font-size:14px;
  color:#666;
  text-align:left;
}
</style>
</head>
<body>

<div class="card">
<h2>🚖 維尼中彰投大台中車隊<br>車資試算</h2>

<input type="text" id="start" placeholder="請輸入上車地點（中彰投）">
<input type="text" id="end" placeholder="請輸入下車地點（中彰投）">

<button onclick="calcFare()">試算車資</button>

<div class="result" id="result"></div>

<div class="note">
※ 實際價格依司機跳錶為準<br>
※ 塞車尖峰時段價格會有浮動
</div>

<button onclick="openLine()">🚖 立即叫車</button>
</div>

<script>
// 這裡用簡單 Google Maps Distance Matrix API 計算距離與時間
// 你需要先申請 API KEY，放到 apiKey 變數
const apiKey = "AIzaSyCMi3iCO0lZuw3XfaUoKxBrQJMGFbiz5po";

function calcFare(){
  let start = document.getElementById("start").value;
  let end = document.getElementById("end").value;

  if(start=="" || end==""){
    alert("請輸入完整上車與下車地點");
    return;
  }

  fetch(`https://maps.googleapis.com/maps/api/distancematrix/json?units=metric&origins=${encodeURIComponent(start)}&destinations=${encodeURIComponent(end)}&key=${apiKey}`)
  .then(response => response.json())
  .then(data => {
    try{
      let distanceMeters = data.rows[0].elements[0].distance.value;
      let durationSec = data.rows[0].elements[0].duration.value;

      let distanceKm = distanceMeters/1000;
      let durationMin = durationSec/60;

      // 計算車資
      let fare = 80 + (distanceKm*15) + (durationMin*3);
      if(distanceKm > 15){
        fare += (distanceKm-15)*10;
      }
      fare = Math.round(fare);

      // 顯示結果
      document.getElementById("result").innerHTML = 
      `預估距離：${distanceKm.toFixed(1)} km<br>`+
      `預估時間：${Math.round(durationMin)} 分鐘<br>`+
      `預估車資：${fare} 元`;
    }catch(e){
      alert("無法計算距離，請確認地址是否正確（僅限中彰投）");
    }
  })
  .catch(err=>{
    alert("距離計算失敗，請稍後再試");
    console.log(err);
  });
}

function openLine(){
  window.open("https://lin.ee/1aSbon2");
}
</script>

</body>
</html>
