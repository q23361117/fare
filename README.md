<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>車資試算</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
body{font-family:Arial;margin:10px;}
input{width:100%;padding:8px;margin:5px 0;}
button{padding:8px 12px;margin:5px 3px;}
#map{height:400px;width:100%;margin-top:10px;}
#result{margin-top:10px;font-size:16px;}
</style>
</head>

<body>

<h3>車資試算</h3>

<input id="start" placeholder="起點">
<input id="end" placeholder="終點">
<button onclick="calcRoute()">計算路線</button>

<div id="map"></div>
<div id="result"></div>

<script>
let map;
let directionsService;
let directionsRenderer;
let startMarker;
let endMarker;
let allRoutes = [];

function initMap(){
map = new google.maps.Map(document.getElementById("map"), {
zoom: 12,
center: {lat:24.1477, lng:120.6736} // 台中
});

directionsService = new google.maps.DirectionsService();

directionsRenderer = new google.maps.DirectionsRenderer({
map: map,
suppressMarkers: true // 用自己的圓點
});
}

function calcRoute(){

let start = document.getElementById("start").value;
let end = document.getElementById("end").value;

let request = {
origin: start,
destination: end,
travelMode: 'DRIVING',
provideRouteAlternatives: true
};

directionsService.route(request, function(result, status){

if(status === 'OK'){

allRoutes = result.routes;

// 顯示第一條路線
directionsRenderer.setDirections(result);
directionsRenderer.setRouteIndex(0);

showRouteButtons();
updateRoute(0);

}else{
alert("距離計算失敗，請確認地址");
}

});
}

// 顯示路線選擇
function showRouteButtons(){

let html = "<b>選擇路線：</b><br>";

allRoutes.forEach((route, index)=>{
let leg = route.legs[0];
let time = Math.round(leg.duration.value/60);
let dist = (leg.distance.value/1000).toFixed(1);

html += `<button onclick="updateRoute(${index})">
路線${index+1}：${time}分 / ${dist}km
</button><br>`;
});

document.getElementById("result").innerHTML = html;
}

// 切換路線
function updateRoute(index){

directionsRenderer.setRouteIndex(index);

let leg = allRoutes[index].legs[0];

drawMarkers(leg);
calcFare(leg);
}

// 畫起終點圓點
function drawMarkers(leg){

let startLocation = leg.start_location;
let endLocation = leg.end_location;

// 清除舊標記
if(startMarker) startMarker.setMap(null);
if(endMarker) endMarker.setMap(null);

// 起點綠圓
startMarker = new google.maps.Marker({
position: startLocation,
map: map,
zIndex: 999,
icon:{
path: google.maps.SymbolPath.CIRCLE,
scale: 8,
fillColor: "#00c853",
fillOpacity: 1,
strokeColor: "#ffffff",
strokeWeight: 2
}
});

// 終點紅圓
endMarker = new google.maps.Marker({
position: endLocation,
map: map,
zIndex: 999,
icon:{
path: google.maps.SymbolPath.CIRCLE,
scale: 8,
fillColor: "#d50000",
fillOpacity: 1,
strokeColor: "#ffffff",
strokeWeight: 2
}
});

// 自動縮放
let bounds = new google.maps.LatLngBounds();
bounds.extend(startLocation);
bounds.extend(endLocation);
map.fitBounds(bounds);
}


// 車資計算
function calcFare(leg){

let distanceKm = leg.distance.value / 1000;
let durationMin = leg.duration.value / 60;

let fare = 80 + (distanceKm * 15) + (durationMin * 3);

if(distanceKm > 15){
fare += (distanceKm - 15) * 10;
}

fare = Math.round(fare);

document.getElementById("result").innerHTML += `
<br><br>
預估距離：${distanceKm.toFixed(1)} km
<br>
預估時間：${Math.round(durationMin)} 分鐘
<br>
預估車資：<b>${fare} 元</b>
`;
}
</script>

<!-- 換成你的 API KEY -->
<script async defer
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCMi3iCO0lZuw3XfaUoKxBrQJMGFbiz5po&callback=initMap">
</script>

</body>
</html>
