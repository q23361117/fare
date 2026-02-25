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
center: {lat:24.1477, lng:120.6736},
mapId: "c5b92407e1dbcc2a3d20b950"   // 你的 Map ID
});

directionsService = new google.maps.DirectionsService();

directionsRenderer = new google.maps.DirectionsRenderer({
map: map,
suppressMarkers: true,
draggable: true
});

// 拖曳路線時自動更新
directionsRenderer.addListener("directions_changed", function(){
let result = directionsRenderer.getDirections();
if(result){
let leg = result.routes[0].legs[0];
drawMarkers(leg);
updateFare(leg);
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

directionsRenderer.setDirections(result);

let leg = result.routes[0].legs[0];
drawMarkers(leg);
updateFare(leg);

}else{
alert("距離計算失敗");
}
});
}

function drawMarkers(leg){

if(startMarker) startMarker.setMap(null);
if(endMarker) endMarker.setMap(null);

// 起點（綠色）
startMarker = new google.maps.marker.AdvancedMarkerElement({
map: map,
position: leg.start_location,
content: new google.maps.marker.PinElement({
background: "#4CAF50",
glyphColor: "white"
}).element,
title: "起點"
});

// 終點（紅色）
endMarker = new google.maps.marker.AdvancedMarkerElement({
map: map,
position: leg.end_location,
content: new google.maps.marker.PinElement({
background: "#FF0000",
glyphColor: "white"
}).element,
title: "終點"
});
}

function updateFare(leg){

let distanceKm = leg.distance.value / 1000;
let durationMin = leg.duration.value / 60;

// 計費公式
let fare = 80 + (distanceKm * 15) + (durationMin * 3);

// 15公里以上加成
if(distanceKm > 15){
fare += (distanceKm - 15) * 10;
}

// 夜間加成（23:00–06:00）
let now = new Date();
let hour = now.getHours();
if(hour >= 23 || hour < 6){
fare *= 1.2;
}

// 最低車資
if(fare < 120){
fare = 120;
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
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCMi3iCO0lZuw3XfaUoKxBrQJMGFbiz5po&callback=initMap&libraries=marker">
</script>

</body>
</html>
