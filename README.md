<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>維尼中彰投大台中車隊｜車資試算</title>

<style>
body{
font-family: Arial;
background:#f5f5f5;
margin:0;
padding:15px;
text-align:center;
}

.card{
background:white;
padding:20px;
border-radius:15px;
max-width:420px;
margin:auto;
box-shadow:0 3px 10px rgba(0,0,0,0.1);
}

input{
width:95%;
padding:12px;
margin:8px 0;
font-size:16px;
border-radius:8px;
border:1px solid #ccc;
}

button{
width:100%;
padding:14px;
margin-top:10px;
font-size:18px;
border:none;
border-radius:10px;
background:#ff9800;
color:white;
cursor:pointer;
}

#map{
width:100%;
height:350px;
margin-top:15px;
border-radius:10px;
}

.result{
font-size:16px;
margin-top:15px;
font-weight:bold;
color:#e65100;
text-align:left;
}

.note{
margin-top:10px;
font-size:13px;
color:#666;
text-align:left;
}
</style>
</head>

<body>

<div class="card">
<h3>🚖 維尼中彰投大台中車隊</h3>

<input id="start" placeholder="上車地點（中彰投）">
<input id="end" placeholder="下車地點（中彰投）">

<button onclick="calcRoute()">試算車資</button>

<div id="map"></div>

<div class="result" id="result"></div>

<div class="note">
※ 實際價格依司機跳錶為準<br>
※ 塞車尖峰時段價格會有浮動
</div>

<button onclick="openLine()">🚖 立即叫車</button>
</div>

<script>
let map;
let directionsService;
let directionsRenderer;
let startMarker;
let endMarker;

function initMap(){
map = new google.maps.Map(document.getElementById("map"), {
zoom: 12,
center: {lat:24.1477, lng:120.6736}
});

directionsService = new google.maps.DirectionsService();

directionsRenderer = new google.maps.DirectionsRenderer({
map: map,
suppressMarkers: true,
preserveViewport: true
});

function calcRoute(){

let start = document.getElementById("start").value.trim();
let end = document.getElementById("end").value.trim();

if(!start || !end){
alert("請輸入完整地址");
return;
}

let request = {
origin: start,
destination: end,
travelMode: 'DRIVING'
};

directionsService.route(request, function(result, status){

if(status === 'OK'){

// 顯示路線
directionsRenderer.setDirections(result);

directionsRenderer.setDirections(result);

let leg = result.routes[0].legs[0];

// 手動把地圖中心拉到路線
map.fitBounds(result.routes[0].bounds);

// 等地圖完成渲染再放 Marker（關鍵）
google.maps.event.addListenerOnce(map, 'idle', function () {
    drawMarkers(leg);
});

// 先縮放地圖
map.fitBounds(result.routes[0].bounds);

// 延遲畫標記（關鍵）
setTimeout(function(){
    drawMarkers(leg);
}, 300);

// 畫定位點
drawMarkers(leg);

// 自動縮放
let bounds = new google.maps.LatLngBounds();
bounds.extend(leg.start_location);
bounds.extend(leg.end_location);
map.fitBounds(bounds);

// 距離時間
let distanceKm = leg.distance.value / 1000;
let durationMin = leg.duration.value / 60;

// 車資計算
let fare = 80 + (distanceKm * 15) + (durationMin * 3);

if(distanceKm > 15){
fare += (distanceKm - 15) * 10;
}

fare = Math.round(fare);

// 顯示
document.getElementById("result").innerHTML =
`預估距離：${distanceKm.toFixed(1)} km<br>
預估時間：${Math.round(durationMin)} 分鐘<br>
預估車資：${fare} 元`;

}else{
alert("距離計算失敗，請重新輸入地址");
}

});
}

function drawMarkers(leg){

if(startMarker) startMarker.setMap(null);
if(endMarker) endMarker.setMap(null);

// 起點
startMarker = new google.maps.Marker({
position: leg.start_location,
map: map,
icon: {
url: "https://maps.google.com/mapfiles/ms/icons/green-dot.png"
},
zIndex: 9999
});

// 終點
endMarker = new google.maps.Marker({
position: leg.end_location,
map: map,
icon: {
url: "https://maps.google.com/mapfiles/ms/icons/red-dot.png"
},
zIndex: 9999
});
}



function openLine(){
window.open("https://lin.ee/1aSbon2");
}
</script>

<script async defer
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCMi3iCO0lZuw3XfaUoKxBrQJMGFbiz5po&callback=initMap">
</script>

</body>
</html>
