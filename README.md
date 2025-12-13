<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8" />
<title>Dashboard  </title>

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
<h2>📦 Dashboard  <h2>

<div class="card">
<h3>📊  dashboard</h3>
<p>nomber of return: <span id="totalReturns">0</span></p>
<p> <span id="invoiceCount"></span></p>
<p> <span id="topItem">-</span></p>
</div>

<div class="card">
<h3>📈  return type   </h3>
<canvas id="typeChart"></canvas>
</div>

<!-- <div class="card">
<h3>📅 الفواتير اليومية</h3>
<canvas id="dailyChart"></canvas>
</div> -->

<div class="card">
<h3>👤  client returns  </h3>
<canvas id="clientChart"></canvas>
</div>

</div>

<script>
// 🔴 رابط Google Sheets CSV
const SHEET_URL = "https://docs.google.com/spreadsheets/d/e/2PACX-1vQ1YYrDLGyKiYfZT1c9Bq6mzJz2K68NsF66eR_ACAUpaHn2oeT5fYBYIWWZw4_oBA/pub?gid=1241838978&single=true&output=csv";

let excelData = [];
let colType = "", colClient = "", colDate = "";

/* تحميل البيانات */
function loadData(){
    Papa.parse(SHEET_URL, {
        download: true,
        header: true,
        complete: function(result){
            excelData = result.data.filter(r => Object.keys(r).length > 1);
            detectColumns();
            generateStats();
            generateCharts();
        }
    });
}

/* التعرف التلقائي على الأعمدة */
function detectColumns(){
    const sample = excelData[0];

    for(let key in sample){
        const k = key.toLowerCase();

        if(!colType && (k.includes("type") || k.includes("return") || k.includes("مرتجع")))
            colType = key;

        if(!colClient && (k.includes("client") || k.includes("customer") || k.includes("زبون")))
            colClient = key;

        if(!colDate && (k.includes("date") || k.includes("تاريخ")))
            colDate = key;
    }
}

/* الإحصائيات */
function generateStats(){
    let total = 0;
    let invoices = new Set();
    let typeCount = {};

    excelData.forEach(r=>{
        if(r[colType]){
            total++;
            typeCount[r[colType]] = (typeCount[r[colType]]||0)+1;
        }
        if(r[colClient]) invoices.add(r[colClient]);
    });

    let topType = Object.entries(typeCount)
        .sort((a,b)=>b[1]-a[1])[0]?.[0] || "-";

    totalReturns.innerText = total;
    invoiceCount.innerText = invoices.size;
    topItem.innerText = topType;
}

/* الرسومات */
function generateCharts(){
    const types = {}, days = {}, clients = {};

    excelData.forEach(r=>{
        if(r[colType])
            types[r[colType]] = (types[r[colType]]||0)+1;

        if(r[colDate])
            days[r[colDate]] = (days[r[colDate]]||0)+1;

        if(r[colClient])
            clients[r[colClient]] = (clients[r[colClient]]||0)+1;
    });

    new Chart(typeChart, {
        type:"bar",
        data:{labels:Object.keys(types), datasets:[{data:Object.values(types)}]},
        options:{responsive:true}
    });

    // new Chart(dailyChart, {
    //     type:"line",
    //     data:{labels:Object.keys(days), datasets:[{data:Object.values(days)}]},
    //     options:{responsive:true}
    // });

    new Chart(clientChart, {
        type:"bar",
        data:{labels:Object.keys(clients), datasets:[{data:Object.values(clients)}]},
        options:{responsive:true}
    });
}

/* تحميل أولي + تحديث تلقائي */
loadData();
setInterval(loadData, 30000);
</script>

</body>
</html>
