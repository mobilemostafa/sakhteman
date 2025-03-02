
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>محاسبه سهم واحدها</title>
    <style>
        * {
            box-sizing: border-box;
        }
        body {
            font-family: 'Segoe UI', Tahoma, sans-serif;
            direction: rtl;
            padding: 5%;
            background-color: #f5f5f5;
            margin: 0;
            font-size: 16px;
        }
        h2 {
            color: #333;
            text-align: center;
            font-size: 1.5rem;
            margin: 0 0 10px 0;
        }
        .container {
            max-width: 100%;
            margin: 0 auto;
            background: white;
            padding: 5%;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        .input-group {
            margin: 8px 0;
            display: flex;
            flex-direction: column;
            width: 100%;
        }
        .input-group label {
            color: #555;
            margin-bottom: 5px;
            font-size: 1rem;
        }
        .input-group input {
            padding: 8px;
            width: 100%;
            border: 1px solid #ddd;
            border-radius: 5px;
            outline: none;
            margin-bottom: 10px;
            font-size: 1rem;
        }
        #cardNumber {
            direction: ltr;
            text-align: left;
        }
        .input-group input:focus {
            border-color: #4CAF50;
        }
        .button-group {
            text-align: center;
            margin: 15px 0;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        button {
            padding: 10px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
            width: 100%;
            font-size: 1rem;
        }
        button:hover {
            background-color: #45a049;
        }
        #printBtn {
            background-color: #2196F3;
        }
        #printBtn:hover {
            background-color: #1e87db;
        }
        table {
            border-collapse: collapse;
            width: 100%;
            margin-top: 15px;
            font-size: 0.9rem;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 5px;
            text-align: center;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
        table tr:nth-child(even) {
            background-color: #e8f5e9;
        }
        #summaryTable {
            width: 100%;
            margin: 15px 0;
        }
        #summaryTable th {
            background-color: #FF9800;
        }
        #summaryTable tr:nth-child(even) {
            background-color: #fff3e0;
        }
        #resultTitle {
            text-align: center;
            font-size: 1.2rem;
            margin-top: 10px;
        }

        /* تنظیمات برای صفحه‌های بزرگ‌تر */
        @media screen and (min-width: 600px) {
            .container {
                max-width: 800px;
                padding: 20px;
            }
            .input-group {
                flex-direction: row;
                align-items: center;
            }
            .input-group label {
                width: 120px;
                margin-bottom: 0;
            }
            .input-group input {
                width: 200px;
                margin: 0 5px 0 0;
            }
            .button-group {
                flex-direction: row;
                justify-content: center;
                gap: 20px;
            }
            button {
                width: auto;
            }
            #summaryTable {
                width: 50%;
                margin: 15px auto;
            }
            table {
                font-size: 1rem;
            }
            th, td {
                padding: 8px;
            }
        }

        /* تنظیمات پرینت */
        @media print {
            body {
                padding: 0;
                background-color: white;
            }
            .container {
                box-shadow: none;
                padding: 5mm;
                width: 100%;
                position: relative; /* برای تنظیم ترتیب */
            }
            .input-group:not(#bankInfo), .button-group {
                display: none;
            }
            h2 {
                font-size: 11pt;
                margin: 0 0 2mm 0;
                position: absolute; /* بالای صفحه */
                top: 0;
                left: 0;
                right: 0;
                text-align: center;
            }
            #bankInfo {
                margin: 12mm 0 2mm 0; /* فاصله از بالا برای جا دادن h2 */
                font-size: 8pt;
                display: flex;
                flex-direction: column;
                align-items: center;
                text-align: center;
                width: 100%;
            }
            #bankInfo span {
                display: flex;
                align-items: center;
                margin-bottom: 2mm;
            }
            #bankInfo label {
                margin: 0 5px 0 0;
                display: inline-block;
            }
            #bankInfo input {
                padding: 2px;
                margin: 0;
                border: none;
                direction: ltr;
                text-align: left;
                white-space: nowrap;
                overflow: visible;
                display: inline-block;
            }
            #bankInfo input#cardNumber {
                width: 150px;
            }
            #bankInfo input#accountHolder,
            #bankInfo input#managerContact {
                width: 100px;
            }
            #summaryTable {
                width: 35%;
                margin: 2mm auto;
                font-size: 8pt;
            }
            #resultTitle {
                font-size: 9pt;
                margin: 2mm 0;
            }
            table {
                width: 85%;
                margin: 0 5mm 0 auto;
                font-size: 8pt;
            }
            th, td {
                padding: 2px;
            }
            @page {
                size: A4;
                margin: 5mm;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="input-group" id="bankInfo">
            <span>
                <label>شماره کارت:</label>
                <input type="text" id="cardNumber" placeholder="شماره کارت">
            </span>
            <span>
                <label>صاحب حساب:</label>
                <input type="text" id="accountHolder" placeholder="نام صاحب حساب">
            </span>
            <span>
                <label>شماره تماس مدیر:</label>
                <input type="text" id="managerContact" placeholder="شماره تماس">
            </span>
        </div>
        <h2>محاسبه سهم هر واحد در ساختمان ابویی با نفرات 25</h2>
        
        <div class="input-group">
            <label>مبلغ قبض آب: </label>
            <input type="number" id="waterBill" placeholder="به تومان">
        </div>
        <div class="input-group">
            <label>مبلغ قبض برق: </label>
            <input type="number" id="electricityBill" placeholder="به تومان">
        </div>
        <div class="input-group">
            <label>مبلغ قبض گاز: </label>
            <input type="number" id="gasBill" placeholder="به تومان">
        </div>
        <div class="input-group">
            <label>هزینه نظافت: </label>
            <input type="number" id="cleaningCost" placeholder="به تومان">
        </div>
        <div class="input-group">
            <label>هزینه اضافی: </label>
            <input type="number" id="extraCost" placeholder="به تومان">
        </div>
        
        <div class="button-group">
            <button onclick="calculateShares()">محاسبه</button>
            <button id="printBtn" onclick="window.print()">پرینت</button>
        </div>
        
        <div id="summary"></div>
        <div id="resultTitle" style="display:none">جزئیات محاسبات</div>
        <div id="result"></div>
    </div>

    <script>
        const residents = [4, 4, 2, 2, 2, 4, 4, 3];
        const unitNames = [
            "واحد یک آقای رضایی",
            "واحد دو آقای آخوندی",
            "واحد سه آقای موسوی",
            "واحد چهار آقای خراسانی",
            "واحد پنج خانم حسینی",
            "واحد شش آقای طالبی",
            "واحد هفت آقای عباس‌لو",
            "واحد هشت آقای ماندگاری"
        ];
        const totalUnits = 8;
        const totalResidents = residents.reduce((a, b) => a + b, 0);

        function formatNumber(number) {
            return Math.round(number).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        }

        document.getElementById('cardNumber').addEventListener('input', function(e) {
            this.value = this.value.replace(/[^0-9-]/g, '');
        });
        document.getElementById('managerContact').addEventListener('input', function(e) {
            this.value = this.value.replace(/[^0-9-]/g, '');
        });
        document.getElementById('accountHolder').addEventListener('input', function(e) {
            this.value = this.value.replace(/[^آ-یa-zA-Z0-9\s]/g, '');
        });

        function calculateShares() {
            const waterBill = parseFloat(document.getElementById('waterBill').value) || 0;
            const electricityBill = parseFloat(document.getElementById('electricityBill').value) || 0;
            const gasBill = parseFloat(document.getElementById('gasBill').value) || 0;
            const cleaningCost = parseFloat(document.getElementById('cleaningCost').value) || 0;
            const extraCost = parseFloat(document.getElementById('extraCost').value) || 0;
            const cardNumber = document.getElementById('cardNumber').value || "نامشخص";
            const accountHolder = document.getElementById('accountHolder').value || "نامشخص";
            const managerContact = document.getElementById('managerContact').value || "نامشخص";

            document.getElementById('cardNumber').value = cardNumber;
            document.getElementById('accountHolder').value = accountHolder;
            document.getElementById('managerContact').value = managerContact;

            const waterPerPerson = waterBill / totalResidents;
            const electricityShare = electricityBill / totalUnits;
            const gasShare = gasBill / totalUnits;
            const cleaningShare = cleaningCost / totalUnits;
            const extraShare = extraCost / totalUnits;

            let table = '<table>';
            table += '<tr><th>واحد</th><th>تعداد نفرات</th><th>سهم آب</th><th>سهم برق</th><th>سهم گاز</th><th>سهم نظافت</th><th>سهم اضافی</th><th>جمع کل</th></tr>';

            let summaryTable = '<table id="summaryTable">';
            summaryTable += '<tr><th>واحد</th><th>جمع کل</th></tr>';

            for (let i = 0; i < totalUnits; i++) {
                const unitName = unitNames[i];
                const residentCount = residents[i];
                const waterShare = waterPerPerson * residentCount;
                const totalShare = waterShare + electricityShare + gasShare + cleaningShare + extraShare;

                table += `<tr>
                    <td>${unitName}</td>
                    <td>${residentCount}</td>
                    <td>${formatNumber(waterShare)}</td>
                    <td>${formatNumber(electricityShare)}</td>
                    <td>${formatNumber(gasShare)}</td>
                    <td>${formatNumber(cleaningShare)}</td>
                    <td>${formatNumber(extraShare)}</td>
                    <td>${formatNumber(totalShare)} تومان</td>
                </tr>`;

                summaryTable += `<tr>
                    <td>${unitName}</td>
                    <td>${formatNumber(totalShare)} تومان</td>
                </tr>`;
            }

            table += '</table>';
            summaryTable += '</table>';

            document.getElementById('summary').innerHTML = summaryTable;
            document.getElementById('result').innerHTML = table;
            document.getElementById('resultTitle').style.display = 'block';
        }
    </script>
</body>
</html>
