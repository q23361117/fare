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
height:380px;
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
</style>
</head>

<body>

<div class="card">
<h3>🚖 維尼中彰投大台中車隊</h3>

<input id="start" placeholder="上車地點">
<input id="end" placeholder="下車地點">

<button onclick="calcRoute()">試算車資</button>

<div id="map"></div>

<div class="result" id="result"></div>

<button onclick="openLine()">🚖 立即叫車</button>
</div>

<script>
let map;
let directionsService;
let directionsRenderer;
let startMarker = null;
let endMarker = null;

function initMap(){
map = new google.maps.Map(document.getElementById("map"), {
zoom: 12,
center: {lat:24.1477, lng:120.6736}
});

directionsService = new google.maps.DirectionsService();

// allow route selection
directionsRenderer = new google.maps.DirectionsRenderer({
map: map,
suppressMarkers: true,
draggable: true
});

// 使用者改路線時重新計算車資
directionsRenderer.addListener("directions_changed", function(){
let result = directionsRenderer.getDirections();
if(result){
updateFare(result.routes[0].legs[0]);
}
});
}

function calcRoute(){

let start = document.getElementById("start").value.trim();
let end = document.getElementById("end").value.trim();

if(!start || !end){
alert("請輸入完整地址");
return;
}

directionsService.route({
origin: start,
destination: end,
travelMode: 'DRIVING',
provideRouteAlternatives: true
}, function(result, status){

if(status === 'OK'){

// 顯示路線（可選擇）
directionsRenderer.setDirections(result);

// 畫紅綠點（關鍵：延遲執行）
setTimeout(function(){
drawMarkers(result.routes[0].legs[0]);
updateFare(result.routes[0].legs[0]);
}, 300);

}else{
alert("距離計算失敗");
}
});
}

function drawMarkers(leg){

// 清除舊點
if(startMarker) startMarker.setMap(null);
if(endMarker) endMarker.setMap(null);

// 起點綠點
startMarker = new google.maps.Marker({
position: leg.start_location,
map: map,
icon: {
url: "https://maps.google.com/mapfiles/ms/icons/green-dot.png"
}
});

// 終點紅點
endMarker = new google.maps.Marker({
position: leg.end_location,
map: map,
icon: {
url: "https://maps.google.com/mapfiles/ms/icons/red-dot.png"
}
});
}

function updateFare(leg){

let distanceKm = leg.distance.value / 1000;
let durationMin = leg.duration.value / 60;

let fare = 80 + (distanceKm * 15) + (durationMin * 3);

if(distanceKm > 15){
fare += (distanceKm - 15) * 10;
}

fare = Math.round(fare);

document.getElementById("result").innerHTML =
`預估距離：${distanceKm.toFixed(1)} km<br>
預估時間：${Math.round(durationMin)} 分鐘<br>
預估車資：${fare} 元`;
}

function openLine(){
window.open("https://lin.ee/1aSbon2");
}
</script>

<script async
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCMi3iCO0lZuw3XfaUoKxBrQJMGFbiz5po&callback=initMap">
</script>

</body>
</html>
