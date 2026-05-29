<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Black Brix Maps – Goa Maritime</title>
<style>
* { margin:0; padding:0; box-sizing:border-box; }
body { font-family:'Georgia',serif; background:#111; color:#e8dcc8; height:100vh; display:flex; flex-direction:column; }

header {
  padding:10px 18px; background:#111; border-bottom:1px solid #2a2a2a;
  display:flex; align-items:center; justify-content:space-between; flex-wrap:wrap; gap:6px;
}
.brand { display:flex; align-items:center; gap:10px; }
.brand-icon { display:grid; grid-template-columns:repeat(3,8px); grid-template-rows:repeat(2,8px); gap:2px; }
.brick { background:#c0392b; border-radius:1px; }
.brick.light { background:#e74c3c; }
.brand-name { font-size:1.05rem; font-weight:bold; letter-spacing:0.12em; text-transform:uppercase; color:#fff; }
.brand-sub  { font-size:0.62rem; letter-spacing:0.2em; color:#888; text-transform:uppercase; }
header .hint { font-size:0.65rem; color:#555; }

#map { flex:1; width:100%; }

#legend-panel {
  position:absolute; bottom:28px; left:14px; z-index:10;
  background:rgba(17,17,17,0.94); border:1px solid #2a2a2a;
  border-radius:7px; padding:10px 14px; width:230px; font-size:0.72rem;
}
.legend-section { margin-bottom:7px; }
.legend-section-title { color:#888; font-size:0.62rem; letter-spacing:0.1em; text-transform:uppercase; border-bottom:1px solid #222; padding-bottom:3px; margin-bottom:5px; }
.legend-row { display:flex; align-items:center; justify-content:space-between; margin-bottom:4px; }
.legend-left { display:flex; align-items:center; gap:7px; }
.sym { width:14px; height:14px; display:flex; align-items:center; justify-content:center; flex-shrink:0; }
.sym svg { width:14px; height:14px; }
.tog { position:relative; width:28px; height:15px; flex-shrink:0; }
.tog input { opacity:0; width:0; height:0; }
.tog-slider { position:absolute; inset:0; background:#333; border-radius:15px; cursor:pointer; transition:background 0.2s; }
.tog-slider:before { content:""; position:absolute; width:11px; height:11px; left:2px; bottom:2px; background:#fff; border-radius:50%; transition:transform 0.2s; }
.tog input:checked + .tog-slider { background:#4caf50; }
.tog input:checked + .tog-slider:before { transform:translateX(13px); }
.sub-legend { margin-left:20px; margin-top:2px; margin-bottom:4px; }
.sub-row { display:flex; align-items:center; gap:6px; margin-bottom:2px; font-size:0.67rem; color:#aaa; }
.sub-dot { width:9px; height:9px; border-radius:2px; flex-shrink:0; }

#info-panel {
  position:absolute; top:62px; right:14px; z-index:10;
  background:rgba(17,17,17,0.96); border:1px solid #2a2a2a;
  border-radius:7px; padding:16px 20px; width:310px;
  font-size:0.99rem; display:none;
}
.info-tag  { font-size:0.82rem; text-transform:uppercase; letter-spacing:0.09em; margin-bottom:5px; }
.info-name { font-size:1.14rem; color:#fff; margin-bottom:8px; line-height:1.35; }
.info-row  { color:#666; font-size:0.88rem; margin-top:3px; }
.info-row span { color:#bbb; }
.info-close { float:right; cursor:pointer; color:#555; font-size:0.8rem; }
.info-close:hover { color:#fff; }

#loading {
  position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);
  background:rgba(17,17,17,0.95); border:1px solid #2a2a2a;
  border-radius:8px; padding:20px 30px; text-align:center; z-index:20;
  font-size:0.85rem; color:#aaa;
}
</style>
</head>
<body>

<header>
  <div class="brand"><img src="data:image/png;base64,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" alt="Black Brix" style="height:40px;object-fit:contain;"></div>
    <div>
      <div class="brand-name">Black Brix<sup style="font-size:0.5em">®</sup> Maps</div>
      <div class="brand-sub">Goa Maritime Infrastructure</div>
    </div>
  </div>
  <div class="hint">Click any marker for details</div>
</header>

<div id="map"></div>
<div id="loading">Loading data from Masterlist…</div>

<!-- LEGEND -->
<div id="legend-panel">
  <div class="legend-section">
    <div class="legend-section-title">Navigation</div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 13,13 1,13" fill="#f0c060" stroke="#fff" stroke-width="1"/></svg></div><span>Lighthouse</span></div>
      <label class="tog"><input type="checkbox" checked onchange="toggleLayer('LightHouse',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><circle cx="7" cy="7" r="5" fill="#4fc3f7" stroke="#fff" stroke-width="1"/></svg></div><span>Beacon</span></div>
      <label class="tog"><input type="checkbox" checked onchange="toggleLayer('Beacon',this.checked)"><span class="tog-slider"></span></label>
    </div>
  </div>

  <div class="legend-section">
    <div class="legend-section-title">Jetties</div>

    <!-- Govt. Jetties header with master toggle -->
    <div class="legend-row" style="margin-bottom:3px;">
      <span style="color:#ccc;font-size:0.7rem;font-weight:bold;">Govt. Jetties</span>
      <label class="tog"><input type="checkbox" checked onchange="toggleGroup('govt',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 13,7 7,13 1,7" fill="#06b6d4" stroke="#fff" stroke-width="1"/></svg></div><span style="font-size:0.68rem;">CCP Smart City</span></div>
      <label class="tog"><input type="checkbox" class="tog-govt" checked onchange="toggleLayer('Jetty__CCP Smart City',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 13,7 7,13 1,7" fill="#22c55e" stroke="#fff" stroke-width="1"/></svg></div><span style="font-size:0.68rem;">Fisheries Dept.</span></div>
      <label class="tog"><input type="checkbox" class="tog-govt" checked onchange="toggleLayer('Jetty__Fisheries Dept.',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 13,7 7,13 1,7" fill="#f59e0b" stroke="#fff" stroke-width="1"/></svg></div><span style="font-size:0.68rem;">River Navigation Dept.</span></div>
      <label class="tog"><input type="checkbox" class="tog-govt" checked onchange="toggleLayer('Jetty__River Navigation Dept.',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 13,7 7,13 1,7" fill="#ec4899" stroke="#fff" stroke-width="1"/></svg></div><span style="font-size:0.6
8rem;">Tourism Dept.</span></div>
      <label class="tog"><input type="checkbox" class="tog-govt" checked onchange="toggleLayer('Jetty__Tourism Dept.',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row" style="margin-bottom:6px;">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 13,7 7,13 1,7" fill="#818cf8" stroke="#fff" stroke-width="1"/></svg></div><span style="font-size:0.68rem;">Captain of Ports Dept.</span></div>
      <label class="tog"><input type="checkbox" class="tog-govt" checked onchange="toggleLayer('Jetty__Captain of Ports Dept.',this.checked)"><span class="tog-slider"></span></label>
    </div>

    <!-- Private Jetties -->
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 13,7 7,13 1,7" fill="#e05c7a" stroke="#fff" stroke-width="1"/></svg></div><span style="font-size:0.68rem;">Private Jetties</span></div>
      <label class="tog"><input type="checkbox" checked onchange="toggleLayer('Jetty__Private',this.checked)"><span class="tog-slider"></span></label>
    </div>
  </div>

  <div class="legend-section">
    <div class="legend-section-title">Ferry & Shipyards</div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><rect x="2" y="2" width="10" height="10" fill="#a78bfa" stroke="#fff" stroke-width="1"/></svg></div><span>Ferry Ramp</span></div>
      <label class="tog"><input type="checkbox" checked onchange="toggleLayer('FerryRamp',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 9,5 13,6 10,10 11,14 7,12 3,14 4,10 1,6 5,5" fill="#fb923c" stroke="#fff" stroke-width="1"/></svg></div><span>Drydock</span></div>
      <label class="tog"><input type="checkbox" checked onchange="toggleLayer('Drydock',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 9,5 13,6 10,10 11,14 7,12 3,14 4,10 1,6 5,5" fill="#34d399" stroke="#fff" stroke-width="1"/></svg></div><span>Shipyard</span></div>
      <label class="tog"><input type="checkbox" checked onchange="toggleLayer('Shipyard',this.checked)"><span class="tog-slider"></span></label>
    </div>
    <div class="legend-row">
      <div class="legend-left"><div class="sym"><svg viewBox="0 0 14 14"><polygon points="7,1 9,5 13,6 10,10 11,14 7,12 3,14 4,10 1,6 5,5" fill="#cbd5e1" stroke="#fff" stroke-width="1"/></svg></div><span>Workshop</span></div>
      <label class="tog"><input type="checkbox" checked onchange="toggleLayer('Workshop',this.checked)"><span class="tog-slider"></span></label>
    </div>
  </div>

  <div class="legend-section">
    <div class="legend-section-title">Labels</div>
    <div class="legend-row">
      <span style="color:#aaa;">Taluka Names</span>
      <label class="tog"><input type="checkbox" checked onchange="toggleLayer('talukas',this.checked)"><span class="tog-slider"></span></label>
    </div>
  </div>
</div>

<!-- INFO PANEL -->
<div id="info-panel">
  <span class="info-close" onclick="document.getElementById('info-panel').style.display='none'">✕</span>
  <div class="info-tag"  id="i-tag"></div>
  <div class="info-name" id="i-name"></div>
  <div class="info-row"  id="i-r1"></div>
  <div class="info-row"  id="i-r2"></div>
  <div class="info-row"  id="i-r3"></div>
  <div class="info-row"  id="i-r4"></div>
</div>


<script>

/* ============================================================
   ★ STEP 1 — PASTE YOUR GOOGLE SHEET CSV URL HERE
   ============================================================
   Find it in your Google Sheets URL:
   docs.google.com/spreadsheets/d/ <<<COPY THIS PART>>> /edit

   Also make sure your sheet is shared:
   Share → Anyone with the link → Viewer
   ============================================================ */

const SHEET_ID = "1lWb0JHyGKG8e38Pj8tydeUtQRhoWMlsxKzVDBlANzso";


/* ============================================================
   STEP 2 — MAP SETTINGS
   ============================================================ */

const MAP_CENTER = { lat:15.4, lng:74.05 };
const MAP_ZOOM   = 10;
const MAP_ID     = "b00a3c1a6fd65b6bf10ea022";  // ← Your Cloud Console Map ID

const MAP_STYLE = [
  { elementType:"labels",         stylers:[{visibility:"off"}] },
  { featureType:"road",           stylers:[{visibility:"off"}] },
  { featureType:"transit",        stylers:[{visibility:"off"}] },
  { featureType:"poi",            stylers:[{visibility:"off"}] },
  { featureType:"administrative", stylers:[{visibility:"off"}] },
  { featureType:"water",     elementType:"geometry", stylers:[{color:"#4d8285"}] },  /* ← WATER COLOR  */
  { featureType:"landscape", elementType:"geometry", stylers:[{color:"#fbfbfb"}] },  /* ← LAND COLOR   */
  { featureType:"landscape.natural.landcover", elementType:"geometry", stylers:[{color:"#fbfbfb"}] },
];


/* ============================================================
   STEP 3 — MARKER STYLES
   Maps_Type + Maps_Sub Type from your sheet → picks style below
   To change a color/size, edit the values here.
   ============================================================ */

const SHAPES = {
  triangle : "M 0,-11 L 10,8 L -10,8 Z",
  diamond  : "M 0,-10 L 9,0 L 0,10 L -9,0 Z",
  square   : "M -7,-7 L 7,-7 L 7,7 L -7,7 Z",
  star     : "M 0,-11 L 3,-4 L 10,-4 L 5,1 L 7,9 L 0,5 L -7,9 L -5,1 L -10,-4 L -3,-4 Z",
};

/* Maps_Type value (lowercase) → style */
const JETTY_TYPES = {
  "ccp smart city"          : "#06b6d4",
  "fisheries dept."         : "#22c55e",
  "river navigation dept."  : "#f59e0b",
  "tourism dept."           : "#ec4899",
  "government"              : "#94a3b8",
  "captain of ports dept."  : "#818cf8",
  "private"                 : "#e05c7a",
};

const TYPE_STYLES = {
  "lighthouse"  : { shape:"triangle", color:"#f0c060", border:"#fff" },
  "beacon"      : { shape:"circle",   color:"#4fc3f7", border:"#fff" },
  "ferry ramp"  : { shape:"square",   color:"#a78bfa", border:"#fff" },
  "ferryramp"   : { shape:"square",   color:"#a78bfa", border:"#fff" },
  "drydock"     : { shape:"star",     color:"#fb923c", border:"#fff" },
  "shipyard"    : { shape:"star",     color:"#34d399", border:"#fff" },
  "workshop"    : { shape:"star",     color:"#cbd5e1", border:"#fff" },
};

/* Layer key per Maps_Type */
function layerKey(type) {
  const t = type.toLowerCase().trim();
  /* Jetty types — matched directly from Maps_Type */
  if (JETTY_TYPES[t] !== undefined) return "Jetty__" + type.trim();
  const map = {
    "lighthouse":"LightHouse", "beacon":"Beacon",
    "ferry ramp":"FerryRamp",  "ferryramp":"FerryRamp",
    "drydock":"Drydock",       "shipyard":"Shipyard",
    "workshop":"Workshop",
  };
  return map[t] || t;
}

function jettyColor(type) {
  return JETTY_TYPES[type.toLowerCase().trim()] || "#94a3b8";
}

/* Tag color for info panel */
const TAG_COLORS = {
  "LightHouse":"#f0c060", "Beacon":"#4fc3f7",
  "Jetty_Govt":"#38bdf8", "Jetty_Private":"#e05c7a",
  "FerryRamp":"#a78bfa",  "Drydock":"#fb923c",
  "Shipyard":"#34d399",   "Workshop":"#cbd5e1",
};


/* ============================================================
   STEP 4 — TALUKA LABEL POSITIONS
   Adjust lat/lng to move a label. Toggle in legend.
   ============================================================ */

const TALUKAS = [
  { name:"Tiswadi",     lat:15.498, lng:73.870 },
  { name:"Bardez",      lat:15.590, lng:73.820 },
  { name:"Pernem",      lat:15.720, lng:73.790 },
  { name:"Bicholim",    lat:15.590, lng:73.985 },
  { name:"Sattari",     lat:15.648, lng:74.120 },
  { name:"Ponda",       lat:15.395, lng:74.030 },
  { name:"Mormugao",    lat:15.408, lng:73.815 },
  { name:"Salcete",     lat:15.255, lng:73.980 },
  { name:"Quepem",      lat:15.210, lng:74.090 },
  { name:"Sanguem",     lat:15.220, lng:74.240 },
  { name:"Canacona",    lat:15.010, lng:74.060 },
  { name:"Dharbandora", lat:15.360, lng:74.270 },
];


/* ============================================================
   STEP 5 — BOUNDARY COLORS
   ============================================================ */

const BORDERS = {
  goaStroke    : "#c0392b", goaWeight    : 2.5,   /* ← Goa border color    */
  indiaStroke  : "#999999", indiaWeight  : 1,      /* ← State border color  */
  talukaStroke : "#555555", talukaWeight : 0.8,    /* ← Taluka border color */
  talukaText   : "#888888", talukaFont   : "11px", /* ← Taluka label color  */
};


/* ============================================================
   MAP ENGINE — DO NOT EDIT BELOW THIS LINE
   ============================================================ */

const layers = {};
let map;

function toggleLayer(key, show) {
  (layers[key] || []).forEach(m => m.setVisible(show));
  if (key === "talukas" && layers["_talukaLayer"])
    layers["_talukaLayer"].setMap(show ? map : null);
}

function pushLayer(key, marker) {
  if (!layers[key]) layers[key] = [];
  layers[key].push(marker);
}

function buildIcon(type) {
  const t = type.toLowerCase().trim();
  if (JETTY_TYPES[t] !== undefined) {
    return { path:SHAPES["diamond"], scale:1, fillColor:JETTY_TYPES[t], fillOpacity:1, strokeColor:"#fff", strokeWeight:1.5 };
  }
  const style = TYPE_STYLES[t];
  if (!style) return { path:google.maps.SymbolPath.CIRCLE, scale:5, fillColor:"#aaa", fillOpacity:1, strokeColor:"#fff", strokeWeight:1 };
  if (style.shape === "circle") {
    return { path:google.maps.SymbolPath.CIRCLE, scale:6, fillColor:style.color, fillOpacity:1, strokeColor:style.border, strokeWeight:1.5 };
  }
  return { path:SHAPES[style.shape], scale:1, fillColor:style.color, fillOpacity:1, strokeColor:style.border, strokeWeight:1.5 };
}

function showInfo(lk, name, row) {
  const label = lk.startsWith("Jetty__") ? lk.replace("Jetty__","") : lk.replace(/_/g," ");
  const color = lk.startsWith("Jetty__")
    ? (JETTY_TYPES[lk.replace("Jetty__","").toLowerCase().trim()] || "#94a3b8")
    : (TAG_COLORS[lk] || "#fff");
  document.getElementById("i-tag").textContent  = label;
  document.getElementById("i-tag").style.color  = color;
  document.getElementById("i-name").textContent = name;
  const ids = ["i-r1","i-r2","i-r3","i-r4"];
  const filled = row.filter(Boolean);
  ids.forEach((id,i) => {
    const el = document.getElementById(id);
    el.innerHTML = filled[i] ? `${filled[i][0]}: <span>${filled[i][1]}</span>` : "";
  });
  document.getElementById("info-panel").style.display = "block";
}

/* Parse CSV text → array of objects using header row */
function parseCSV(text) {
  const lines = text.trim().split("\n").filter(l => l.trim());
  const headers = lines[0].split(",").map(h => h.trim().replace(/^"|"$/g,""));
  return lines.slice(1).map(line => {
    const vals = line.match(/(".*?"|[^,]+|(?<=,)(?=,)|^(?=,)|(?<=,)$)/g) || [];
    const obj = {};
    headers.forEach((h,i) => { obj[h] = (vals[i] || "").replace(/^"|"$/g,"").trim(); });
    return obj;
  });
}

/* Toggle all govt jetty sub-layers at once */
function toggleGroup(group, show) {
  if (group === 'govt') {
    document.querySelectorAll('.tog-govt').forEach(cb => { cb.checked = show; });
    Object.keys(layers).filter(k => k.startsWith("Jetty__") && k !== "Jetty__Private")
      .forEach(k => toggleLayer(k, show));
  }
}

function buildJettyLegend() { /* static */ }

function plotMarkers(rows) {
  rows.forEach(r => {
    const lat  = parseFloat(r["Maps_Lat"]);
    const lng  = parseFloat(r["Maps_Long"]);
    if (isNaN(lat) || isNaN(lng)) return;

    const type = r["Maps_Type"] || "";
    const name = r["Maps_Name"] || "—";
    const lk   = layerKey(type);

    const marker = new google.maps.Marker({
      position: { lat, lng }, map,
      icon: buildIcon(type),
      zIndex: 5,
    });

    marker.addListener("click", () => showInfo(lk, name, [
      r["Maps_River"]    && r["Maps_River"]    !== "—" ? ["River",    r["Maps_River"]]    : null,
      r["Maps_Location"] && r["Maps_Location"] !== "—" ? ["Location", r["Maps_Location"]] : null,
      ["Coords", `${lat}, ${lng}`],
    ]));

    pushLayer(lk, marker);
  });
}

function loadBoundaries() {
  /* India states */
  const indiaLayer = new google.maps.Data();
  indiaLayer.loadGeoJson("https://raw.githubusercontent.com/geohacker/india/master/state/india_state.geojson");
  indiaLayer.setStyle({ strokeColor:BORDERS.indiaStroke, strokeWeight:BORDERS.indiaWeight, fillOpacity:0 });
  indiaLayer.setMap(map);

  /* Goa state */
  const goaLayer = new google.maps.Data();
  goaLayer.loadGeoJson("https://raw.githubusercontent.com/datameet/maps/master/States/goa.geojson");
  goaLayer.setStyle({ strokeColor:BORDERS.goaStroke, strokeWeight:BORDERS.goaWeight, fillOpacity:0 });
  goaLayer.setMap(map);

  /* Goa talukas boundary */
  const talukaLayer = new google.maps.Data();
  talukaLayer.loadGeoJson("https://raw.githubusercontent.com/datameet/maps/master/Talukas/goa.geojson");
  talukaLayer.setStyle({ strokeColor:BORDERS.talukaStroke, strokeWeight:BORDERS.talukaWeight, fillOpacity:0 });
  talukaLayer.setMap(map);
  layers["_talukaLayer"] = talukaLayer;
}

function addTalukaLabels() {
  TALUKAS.forEach(t => {
    const m = new google.maps.Marker({
      position: { lat:t.lat, lng:t.lng }, map,
      icon:  { path:google.maps.SymbolPath.CIRCLE, scale:0 },
      label: { text:t.name, color:BORDERS.talukaText, fontSize:BORDERS.talukaFont, fontWeight:"bold", fontFamily:"Cambria,Georgia,serif" },
      zIndex:2,
    });
    pushLayer("talukas", m);
  });
}

function initMap() {
  map = new google.maps.Map(document.getElementById("map"), {
    center:MAP_CENTER, zoom:MAP_ZOOM, mapId:MAP_ID,
    mapTypeControl:false, streetViewControl:false, fullscreenControl:true,
  });

  loadBoundaries();
  addTalukaLabels();

  /* Load Masterlist via Google Visualization API (works from local files) */
  window._sheetCallback = function(data) {
    try {
      const cols = data.table.cols.map(c => c.label);
      const rows = data.table.rows.map(r =>
        Object.fromEntries(cols.map((h,i) => [h, r.c[i] && r.c[i].v != null ? String(r.c[i].v) : ""]))
      );
      document.getElementById("loading").style.display = "none";
      plotMarkers(rows);
    } catch(e) {
      document.getElementById("loading").innerHTML = "⚠️ Could not parse sheet data.<br><small>" + e.message + "</small>";
    }
  };

  /* Google Viz wraps response in this — intercept it */
  window.google = window.google || {};
  window.google.visualization = window.google.visualization || {};
  window.google.visualization.Query = { setResponse: window._sheetCallback };

  const sheetURL = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?tqx=out:json&sheet=Masterlist&callback=_sheetCallback`;

  /* Show the URL so you can test it directly in browser */
  document.getElementById("loading").innerHTML =
    `Loading Masterlist…<br><small style="word-break:break-all;color:#aaa;">
    <a href="${sheetURL.replace('&callback=_sheetCallback','')}" target="_blank" style="color:#4fc3f7;">
    Test sheet URL ↗</a></small>`;

  const s = document.createElement("script");
  s.onerror = () => {
    document.getElementById("loading").innerHTML =
      "⚠️ Script load failed.<br><small>Check Sheet ID or sharing settings.</small>";
  };
  s.src = sheetURL;
  document.head.appendChild(s);

  /* Timeout — if no response in 8 seconds, show error */
  setTimeout(() => {
    if (document.getElementById("loading").style.display !== "none") {
      document.getElementById("loading").innerHTML =
        `⚠️ Timed out loading sheet.<br>
        <small>1. Check Sheet ID is correct<br>
        2. Sharing = Anyone with link (Viewer)<br>
        3. <a href="${sheetURL.replace('&callback=_sheetCallback','')}" target="_blank" style="color:#4fc3f7;">Click to test URL directly ↗</a></small>`;
    }
  }, 8000);

  map.addListener("click", () => {
    document.getElementById("info-panel").style.display = "none";
  });
}

</script>

<!-- ============================================================
     YOUR GOOGLE MAPS API KEY — replace YOUR_API_KEY below
     ============================================================ -->
<script async defer
  src="https://maps.googleapis.com/maps/api/js?key=AIzaSyD8LvbKjHe5a3zJ24WTyyuAK-aoZr0OAZ0&callback=initMap">
</script>

</body>
</html>
