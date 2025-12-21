<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Warehouse Dashboard PRO+ Secure</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css">
<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
:root{
  --bg:#020617;
  --card:#111827;
  --accent:#22c55e;
  --danger:#ef4444;
  --warning:#f59e0b;
  --text:#e5e7eb;
  --muted:#94a3b8;
}
*{box-sizing:border-box;margin:0;padding:0;font-family:Inter,sans-serif;}
body{background:#0f172a;color:var(--text);}

/* ===== LOGIN ===== */
#loginContainer{
  display:flex;justify-content:center;align-items:center;width:100%;height:100vh;
}
#loginForm{
  background:var(--card);padding:30px;border-radius:16px;box-shadow:0 6px 25px rgba(0,0,0,.35);width:320px;
}
#loginForm h2{text-align:center;margin-bottom:20px;color:var(--accent);font-size:22px;}
#loginForm input{width:100%;padding:12px;margin-bottom:15px;border-radius:10px;border:1px solid #1f2937;background:#020617;color:white;}
#loginForm button{width:100%;padding:12px;background:var(--accent);border:none;color:#020617;font-weight:600;border-radius:8px;cursor:pointer;}
#loginForm button:hover{background:#16a34a;}
#loginError{color:#ef4444;text-align:center;margin-top:10px;display:none;}

/* ===== DASHBOARD ===== */
.hidden{display:none;}
header{
  display:flex;align-items:center;justify-content:space-between;padding:12px 20px;
  background:#111827;border-bottom:1px solid #1f2937;
}
header h1{color:var(--accent);font-size:22px;font-weight:700;display:flex;align-items:center;gap:10px;}
header h1 i{color:var(--accent)}

.container{padding:20px;display:grid;gap:20px}
.card{background:var(--card);border-radius:16px;padding:18px;box-shadow:0 6px 25px rgba(0,0,0,.35);}
.kpis{display:grid;grid-template-columns:repeat(auto-fit,minmax(120px,1fr));gap:15px;text-align:center;}
.kpi{cursor:pointer;transition:transform .2s ease;}
.kpi:hover{transform:translateY(-2px);}
.kpi-active{outline:2px solid var(--accent);}
.kpi .value{font-size:28px;font-weight:700;}
.success{color:var(--accent);}
.warning{color:var(--warning);}
.danger{color:var(--danger);}
table{width:100%;border-collapse:collapse;font-size:13px;}
th,td{padding:10px;border-bottom:1px solid #1f2937;color:var(--text);}
thead th{background:#111827;position:sticky;top:0;}
#reportBody tr{cursor:pointer;}
#reportBody tr:hover{background:#1e293b;}
.badge{padding:4px 10px;border-radius:999px;font-size:12px;font-weight:600;}
.completed{background:#052e1a;color:#22c55e;}
.pending{background:#3b2a05;color:#f59e0b;}
.delayed{background:#3a0d0d;color:#ef4444;}
button.csv-btn{padding:8px 14px;border-radius:8px;background:var(--accent);border:none;color:#020617;font-weight:600;cursor:pointer;}
button.csv-btn:hover{background:#16a34a;}
.tab-btn{padding:10px 15px;background:#1f2937;color:var(--text);border:none;border-radius:8px;cursor:pointer;font-weight:600;}
.tab-btn:hover{background:#2e3a59;}
.tab-active{background:var(--accent);color:#020617;}

/* Pending Cards */
.pending-card{
  background:var(--card);
  border-radius:16px;
  padding:15px;
  box-shadow:0 6px 20px rgba(0,0,0,0.3);
  display:flex;
  flex-direction:column;
  gap:8px;
}
.pending-card h3{
  font-size:16px;
  font-weight:700;
  margin-bottom:5px;
  color:var(--accent);
}
.pending-card p{
  font-size:13px;
  color:var(--muted);
}
.pending-card .badge{
  align-self:flex-start;
}

@media screen and (max-width:900px){
  .container{grid-template-columns:1fr;}
  table, th, td{font-size:12px;}
  #pendingContainer{grid-template-columns:1fr;}
}
  /* ===== GLOBAL RESPONSIVE FIXES ===== */
html, body {
  width: 100%;
  overflow-x: hidden;
}

/* Prevent tables from breaking layout */
.table-wrapper {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

/* Make charts responsive */
canvas {
  max-width: 100% !important;
  height: auto !important;
}

/* ===== HEADER ===== */
header {
  flex-wrap: wrap;
  gap: 10px;
}

header h1 {
  font-size: 18px;
}

/* ===== KPIs ===== */
.kpis {
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
}

.kpi .value {
  font-size: 22px;
}

/* ===== TABS ===== */
.tab-btn {
  flex: 1;
  font-size: 14px;
  padding: 10px;
}

.card[style*="display:flex"] {
  flex-wrap: wrap;
}

/* ===== TABLES ===== */
table {
  min-width: 600px;
}

th, td {
  white-space: nowrap;
}

/* ===== PENDING CARDS ===== */
#pendingContainer {
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
}

/* ===== LOGIN ===== */
#loginForm {
  width: 90%;
  max-width: 360px;
}

/* ===== MOBILE SMALL ===== */
@media (max-width: 480px) {
  header {
    padding: 10px 12px;
  }

  header h1 {
    font-size: 16px;
  }

  .container {
    padding: 12px;
    gap: 15px;
  }

  .card {
    padding: 14px;
    border-radius: 14px;
  }

  .kpi .value {
    font-size: 20px;
  }

  .tab-btn {
    font-size: 13px;
  }

  table {
    font-size: 12px;
  }

  #pendingPage {
    padding: 12px;
  }

  .pending-card h3 {
    font-size: 14px;
  }
}

/* ===== TABLET ===== */
@media (min-width: 481px) and (max-width: 900px) {
  .container {
    grid-template-columns: 1fr;
  }

  .kpis {
    grid-template-columns: repeat(2, 1fr);
  }
}
  
  
  
  
  
</style>
</head>

<body>

<!-- ===== LOGIN ===== -->
<div id="loginContainer">
  <form id="loginForm">
    <h2><i class="fa-solid fa-warehouse"></i></h2>
    <input type="text" id="username" placeholder="Username" required>
    <input type="password" id="password" placeholder="Password" required>
    <button type="submit">Login</button>
    <p id="loginError">Invalid credentials</p>
  </form>
</div>

<!-- ===== DASHBOARD ===== -->
<div id="dashboard" class="hidden" style="width:100%;position:fixed;top:0;left:0;right:0;bottom:0;overflow:auto;z-index:100;">
<header>
  <h1><i class="fa-solid fa-warehouse"></i> My Holdal</h1>
</header>

<div class="container">
  <!-- KPIs -->
  <section class="kpis">
    <div class="card kpi kpi-click kpi-active" id="kpi-total">
      <div>Total</div>
      <div class="value" id="total">0</div>
    </div>
    <div class="card kpi kpi-click" id="kpi-completed">
      <div>Completed</div>
      <div class="value success" id="completed">0</div>
    </div>
    <div class="card kpi kpi-click" id="kpi-pending">
      <div>Pending</div>
      <div class="value warning" id="pending">0</div>
    </div>
    <div class="card kpi kpi-click" id="kpi-delayed">
      <div>Delayed</div>
      <div class="value danger" id="delayed">0</div>
    </div>
  </section>

  <!-- TABS -->
  <div class="card" style="display:flex;gap:10px;">
    <button class="tab-btn tab-active" onclick="showTab('reports', event)"><i class="fa-solid fa-chart-line"></i> Reports</button>
    <button class="tab-btn" onclick="showTab('warehouses', event)"><i class="fa-solid fa-warehouse"></i> Warehouses</button>
  </div>

  <!-- REPORTS -->
  <div id="tab-reports">
    <section class="card">
      <h2 style="margin-bottom:15px;color:var(--accent)"><i class="fa-solid fa-chart-bar"></i> Reports Summary</h2>
      <table>
        <thead>
          <tr>
            <th>Status</th>
            <th>Count</th>
            <th>Percentage</th>
          </tr>
        </thead>
        <tbody id="reportBody"></tbody>
      </table>
      <canvas id="reportChart" style="margin-top:20px;max-height:300px;"></canvas>
    </section>
  </div>

  <!-- WAREHOUSES -->
  <div id="tab-warehouses" class="hidden">
    <section class="card">
      <h2 style="margin-bottom:15px;color:var(--accent)"><i class="fa-solid fa-warehouse"></i> Warehouses Overview</h2>
      <table>
        <thead>
          <tr>
            <th>Warehouse</th>
            <th>Total Orders</th>
            <th>Completed</th>
            <th>Pending</th>
            <th>Delayed</th>
          </tr>
        </thead>
        <tbody id="warehouseBody"></tbody>
      </table>
      <canvas id="warehouseChart" style="margin-top:20px;max-height:300px;"></canvas>
    </section>
  </div>
</div>
</div>

<!-- PENDING ORDERS PAGE -->
<div id="pendingPage" class="hidden" style="width:100%;position:fixed;top:0;left:0;right:0;bottom:0;overflow:auto;z-index:101;background:#0f172a;padding:20px;">
  <button onclick="closePending()" style="margin-bottom:15px;padding:10px 15px;border:none;border-radius:8px;background:var(--accent);color:#020617;font-weight:600;cursor:pointer;">
    ◀ Back to Dashboard
  </button>
  <h2 style="margin-bottom:20px;color:var(--accent)"><i class="fa-solid fa-hourglass-half"></i> Pending Orders</h2>
  <input class="search" placeholder="🔍 Search Pending Orders" id="pendingSearch" style="margin-bottom:15px;width:100%;max-width:400px;">
  <div id="pendingContainer" style="display:grid;grid-template-columns:repeat(auto-fit,minmax(250px,1fr));gap:15px;"></div>
</div>

<script>
// ===== LOGIN =====
const loginForm = document.getElementById('loginForm');
const loginContainer = document.getElementById('loginContainer');
const dashboard = document.getElementById('dashboard');
const validUser = {username:'admin', password:'123456'};

// ===== DATA =====
const sheetURL="https://docs.google.com/spreadsheets/d/e/2PACX-1vSmwFwL90bL8_I0L_0AHfi7ZKVqf6r2s1LGcTHViKLIqzLmddf3FnxliUd_84ARJA/pub?gid=754336810&single=true&output=csv";
let allOrders=[], reportChart, warehouseChart;

// ===== LOGIN FUNCTION =====
loginForm.addEventListener('submit', e=>{
  e.preventDefault();
  const user = document.getElementById('username').value;
  const pass = document.getElementById('password').value;
  if(user===validUser.username && pass===validUser.password){
    loginContainer.remove();
    dashboard.classList.remove('hidden');
    loadData();
  } else {
    document.getElementById('loginError').style.display='block';
  }
});

// ===== LOAD DATA =====
function loadData(){
  fetch(sheetURL).then(r=>r.text()).then(csv=>{
    const data = Papa.parse(csv,{header:true,skipEmptyLines:true}).data;
    allOrders = data.map(r=>{
      const orderCol = Object.keys(r).find(k=>k.startsWith("#M"));
      const orderName = r[orderCol]?.trim() || "-";
      const s = r["start packing time"]?.trim();
      const e = r["end packing time"]?.trim();
      const receiveName = r["Baseline WH"]?.trim() || "-";
      const warehouseName = r["Warehouse Name"]?.trim() || "-";
      const mins = (s && e) ? (+e.split(":")[0]*60 + +e.split(":")[1]) - (+s.split(":")[0]*60 + +s.split(":")[1]) : null;
      let status = "pending";
      if(mins !== null) status = mins > 60 ? "delayed" : "completed";
      return {order: orderName, receive: receiveName, start: s||"-", end: e||"-", mins: mins!==null?mins:"-", status, warehouse: warehouseName};
    });
    updateDashboard();
  });
}

// ===== TABS =====
function showTab(tab, event){
  document.querySelectorAll('.tab-btn').forEach(btn=>btn.classList.remove('tab-active'));
  event.currentTarget.classList.add('tab-active');
  document.getElementById('tab-reports').classList.add('hidden');
  document.getElementById('tab-warehouses').classList.add('hidden');
  document.getElementById('tab-'+tab).classList.remove('hidden');
}

// ===== UPDATE DASHBOARD =====
function updateDashboard(){
  updateReports();
  updateWarehouses();
  updateCharts();
}

// ===== REPORTS =====
function updateReports(){
  const total = allOrders.length;
  const completed = allOrders.filter(o=>o.status==='completed').length;
  const pending = allOrders.filter(o=>o.status==='pending').length;
  const delayed = allOrders.filter(o=>o.status==='delayed').length;

  document.getElementById('total').textContent = total;
  document.getElementById('completed').textContent = completed;
  document.getElementById('pending').textContent = pending;
  document.getElementById('delayed').textContent = delayed;

  document.getElementById('reportBody').innerHTML = `
    <tr onclick="showPendingOrders()"><td>Completed</td><td>${completed}</td><td>${((completed/total)*100).toFixed(1)}%</td></tr>
    <tr onclick="showPendingOrders()"><td>Pending</td><td>${pending}</td><td>${((pending/total)*100).toFixed(1)}%</td></tr>
    <tr onclick="showPendingOrders()"><td>Delayed</td><td>${delayed}</td><td>${((delayed/total)*100).toFixed(1)}%</td></tr>
  `;
}

// ===== WAREHOUSES =====
function updateWarehouses(){
  const warehouseMap = {};
  allOrders.forEach(o=>{
    if(!warehouseMap[o.warehouse]) warehouseMap[o.warehouse]={total:0,completed:0,pending:0,delayed:0};
    warehouseMap[o.warehouse].total++;
    warehouseMap[o.warehouse][o.status]++;
  });

  const fragment = document.createDocumentFragment();
  Object.keys(warehouseMap).forEach(w=>{
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td>${w}</td>
      <td>${warehouseMap[w].total}</td>
      <td>${warehouseMap[w].completed}</td>
      <td style="cursor:pointer;color:var(--warning);" onclick="showPendingOrders('${w}')">${warehouseMap[w].pending}</td>
      <td>${warehouseMap[w].delayed}</td>
    `;
    fragment.appendChild(tr);
  });
  const tbody = document.getElementById('warehouseBody');
  tbody.innerHTML = '';
  tbody.appendChild(fragment);
}

// ===== PENDING ORDERS PAGE =====
function showPendingOrders(warehouse=null){
  document.getElementById('dashboard').classList.add('hidden');
  const pendingPage = document.getElementById('pendingPage');
  pendingPage.classList.remove('hidden');

  let pendingOrders = allOrders.filter(o=>o.status==="pending");
  if(warehouse) pendingOrders = pendingOrders.filter(o=>o.warehouse === warehouse);

  const container = document.getElementById('pendingContainer');

  container.innerHTML = pendingOrders.map(o=>`
    <div class="pending-card">
      <h3>${o.order}</h3>
      <p><strong>Receiving:</strong> ${o.receive}</p>
      <p><strong>Warehouse:</strong> ${o.warehouse}</p>
      <p><strong>Start:</strong> ${o.start} | <strong>End:</strong> ${o.end}</p>
      <p><strong>Minutes:</strong> ${o.mins}</p>
      <span class="badge pending">Pending</span>
    </div>
  `).join('');
}

// ===== SEARCH IN PENDING =====
document.getElementById('pendingSearch').addEventListener('input', e=>{
  const text = e.target.value.toLowerCase();
  const container = document.getElementById('pendingContainer');
  let pendingOrders = allOrders.filter(o=>o.status==="pending");
  pendingOrders = pendingOrders.filter(o=>o.order.toLowerCase().includes(text) || o.warehouse.toLowerCase().includes(text));
  container.innerHTML = pendingOrders.map(o=>`
    <div class="pending-card">
      <h3>${o.order}</h3>
      <p><strong>Receiving:</strong> ${o.receive}</p>
      <p><strong>Warehouse:</strong> ${o.warehouse}</p>
      <p><strong>Start:</strong> ${o.start} | <strong>End:</strong> ${o.end}</p>
      <p><strong>Minutes:</strong> ${o.mins}</p>
      <span class="badge pending">Pending</span>
    </div>
  `).join('');j
});

// ===== CLOSE PENDING PAGE =====
function closePending(){
  document.getElementById('pendingPage').classList.add('hidden');
  document.getElementById('dashboard').classList.remove('hidden');
}

// ===== CHARTS =====
function updateCharts(){
  const completed = allOrders.filter(o=>o.status==='completed').length;
  const pending = allOrders.filter(o=>o.status==='pending').length;
  const delayed = allOrders.filter(o=>o.status==='delayed').length;

  // Report Chart
  const reportData = {labels:['Completed','Pending','Delayed'],datasets:[{label:'Orders Count',data:[completed,pending,delayed],backgroundColor:['#22c55e','#f59e0b','#ef4444']}]};

  if(reportChart) reportChart.destroy();
  const ctxReport = document.getElementById('reportChart').getContext('2d');
  reportChart = new Chart(ctxReport,{
    type:'doughnut',
    data: reportData,
    options:{
      plugins:{
        legend:{position:'bottom', labels:{color:'#e5e7eb'}},
        tooltip:{enabled:true}
      },
      onClick: function(evt, elements){
        if(elements.length>0){
          const index = elements[0].index;
          const status = reportData.labels[index].toLowerCase();
          let pendingOrders = allOrders.filter(o=>o.status===status);
          if(status==='pending') showPendingOrders();
        }
      }
    }
  });

  // Warehouse Chart
  const warehouseMap = {};
  allOrders.forEach(o=>{
    if(!warehouseMap[o.warehouse]) warehouseMap[o.warehouse]=0;
    warehouseMap[o.warehouse]++;
  });

  const warehouseData = {
    labels: Object.keys(warehouseMap),
    datasets: [{
      label:'Total Orders',
      data: Object.values(warehouseMap),
      backgroundColor: Object.keys(warehouseMap).map(()=>`#${Math.floor(Math.random()*16777215).toString(16)}`)
    }]
  };

  if(warehouseChart) warehouseChart.destroy();
  const ctxWarehouse = document.getElementById('warehouseChart').getContext('2d');
  warehouseChart = new Chart(ctxWarehouse,{
    type:'bar',
    data: warehouseData,
    options:{
      plugins:{legend:{display:false}},
      scales:{y:{beginAtZero:true,ticks:{color:'#e5e7eb'}}, x:{ticks:{color:'#e5e7eb'}}},
      onClick: function(evt, elements){
        if(elements.length>0){
          const index = elements[0].index;
          const warehouse = warehouseData.labels[index];
          showPendingOrders(warehouse);
        }
      }
    }
  });
}
</script>

</body>
</html>
