<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Returns Dashboard</title>

    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>

    <style>
        /* Global body styles */
        body {
            font-family: 'Arial', sans-serif;
            background: #2c3e50; /* Dark grey-blue background */
            margin: 0;
            padding: 0;
            color: #ecf0f1; /* Light grey text */
            transition: background-color 0.5s ease;
        }

        /* Header styles */
        header {
        font-size:14px;
            background-color: #34495e; /* Darker grey-blue */
            color: #f39c12; /* Gold color for header */
            padding: 20px 0;
            text-align: center;
            font-size: 2rem; /* Adjusted for better responsiveness */
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }

        /* Container styling */
        .container {
            max-width: 1200px;
            margin: 40px auto;
            background: #34495e; /* Darker grey-blue for containers */
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
            animation: fadeIn 1s ease-in-out;
        }

        /* Footer styles */
        footer {
            background-color: #34495e;
            color: #ecf0f1;
            padding: 20px 0;
            text-align: center;
            font-size: 1rem;
            width: 100%;
            bottom: 0;
            left: 0;
            box-shadow: 0 -4px 10px rgba(0, 0, 0, 0.2);
        }

        /* Fade-in animation */
        @keyframes fadeIn {
            0% {
                opacity: 0;
            }
            100% {
                opacity: 1;
            }
        }

        /* Header */
        h2 {
            text-align: center;
            color: #f39c12; /* Gold color for header */
            font-size: 2.5rem;
            margin-bottom: 20px;
            animation: slideIn 1s ease-out;
        }

        /* Slide-in animation */
        @keyframes slideIn {
            0% {
                transform: translateY(-30px);
                opacity: 0;
            }
            100% {
                transform: translateY(0);
                opacity: 1;
            }
        }

        /* Card layout */
        .card {
            background: #4a5158; /* Dark teal */
            padding: 20px;
            border-radius: 12px;
            margin-bottom: 25px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            transition: all 0.3s ease;
        }

        .card:hover {
            transform: translateY(-8px);
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.25);
            animation: bounce 0.5s ease;
        }

        /* Bounce animation */
        @keyframes bounce {
            0% {
                transform: translateY(-8px);
            }
            50% {
                transform: translateY(0);
            }
            100% {
                transform: translateY(-8px);
            }
        }

        /* Card title */
        h3 {
            color: #ecf0f1;
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
            color: #f39c12;
        }

        /* Chart area */
        canvas {
            width: 100%;
            height: 400px;
            border-radius: 10px;
            animation: slideIn 1.5s ease-out;
        }

        /* Responsive design for mobile devices */
        @media (max-width: 768px) {
            header {
                font-size: 1.5rem; /* Adjust font size for smaller screens */
            }

            .container {
                padding: 15px;
                width:100%
            }

            h2 {
                font-size: 2rem;
            }

            .card {
            
                padding: 15px;
            }

            canvas {
                height: 250px; /* Adjust canvas size */
            }

            h3 {
                font-size: 1.5rem; /* Adjust card titles */
            }

            footer {
                font-size: 0.9rem; /* Adjust footer text for small screens */
            }
        }
 @media (max-width: 400px) {
            header {
                font-size: 1.5rem; /* Adjust font size for smaller screens */
            }

            .container {
                padding: 15px;
                width:100%
            }

            h2 {
                font-size: 2rem;
            }
            }
        /* Loading Spinner */
        .loading {
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.5rem;
            color: #f39c12;
            height: 100vh;
        }

    </style>
</head>

<body>

    <!-- Header Section -->
    <header>
        <h2>📦 Returns Dashboard</h2>
    </header>

    <div class="container">
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

        <div class="card">
            <h3>👤 Client Returns</h3>
            <canvas id="clientChart"></canvas>
        </div>

    </div>

    <!-- Footer Section -->
    <footer>
        &copy; 2025 Returns Dashboard | All Rights Reserved
    </footer>

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
                options: {
                    responsive: true,
                    scales: {
                        y: {
                            ticks: {
                                beginAtZero: true,
                                stepSize: 1,
                                precision: 0
                            }
                        }
                    }
                }
            });

            // Client Chart
            new Chart(clientChart, {
                type: "bar",
                data: { labels: Object.keys(clients), datasets: [{ data: Object.values(clients) }] },
                options: {
                    responsive: true,
                    scales: {
                        y: {
                            ticks: {
                                beginAtZero: true,
                                stepSize: 1,
                                precision: 0
                            }
                        }
                    }
                }
            });
        }

        // Initial data load and auto-refresh every 30 seconds
        loadData();
        setInterval(loadData, 30000);
    </script>

</body>

</html>
