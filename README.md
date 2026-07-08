<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>119 소방 앱</title>
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }
html, body { width: 100%; height: 100%; font-family: "Apple SD Gothic Neo", "Pretendard", sans-serif; background: #0b1220; overflow: hidden; }

/* ── 하단 탭 네비 ── */
#bottomNav {
  position: fixed; bottom: 0; left: 0; right: 0;
  height: 54px; background: rgba(10,17,35,0.98);
  border-top: 1px solid #1e293b;
  display: flex; z-index: 200;
}
.navBtn {
  flex: 1; background: none; border: none; color: #475569;
  font-size: 11px; font-weight: 600; cursor: pointer;
  display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 2px;
  -webkit-tap-highlight-color: transparent; transition: color 0.2s;
}
.navBtn .ico { font-size: 22px; line-height: 1; }
.navBtn.active { color: #38bdf8; }

/* ── 페이지 ── */
.page { position: fixed; inset: 0; bottom: 54px; display: none; }
.page.active { display: block; }
#page-map.active { display: flex; flex-direction: column; }

/* ══════════════════════════
   PAGE 1 ─ 출동 시뮬레이터
══════════════════════════ */
#map { position: absolute; inset: 0; }

#panel {
  position: absolute; background: rgba(13,20,40,0.95);
  border: 1px solid #1e293b; color: #e2e8f0;
  box-shadow: 0 8px 32px rgba(0,0,0,.5); z-index: 10; overflow-y: auto;
  top: 12px; left: 12px; width: 300px; max-height: calc(100% - 24px);
  border-radius: 14px; padding: 16px;
}
@media (max-width: 640px) {
  #panel {
    top: auto; left: 0; right: 0; bottom: 0; width: 100%; max-height: 52vh;
    border-radius: 18px 18px 0 0; padding: 14px 16px 16px;
    border-left: none; border-right: none; border-bottom: none;
  }
  #panel::before {
    content: ''; display: block; width: 40px; height: 4px;
    background: #334155; border-radius: 2px; margin: 0 auto 10px;
  }
}
#panel h1 { font-size: 15px; font-weight: 700; color: #f8fafc; margin-bottom: 3px; }
#panel .sub { font-size: 11px; color: #64748b; margin-bottom: 10px; line-height: 1.5; }
#addrRow { display: flex; gap: 6px; margin-bottom: 8px; }
#addrInput {
  flex: 1; background: #0f172a; border: 1px solid #334155; border-radius: 8px;
  color: #e2e8f0; font-size: 13px; padding: 8px 10px; outline: none; -webkit-appearance: none;
}
#addrInput::placeholder { color: #475569; }
#addrInput:focus { border-color: #38bdf8; }
#addrBtn {
  background: #1d4ed8; color: #fff; border: none; border-radius: 8px;
  font-size: 13px; font-weight: 600; padding: 8px 12px; cursor: pointer; white-space: nowrap;
}
#addrDropdown { display: none; background: #0f172a; border: 1px solid #334155; border-radius: 8px; margin-bottom: 8px; overflow: hidden; }
#addrDropdown.open { display: block; }
#addrDropdown li { list-style: none; padding: 9px 12px; font-size: 12px; color: #cbd5e1; border-bottom: 1px solid #1e293b; cursor: pointer; }
#addrDropdown li:last-child { border-bottom: none; }
#addrDropdown li strong { color: #38bdf8; font-size: 13px; display: block; }
#status { font-size: 11px; color: #38bdf8; margin-bottom: 8px; min-height: 15px; font-family: monospace; }
#resultList { list-style: none; }
#resultList li { display: flex; align-items: center; gap: 7px; padding: 8px 10px; border-radius: 8px; background: #0f172a; margin-bottom: 5px; font-size: 13px; }
#resultList .rank { font-family: monospace; font-size: 10px; color: #64748b; width: 16px; flex-shrink: 0; }
#resultList .dot { width: 10px; height: 10px; border-radius: 50%; flex-shrink: 0; }
#resultList .name { flex: 1; color: #e2e8f0; font-size: 12px; }
#resultList .time { font-family: monospace; font-weight: 700; color: #facc15; font-size: 13px; }
#resultList .dist { font-family: monospace; font-size: 11px; color: #64748b; }
#resultList li.error { color: #f87171; }
.station-label { background: rgba(15,23,42,.9); color: #e2e8f0; font-size: 11px; padding: 3px 7px; border-radius: 6px; white-space: nowrap; border: 1px solid #334155; pointer-events: none; }
.eta-badge { color: #0b1220; font-size: 12px; font-weight: 700; padding: 3px 9px; border-radius: 999px; white-space: nowrap; pointer-events: none; box-shadow: 0 2px 6px rgba(0,0,0,.4); font-family: monospace; }
#hint { position: absolute; bottom: calc(52% + 8px); left: 50%; transform: translateX(-50%); background: rgba(13,20,40,.88); color: #94a3b8; font-size: 12px; padding: 7px 15px; border-radius: 999px; border: 1px solid #1e293b; z-index: 9; pointer-events: none; white-space: nowrap; }
@media (min-width: 641px) { #hint { bottom: 16px; } }

/* ══════════════════════════
   PAGE 2 ─ 소방작전도
══════════════════════════ */
#mapHeader {
  padding: 10px 12px 8px;
  background: rgba(13,20,40,0.97);
  border-bottom: 1px solid #1e293b;
  flex-shrink: 0;
}
#floorControls {
  display: flex; align-items: center; gap: 8px; flex-wrap: wrap; margin-bottom: 8px;
}
#floorControls label { color: #e2e8f0; font-size: 13px; display: flex; align-items: center; gap: 4px; }
.num-input {
  width: 50px; background: #0f172a; border: 1px solid #334155; border-radius: 6px;
  color: #e2e8f0; font-size: 14px; padding: 5px 4px; text-align: center; outline: none;
}
#buildBtn {
  background: #dc2626; color: #fff; border: none; border-radius: 8px;
  font-size: 13px; font-weight: 700; padding: 6px 14px; cursor: pointer;
  -webkit-tap-highlight-color: transparent;
}
#buildBtn:active { background: #b91c1c; }
#customUnitRow { display: flex; gap: 6px; }
#customUnitInput {
  flex: 1; background: #0f172a; border: 1px solid #334155; border-radius: 6px;
  color: #e2e8f0; font-size: 12px; padding: 6px 8px; outline: none;
}
#customUnitInput::placeholder { color: #475569; }
#customUnitInput:focus { border-color: #4ade80; }
#addUnitBtn {
  background: #065f46; color: #fff; border: none; border-radius: 6px;
  font-size: 12px; font-weight: 700; padding: 6px 12px; cursor: pointer;
  -webkit-tap-highlight-color: transparent;
}

/* 메인 영역 */
#mapMain { flex: 1; display: flex; overflow: hidden; }

/* 건물 */
#buildingArea {
  width: 48%; overflow-y: auto; padding: 8px 6px 8px 8px;
  border-right: 1px solid #1e293b;
}
#noBuildingMsg { color: #475569; font-size: 12px; text-align: center; margin-top: 30px; line-height: 1.8; }

.floor-zone {
  display: flex; align-items: stretch;
  min-height: 46px; margin-bottom: 2px; border-radius: 6px;
  border: 1.5px solid #1e293b; overflow: hidden;
  transition: border-color 0.12s, background 0.12s;
}
.floor-zone.ground { background: #0f1f35; }
.floor-zone.basement { background: #0a1520; }
.floor-zone.roof { background: #1a0a2e; border-color: #7c3aed; min-height: 34px; }
.floor-zone.drag-over { border-color: #38bdf8 !important; background: rgba(56,189,248,0.1) !important; }

.floor-label {
  width: 34px; flex-shrink: 0; display: flex; align-items: center; justify-content: center;
  font-size: 10px; font-weight: 700; font-family: monospace;
  background: #1e293b; color: #94a3b8;
}
.floor-zone.basement .floor-label { background: #162032; color: #64748b; }
.floor-zone.roof .floor-label { background: #2d1b69; color: #c4b5fd; }

.floor-content {
  flex: 1; display: flex; flex-wrap: wrap; align-items: center;
  gap: 3px; padding: 4px 5px; min-height: 46px;
}
.floor-zone.roof .floor-content { min-height: 34px; }

.placed-chip {
  display: inline-flex; align-items: center; gap: 3px;
  padding: 2px 7px 2px 8px; border-radius: 999px;
  font-size: 10px; font-weight: 700; color: #0b1220;
  cursor: pointer; user-select: none; -webkit-tap-highlight-color: transparent;
  white-space: nowrap;
}
.placed-chip .remove-x { font-size: 11px; opacity: 0.7; margin-left: 1px; }

/* 출동대 패널 */
#unitPanel { width: 52%; overflow-y: auto; padding: 8px; }
.unit-section-label {
  font-size: 10px; color: #64748b; font-weight: 700;
  letter-spacing: 0.06em; padding: 2px 2px 5px; display: block;
}
#unitGrid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 5px; margin-bottom: 8px; }

.unit-icon {
  background: #111827; border: 1.5px solid #1e293b;
  border-radius: 9px; padding: 7px 4px; text-align: center;
  font-size: 10px; font-weight: 700; color: #e2e8f0;
  cursor: grab; user-select: none; touch-action: none;
  -webkit-tap-highlight-color: transparent;
  transition: opacity 0.2s, border-color 0.2s;
  line-height: 1.3; position: relative;
}
.unit-icon .u-emoji { font-size: 18px; display: block; margin-bottom: 2px; }
.unit-icon .u-name { display: block; word-break: keep-all; }
.unit-icon.placed { opacity: 0.3; cursor: default; }
.unit-icon.custom { border-color: #4ade80; }

/* 드래그 고스트 */
#dragGhost {
  position: fixed; pointer-events: none; z-index: 9999;
  background: #1e3a5f; border: 2px solid #38bdf8; border-radius: 9px;
  padding: 7px 4px; text-align: center; font-size: 10px; font-weight: 700; color: #e2e8f0;
  width: 75px; opacity: 0.92; display: none;
  box-shadow: 0 6px 20px rgba(0,0,0,.5); transform: scale(1.08);
  line-height: 1.3;
}
#dragGhost .u-emoji { font-size: 18px; display: block; margin-bottom: 2px; }

.reset-btn {
  width: 100%; margin-top: 6px; padding: 7px;
  background: #1e293b; border: 1px solid #334155; border-radius: 8px;
  color: #94a3b8; font-size: 12px; cursor: pointer;
  -webkit-tap-highlight-color: transparent;
}
.reset-btn:active { background: #0f172a; }
</style>
</head>
<body>

<!-- ══ PAGE 1: 출동 시뮬레이터 ══ -->
<div id="page-sim" class="page active">
  <div id="map"></div>
  <div id="hint">📍 지도 탭/클릭 = 화재 위치 지정</div>
  <div id="panel">
    <h1>🚒 119 다중 출동 시뮬레이터</h1>
    <div class="sub">주소 검색 또는 지도 탭으로 화재 위치를 지정하세요.</div>
    <div id="addrRow">
      <input id="addrInput" type="text" placeholder="주소 입력 (예: 신월동 123)">
      <button id="addrBtn">검색</button>
    </div>
    <ul id="addrDropdown"></ul>
    <div id="status">화재 위치를 지정해주세요.</div>
    <ol id="resultList"></ol>
  </div>
</div>

<!-- ══ PAGE 2: 소방작전도 ══ -->
<div id="page-map" class="page">
  <div id="mapHeader">
    <div id="floorControls">
      <label>지상 <input class="num-input" id="groundFloors" type="number" min="1" max="150" value="5"> 층</label>
      <label>지하 <input class="num-input" id="basementFloors" type="number" min="0" max="20" value="1"> 층</label>
      <button id="buildBtn">🏢 건물 생성</button>
    </div>
    <div id="customUnitRow">
      <input id="customUnitInput" type="text" placeholder="출동대 이름 직접 입력 후 추가">
      <button id="addUnitBtn">➕ 추가</button>
    </div>
  </div>

  <div id="mapMain">
    <div id="buildingArea">
      <div id="noBuildingMsg">⬆️ 층수를 입력하고<br>건물 생성을 누르세요</div>
    </div>
    <div id="unitPanel">
      <span class="unit-section-label">출동대 — 건물 층으로 끌어다 배치</span>
      <div id="unitGrid"></div>
      <button class="reset-btn" id="resetBtn">🔄 배치 초기화</button>
    </div>
  </div>
</div>

<!-- 드래그 고스트 -->
<div id="dragGhost"><span class="u-emoji">🚒</span><span class="u-name"></span></div>

<!-- 하단 탭 네비 -->
<div id="bottomNav">
  <button class="navBtn active" onclick="showPage('sim')">
    <span class="ico">🚒</span>출동예측
  </button>
  <button class="navBtn" onclick="showPage('map')">
    <span class="ico">🗺️</span>작전도
  </button>
</div>

<!-- ⚠️ 카카오 JavaScript 키로 교체 -->
<script src="//dapi.kakao.com/v2/maps/sdk.js?appkey=YOUR_KAKAO_JS_KEY&libraries=services"></script>
<script>
/* ================================================
   ⚠️ 설정값 — 본인 키/좌표로 교체
   ================================================ */
const TMAP_APP_KEY = "YOUR_TMAP_APP_KEY";

const fireStations = [
  { name: "양천소방서",      lat: 37.5270, lng: 126.8554 },
  { name: "신정안전센터",    lat: 37.0000, lng: 127.0000 }, // ⚠️ 실제 좌표로 교체
  { name: "목동안전센터",    lat: 37.0000, lng: 127.0000 },
  { name: "신월안전센터",    lat: 37.0000, lng: 127.0000 },
];

const ROUTE_COLORS = ["#38bdf8","#facc15","#4ade80","#fb923c","#c084fc","#f472b6","#22d3ee","#a3e635"];

/* ================================================
   탭 전환
   ================================================ */
let mapInitialized = false;
let kakaoMap = null;

function showPage(id) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.navBtn').forEach(b => b.classList.remove('active'));
  document.getElementById('page-' + id).classList.add('active');
  document.querySelectorAll('.navBtn')[id === 'sim' ? 0 : 1].classList.add('active');

  if (id === 'sim' && !mapInitialized) {
    initKakaoMap();
    mapInitialized = true;
  }
  if (id === 'sim' && kakaoMap) {
    setTimeout(() => kakaoMap.relayout(), 50);
  }
}

/* ================================================
   PAGE 1: 출동 시뮬레이터
   ================================================ */
function initKakaoMap() {
  kakaoMap = new kakao.maps.Map(document.getElementById('map'), {
    center: new kakao.maps.LatLng(fireStations[0].lat, fireStations[0].lng),
    level: 7
  });

  fireStations.forEach(st => {
    const pos = new kakao.maps.LatLng(st.lat, st.lng);
    new kakao.maps.Marker({ position: pos, map: kakaoMap });
    new kakao.maps.CustomOverlay({
      position: pos,
      content: `<div class="station-label">🚒 ${st.name}</div>`,
      yAnchor: 2.4, map: kakaoMap
    });
  });

  const geocoder = new kakao.maps.services.Geocoder();

  function searchAddress() {
    const q = document.getElementById('addrInput').value.trim();
    if (!q) return;
    geocoder.addressSearch(q, (result, status) => {
      const dd = document.getElementById('addrDropdown');
      dd.innerHTML = '';
      if (status === kakao.maps.services.Status.OK && result.length) {
        result.slice(0, 5).forEach(r => {
          const li = document.createElement('li');
          li.innerHTML = `<strong>${r.address_name}</strong>${r.road_address ? r.road_address.address_name : ''}`;
          li.addEventListener('click', () => {
            dd.classList.remove('open');
            document.getElementById('addrInput').value = r.address_name;
            runSimulation(parseFloat(r.y), parseFloat(r.x));
          });
          dd.appendChild(li);
        });
        dd.classList.add('open');
      } else {
        const li = document.createElement('li');
        li.textContent = '검색 결과가 없습니다.';
        dd.appendChild(li);
        dd.classList.add('open');
      }
    });
  }

  document.getElementById('addrBtn').addEventListener('click', searchAddress);
  document.getElementById('addrInput').addEventListener('keydown', e => { if (e.key === 'Enter') searchAddress(); });
  kakao.maps.event.addListener(kakaoMap, 'click', e => {
    runSimulation(e.latLng.getLat(), e.latLng.getLng());
    document.getElementById('addrDropdown').classList.remove('open');
  });
}

let fireOverlay = null;
let routeOverlays = [];

function clearRoutes() {
  routeOverlays.forEach(o => { if (o.setMap) o.setMap(null); });
  routeOverlays = [];
}

async function runSimulation(fireLat, fireLng) {
  clearRoutes();
  const statusEl = document.getElementById('status');
  const listEl = document.getElementById('resultList');
  statusEl.textContent = `경로 계산 중… (${fireStations.length}곳)`;
  listEl.innerHTML = '';

  if (fireOverlay) fireOverlay.setMap(null);
  fireOverlay = new kakao.maps.CustomOverlay({
    position: new kakao.maps.LatLng(fireLat, fireLng),
    content: `<div style="font-size:32px;filter:drop-shadow(0 2px 4px rgba(0,0,0,.5))">🔥</div>`,
    yAnchor: 1.0, map: kakaoMap
  });
  kakaoMap.panTo(new kakao.maps.LatLng(fireLat, fireLng));

  const results = await Promise.all(fireStations.map(st => getRoute(st, fireLat, fireLng)));
  results.sort((a, b) => {
    if (a.success && b.success) return a.totalTime - b.totalTime;
    return a.success ? -1 : 1;
  });

  results.forEach((r, rank) => {
    const color = ROUTE_COLORS[rank % ROUTE_COLORS.length];
    if (!r.success) { appendRow(listEl, rank, r.station.name, null, null, color, r.error); return; }
    const poly = new kakao.maps.Polyline({
      path: r.coords.map(c => new kakao.maps.LatLng(c[1], c[0])),
      strokeWeight: 5, strokeColor: color, strokeOpacity: 0.85, strokeStyle: 'solid'
    });
    poly.setMap(kakaoMap);
    routeOverlays.push(poly);
    const min = Math.max(1, Math.round(r.totalTime / 60));
    const km = (r.totalDistance / 1000).toFixed(1);
    const badge = new kakao.maps.CustomOverlay({
      position: new kakao.maps.LatLng(r.station.lat, r.station.lng),
      content: `<div class="eta-badge" style="background:${color}">${min}분</div>`,
      yAnchor: 3.4, map: kakaoMap
    });
    routeOverlays.push(badge);
    appendRow(listEl, rank, r.station.name, min, km, color, null);
  });
  statusEl.textContent = `🔥 화재 위치 설정 완료`;
}

async function getRoute(station, fireLat, fireLng) {
  try {
    const res = await fetch('https://apis.openapi.sk.com/tmap/routes?version=1&format=json', {
      method: 'POST',
      headers: { 'appKey': TMAP_APP_KEY, 'Content-Type': 'application/json', 'Accept': 'application/json' },
      body: JSON.stringify({
        startX: String(station.lng), startY: String(station.lat),
        endX: String(fireLng), endY: String(fireLat),
        reqCoordType: 'WGS84GEO', resCoordType: 'WGS84GEO', searchOption: '0'
      })
    });
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();
    let coords = [], totalTime = 0, totalDistance = 0;
    (data.features || []).forEach(f => {
      if (f.geometry?.type === 'LineString') coords = coords.concat(f.geometry.coordinates);
      if (f.properties?.totalTime > totalTime) totalTime = f.properties.totalTime;
      if (f.properties?.totalDistance > totalDistance) totalDistance = f.properties.totalDistance;
    });
    if (!coords.length) throw new Error('경로 없음');
    return { success: true, station, coords, totalTime, totalDistance };
  } catch (err) {
    return { success: false, station, error: err.message };
  }
}

function appendRow(listEl, rank, name, min, km, color, error) {
  const li = document.createElement('li');
  if (error) {
    li.classList.add('error');
    li.innerHTML = `<span class="rank">${rank+1}</span><span class="dot" style="background:${color}"></span><span class="name">${name}</span><span class="dist">${error}</span>`;
  } else {
    li.innerHTML = `<span class="rank">${rank+1}</span><span class="dot" style="background:${color}"></span><span class="name">${name}</span><span class="time">${min}분</span><span class="dist">${km}km</span>`;
  }
  listEl.appendChild(li);
}

/* ================================================
   PAGE 2: 소방작전도
   ================================================ */
const DEFAULT_UNITS = [
  { name: "양천대",      emoji: "🚒" },
  { name: "신정대",      emoji: "🚒" },
  { name: "신트리대",    emoji: "🚒" },
  { name: "신월대",      emoji: "🚒" },
  { name: "목동대",      emoji: "🚒" },
  { name: "양천구조대",  emoji: "🦺" },
  { name: "강서구조대",  emoji: "🦺" },
  { name: "구로구조대",  emoji: "🦺" },
  { name: "영등포구조대",emoji: "🦺" },
  { name: "등촌대",      emoji: "🚒" },
  { name: "화곡대",      emoji: "🚒" },
  { name: "당산대",      emoji: "🚒" },
  { name: "신도림대",    emoji: "🚒" },
  { name: "고척대",      emoji: "🚒" },
];

const UNIT_COLORS = [
  "#38bdf8","#facc15","#4ade80","#fb923c","#c084fc",
  "#f472b6","#22d3ee","#a3e635","#f87171","#818cf8",
  "#34d399","#fbbf24","#60a5fa","#e879f9","#2dd4bf","#f97316"
];

let units = DEFAULT_UNITS.map((u, i) => ({ ...u, color: UNIT_COLORS[i % UNIT_COLORS.length], custom: false }));
let placedMap = {}; // { unitName: floorId }

function buildingUnitColorOf(name) {
  const u = units.find(u => u.name === name);
  return u ? u.color : "#94a3b8";
}
function buildingUnitEmojiOf(name) {
  const u = units.find(u => u.name === name);
  return u ? u.emoji : "🚒";
}

/* 건물 생성 */
document.getElementById('buildBtn').addEventListener('click', () => {
  const g = parseInt(document.getElementById('groundFloors').value) || 5;
  const b = parseInt(document.getElementById('basementFloors').value) || 0;
  generateBuilding(Math.min(g, 150), Math.min(b, 20));
});

function generateBuilding(ground, basement) {
  // 배치 초기화
  placedMap = {};
  renderUnitGrid();

  const area = document.getElementById('buildingArea');
  area.innerHTML = '';

  // 옥상
  area.appendChild(makeFloor('RF', '옥상', 'roof'));

  // 지상층 (위에서 아래로)
  for (let f = ground; f >= 1; f--) {
    area.appendChild(makeFloor(`${f}F`, `${f}층`, 'ground'));
  }

  // 지하층
  for (let b = 1; b <= basement; b++) {
    area.appendChild(makeFloor(`B${b}`, `지하${b}`, 'basement'));
  }
}

function makeFloor(id, label, type) {
  const div = document.createElement('div');
  div.className = `floor-zone ${type}`;
  div.dataset.floorId = id;
  div.innerHTML = `
    <div class="floor-label">${label}</div>
    <div class="floor-content" id="fc-${id}"></div>
  `;
  return div;
}

/* 출동대 패널 렌더 */
function renderUnitGrid() {
  const grid = document.getElementById('unitGrid');
  grid.innerHTML = '';
  units.forEach(u => {
    const isPlaced = !!placedMap[u.name];
    const div = document.createElement('div');
    div.className = 'unit-icon' + (isPlaced ? ' placed' : '') + (u.custom ? ' custom' : '');
    div.dataset.unitName = u.name;
    div.innerHTML = `<span class="u-emoji">${u.emoji}</span><span class="u-name">${u.name}</span>`;
    div.style.borderColor = isPlaced ? '#1e293b' : u.color + '66';

    if (!isPlaced) {
      div.addEventListener('pointerdown', onUnitPointerDown);
    }
    grid.appendChild(div);
  });
}

/* 배치 초기화 버튼 */
document.getElementById('resetBtn').addEventListener('click', () => {
  placedMap = {};
  document.querySelectorAll('.floor-content').forEach(fc => fc.innerHTML = '');
  renderUnitGrid();
});

/* 커스텀 출동대 추가 */
document.getElementById('addUnitBtn').addEventListener('click', addCustomUnit);
document.getElementById('customUnitInput').addEventListener('keydown', e => { if (e.key === 'Enter') addCustomUnit(); });

function addCustomUnit() {
  const input = document.getElementById('customUnitInput');
  const name = input.value.trim();
  if (!name) return;
  if (units.find(u => u.name === name)) { input.value = ''; return; }
  const color = UNIT_COLORS[units.length % UNIT_COLORS.length];
  units.push({ name, emoji: '🚒', color, custom: true });
  input.value = '';
  renderUnitGrid();
}

/* ── 드래그 앤 드롭 (pointer events) ── */
const ghost = document.getElementById('dragGhost');
let dragging = null;
let currentHighlight = null;

function onUnitPointerDown(e) {
  if (e.button !== undefined && e.button !== 0) return;
  e.preventDefault();
  const unitName = e.currentTarget.dataset.unitName;
  const unit = units.find(u => u.name === unitName);
  if (!unit) return;

  dragging = unit;

  // 고스트 세팅
  ghost.querySelector('.u-emoji').textContent = unit.emoji;
  ghost.querySelector('.u-name').textContent = unit.name;
  ghost.style.display = 'block';
  moveGhost(e.clientX, e.clientY);

  document.addEventListener('pointermove', onPointerMove);
  document.addEventListener('pointerup', onPointerUp);
  document.addEventListener('pointercancel', onPointerUp);
}

function onPointerMove(e) {
  if (!dragging) return;
  moveGhost(e.clientX, e.clientY);

  // 현재 위치의 floor-zone 하이라이트
  ghost.style.display = 'none';
  const el = document.elementFromPoint(e.clientX, e.clientY);
  ghost.style.display = 'block';
  const fz = el ? el.closest('.floor-zone') : null;

  if (currentHighlight && currentHighlight !== fz) {
    currentHighlight.classList.remove('drag-over');
    currentHighlight = null;
  }
  if (fz && fz !== currentHighlight) {
    fz.classList.add('drag-over');
    currentHighlight = fz;
  }
}

function onPointerUp(e) {
  document.removeEventListener('pointermove', onPointerMove);
  document.removeEventListener('pointerup', onPointerUp);
  document.removeEventListener('pointercancel', onPointerUp);

  if (currentHighlight) {
    currentHighlight.classList.remove('drag-over');
  }
  ghost.style.display = 'none';

  if (!dragging) return;

  // 드롭 위치 찾기
  const el = document.elementFromPoint(e.clientX, e.clientY);
  const fz = el ? el.closest('.floor-zone') : null;

  if (fz) {
    const floorId = fz.dataset.floorId;
    placeUnit(dragging.name, floorId);
  }

  currentHighlight = null;
  dragging = null;
}

function moveGhost(x, y) {
  ghost.style.left = (x - 38) + 'px';
  ghost.style.top  = (y - 44) + 'px';
}

function placeUnit(unitName, floorId) {
  // 기존 배치 제거
  if (placedMap[unitName]) {
    removeChipFromFloor(unitName, placedMap[unitName]);
  }

  placedMap[unitName] = floorId;

  // 칩 추가
  const fc = document.getElementById('fc-' + floorId);
  if (!fc) return;

  const chip = document.createElement('div');
  chip.className = 'placed-chip';
  chip.dataset.unitName = unitName;
  const color = buildingUnitColorOf(unitName);
  const emoji = buildingUnitEmojiOf(unitName);
  chip.style.background = color;
  chip.innerHTML = `${emoji} ${unitName} <span class="remove-x">✕</span>`;
  chip.addEventListener('click', () => {
    removeUnit(unitName);
  });
  fc.appendChild(chip);

  renderUnitGrid();
}

function removeChipFromFloor(unitName, floorId) {
  const fc = document.getElementById('fc-' + floorId);
  if (!fc) return;
  const chip = fc.querySelector(`[data-unit-name="${unitName}"]`);
  if (chip) chip.remove();
}

function removeUnit(unitName) {
  const floorId = placedMap[unitName];
  if (floorId) removeChipFromFloor(unitName, floorId);
  delete placedMap[unitName];
  renderUnitGrid();
}

/* 초기 렌더 */
renderUnitGrid();

// 앱 시작 시 지도 바로 초기화 (첫 탭이 출동예측이므로)
window.addEventListener('load', () => {
  initKakaoMap();
  mapInitialized = true;
});
</script>
</body>
</html>
