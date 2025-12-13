<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8" />
<title>Dashboard المرتجعات Online</title>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>

<style>
body {font-family: sans-serif; background:#f4f4f4; padding:20px;}
.container {max-width:900px; margin:auto; background:#fff; padding:20px; border-radius:12px;}
.card {background:#eef; padding:15px; border-radius:10px; margin-bottom:15px;}
</style>
</head>
<body>

<div class="container">
<h2>📦 Dashboard المرتجعات (Online)</h2>

<div class="card">
<h3>📊 الإحصائيات العامة</h3>
<p>إجمالي المرتجعات: <span id="totalReturns">0</span></p>
<p>عدد الفواتير: <span id="invoiceCount">0</span></p>
<p>أكثر نوع مرتجع تكراراً: <span id="topItem">-</span></p>
</div>

<div class="card">
<h3>📈 المرتجعات حسب نوع المرتجع</h3>
<canvas id="typeChart"></canvas>
</div>

<div class="card">
<h3>📅 الفواتير اليومية</h3>
<canvas id="dailyChart"></canvas>
</div>

<div class="card">
<h3>👤 المرتجعات حسب الزبون</h3>
<canvas id="clientChart"></canvas>
</div>

</div>

<script>
// 🔴 ضع رابط Google Sheets CSV هنا
const SHEET_URL = "PUT_YOUR_GOOGLE_SHEET_CSV_LINK_HERE";

let excelData = [];
let returnTypeColumn = "";

// تحميل البيانات من Google Sheets
function loadData(){
    Papa.parse(SHEET_URL, {
        download: true,
        header: true,
        complete: function(result){
            excelData = result.data.filter(r => Object.keys(r).length > 1);
            detectReturnTypeColumn();
            generateStats();
            generateCharts();
        }
    });
}

// اكتشاف عمود نوع المرتجع تلقائيًا
function detectReturnTypeColumn(){
    const sample = excelData[0];
    for(let key in sample){
        if(key.toLowerCase().includes("type") || key.toLowerCase().includes("return")){
            returnTypeColumn = key;
            break;
        }
    }
}

// الإحصائيات
function generateStats(){
    let total = 0;
    let invoices = new Set();
    let typeCount = {};

    excelData.forEach(r=>{
        if(r[returnTypeColumn]){
            total++;
            typeCount[r[returnTypeColumn]] = (typeCount[r[returnTypeColumn]]||0)+1;
        }
        if(r["client code"]) invoices.add(r["client code"]);
    });

    let topType = Object.entries(typeCount).sort((a,b)=>b[1]-a[1])[0]?.[0] || "-";

    totalReturns.innerText = total;
    invoiceCount.innerText = invoices.size;
    topItem.innerText = topType;
}

// الرسومات
function generateCharts(){
    const types = {}, days = {}, clients = {};

    excelData.forEach(r=>{
        types[r[returnTypeColumn]] = (types[r[returnTypeColumn]]||0)+1;
        days[r.Date || "غير معروف"] = (days[r.Date || "غير معروف"]||0)+1;
        clients[r["client code"]] = (clients[r["client code"]]||0)+1;
    });

    new Chart(typeChart, {
        type:"bar",
        data:{labels:Object.keys(types), datasets:[{data:Object.values(types)}]},
        options:{responsive:true}
    });

    new Chart(dailyChart, {
        type:"line",
        data:{labels:Object.keys(days), datasets:[{data:Object.values(days)}]},
        options:{responsive:true}
    });

    new Chart(clientChart, {
        type:"bar",
        data:{labels:Object.keys(clients), datasets:[{data:Object.values(clients)}]},
        options:{responsive:true}
    });
}

// تحميل أولي + تحديث تلقائي كل 30 ثانية
loadData();
setInterval(loadData, 30000);
</script>

</body>
</html>
