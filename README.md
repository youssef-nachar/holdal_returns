<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>
    <meta charset="UTF-8" />
    <title>Returns Dashboard</title>

    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>

    <style>
        /* Global body styles */
        body {
            font-family: 'Arial', sans-serif;
            background: #f5f5f5;
            margin: 0;
            padding: 0;
            color: #333;
        }

        /* Container styling */
        .container {
            max-width: 1200px;
            margin: 40px auto;
            background: #ffffff;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
        }

        /* Header */
        h2 {
            text-align: center;
            color: #007bff;
            font-size: 2.5rem;
            margin-bottom: 20px;
        }

        /* Card layout */
        .card {
            background: #eef4ff;
            padding: 20px;
            border-radius: 12px;
            margin-bottom: 25px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
            transition: all 0.3s ease;
        }

        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
        }

        /* Card title */
        h3 {
            color: #333;
            font-size: 1.8rem;
            margin-bottom: 20px;
        }

        /* Paragraphs inside card */
        .card p {
            font-size: 1.1rem;
            margin: 10px 0;
        }

        .card span {
            font-weight: bold;
            color: #007bff;
        }

        /* Chart area */
        canvas {
            width: 100%;
            height: 400px;
            border-radius: 10px;
        }

        /* Responsive styles for small screens */
        @media (max-width: 768px) {
            .container {
                padding: 15px;
            }

            h2 {
                font-size: 2rem;
            }

            .card {
                padding: 15px;
            }

            canvas {
                height: 300px;
            }
        }

        /* Loading Spinner */
        .loading {
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.5rem;
            color: #007bff;
            height: 100vh;
        }
    </style>
</head>

<body>

    <div class="container">
        <h2>📦 Returns Dashboard</h2>

        <div class="card">
            <h3>📊 Dashboard Overview</h3>
            <p>Number of returns: <span id="totalReturns">0</span></p>
            <p>Total unique clients: <span id="invoiceCount">0</span></p>
            <p>Most common return type: <span id="topItem">-</span></p>
        </div>

        <div class="card">
            <h3>📈 Return Types</h3>
            <canvas id="typeChart"></canvas>
        </div>

        <!-- Uncomment if you want to include daily data chart -->
        <!-- <div class="card">
            <h3>📅 Daily Returns</h3>
            <canvas id="dailyChart"></canvas>
        </div> -->

        <div class="card">
            <h3>👤 Client Returns</h3>
            <canvas id="clientChart"></canvas>
        </div>

    </div>

    <script>
        // Google Sheets CSV URL
        const SHEET_URL = "https://docs.google.com/spreadsheets/d/e/2PACX-1vQ1YYrDLGyKiYfZT1c9Bq6mzJz2K68NsF66eR_ACAUpaHn2oeT5fYBYIWWZw4_oBA/pub?gid=1241838978&single=true&output=csv";

        let excelData = [];
        let colType = "", colClient = "", colDate = "";

        // Load data from Google Sheets
        function loadData() {
            Papa.parse(SHEET_URL, {
                download: true,
                header: true,
                complete: function (result) {
                    excelData = result.data.filter(r => Object.keys(r).length > 1);
                    detectColumns();
                    generateStats();
                    generateCharts();
                }
            });
        }

        // Automatically detect the columns
        function detectColumns() {
            const sample = excelData[0];

            for (let key in sample) {
                const k = key.toLowerCase();

                if (!colType && (k.includes("type") || k.includes("return") || k.includes("مرتجع")))
                    colType = key;

                if (!colClient && (k.includes("client") || k.includes("customer") || k.includes("زبون")))
                    colClient = key;

                if (!colDate && (k.includes("date") || k.includes("تاريخ")))
                    colDate = key;
            }
        }

        // Generate stats (total returns, unique clients, top return type)
        function generateStats() {
            let total = 0;
            let invoices = new Set();
            let typeCount = {};

            excelData.forEach(r => {
                if (r[colType]) {
                    total++;
                    typeCount[r[colType]] = (typeCount[r[colType]] || 0) + 1;
                }
                if (r[colClient]) invoices.add(r[colClient]);
            });

            let topType = Object.entries(typeCount)
                .sort((a, b) => b[1] - a[1])[0]?.[0] || "-";

            totalReturns.innerText = total;
            invoiceCount.innerText = invoices.size;
            topItem.innerText = topType;
        }

        // Generate charts
        function generateCharts() {
            const types = {}, days = {}, clients = {};

            excelData.forEach(r => {
                if (r[colType]) types[r[colType]] = (types[r[colType]] || 0) + 1;
                if (r[colDate]) days[r[colDate]] = (days[r[colDate]] || 0) + 1;
                if (r[colClient]) clients[r[colClient]] = (clients[r[colClient]] || 0) + 1;
            });

            // Return Type Chart
            new Chart(typeChart, {
                type: "bar",
                data: { labels: Object.keys(types), datasets: [{ data: Object.values(types) }] },
                options: { responsive: true }
            });

            // Uncomment to display daily chart
            // new Chart(dailyChart, {
            //     type: "line",
            //     data: { labels: Object.keys(days), datasets: [{ data: Object.values(days) }] },
            //     options: { responsive: true }
            // });

            // Client Chart
            new Chart(clientChart, {
                type: "bar",
                data: { labels: Object.keys(clients), datasets: [{ data: Object.values(clients) }] },
                options: { responsive: true }
            });
        }

        // Initial data load and auto-refresh every 30 seconds
        loadData();
        setInterval(loadData, 30000);
    </script>

</body>

</html>
