<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>維尼中彰投大台中車隊｜車資試算</title>

<style>
body{font-family:Arial,sans-serif;background:#f5f5f5;margin:0;padding:20px;text-align:center;}
.card{background:white;padding:25px;border-radius:15px;max-width:400px;margin:auto;box-shadow:0 3px 10px rgba(0,0,0,0.1);}
input{width:90%;padding:12px;margin:10px 0;font-size:16px;border-radius:8px;border:1px solid #ccc;}
button{width:95%;padding:14px;margin-top:15px;font-size:18px;border:none;border-radius:10px;background:#ff9800;color:white;cursor:pointer;}
button:hover{background:#e68900;}
.result{font-size:18px;margin-top:20px;font-weight:bold;color:#e65100;text-align:left;}
.note{margin-top:15px;font-size:14px;color:#666;text-align:left;}
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
function openLine(){
    window.open("https://lin.ee/1aSbon2");
}

function calcFare(){
    let start = document.getElementById("start").value.trim();
    let end = document.getElementById("end").value.trim();

    if(!start || !end){
        alert("請完整輸入上車與下車地址");
        return;
    }

    let service = new google.maps.DistanceMatrixService();
    service.getDistanceMatrix({
        origins: [start],
        destinations: [end],
        travelMode: 'DRIVING',
        unitSystem: google.maps.UnitSystem.METRIC,
    }, function(response, status){
        if(status !== 'OK'){
            document.getElementById("result").innerHTML = "距離計算失敗，請稍後再試";
            return;
        }

        let element = response.rows[0].elements[0];
        if(element.status !== "OK"){
            document.getElementById("result").innerHTML = "找不到路線，請確認地址是否在服務範圍";
            return;
        }

        let distanceKm = element.distance.value / 1000;
        let durationMin = element.duration.value / 60;

        let fare = 80 + (distanceKm * 15) + (durationMin * 3);
        if(distanceKm > 15){
            fare += (distanceKm - 15) * 10;
        }
        fare = Math.round(fare);

        document.getElementById("result").innerHTML =
          `預估距離：${distanceKm.toFixed(1)} km<br>` +
          `預估時間：${Math.round(durationMin)} 分鐘<br>` +
          `預估車資：${fare} 元`;
    });
}
</script>

<!-- 請改成你自己的 KEY -->
<script async
  src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCMi3iCO0lZuw3XfaUoKxBrQJMGFbiz5po"></script>

</body>
</html>
