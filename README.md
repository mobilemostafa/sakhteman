# sakhteman
به نام خدا

<html lang="fa">
<head>
    <meta charset="UTF-8">
    <title>محاسبه سهم واحدها</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, sans-serif;
            direction: rtl;
            padding: 20px;
            background-color: #f5f5f5;
        }
        h2 {
            color: #333;
            text-align: center;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        .input-group {
            margin: 15px 0;
            display: flex;
            align-items: center;
        }
        .input-group label {
            width: 120px;
            color: #555;
        }
        .input-group input {
            padding: 8px;
            width: 200px;
            border: 1px solid #ddd;
            border-radius: 5px;
            outline: none;
        }
        .input-group input:focus {
            border-color: #4CAF50;
        }
        .button-group {
            text-align: center;
            margin: 20px 0;
        }
        button {
            padding: 10px 20px;
            margin: 0 10px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
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
            margin-top: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: center;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
        table tr:nth-child(even) {
            background-color: #e8f5e9; /* سبز خیلی روشن */
        }
        #summaryTable {
            width: 50%;
            margin: 20px auto;
        }
        #summaryTable th {
            background-color: #FF9800;
        }
        #summaryTable tr:nth-child(even) {
            background-color: #fff3e0; /* نارنجی خیلی روشن */
        }
        #resultTitle {
            text-align: center;
            font-size: 12pt;
            margin-top: 10mm;
        }

        /* تنظیمات پرینت */
        @media print {
            body {
                padding: 0;
                background-color: white;
            }
            .container {
                box-shadow: none;
                padding: 5mm; /* کاهش padding */
                width: 100%;
                max-width: 100%;
            }
            .input-group, .button-group {
                display: none; /* مخفی کردن ورودی‌ها و دکمه‌ها */
            }
            h2 {
                font-size: 12pt; /* کوچیک‌تر کردن عنوان */
                margin: 0 0 3mm 0; /* کاهش حاشیه */
            }
            #summaryTable {
                width: 35%; /* کوچیک‌تر کردن جدول خلاصه */
                margin: 3mm auto; /* کاهش فاصله */
                font-size: 9pt; /* فونت کوچیک‌تر */
            }
            #resultTitle {
                font-size: 10pt; /* کوچیک‌تر کردن عنوان جزئیات */
                margin: 3mm 0; /* کاهش فاصله */
            }
            table {
                width: 85%; /* کاهش عرض برای جا شدن ستون آخر */
                margin: 0 5mm 0 auto; /* کشیدن به راست */
                font-size: 9pt; /* فونت کوچیک‌تر */
            }
            th, td {
                padding: 3px; /* کاهش padding سلول‌ها */
            }
            @page {
                size: A4;
                margin: 5mm; /* کاهش حاشیه صفحه */
            }
        }
    </style>
</head>
<body>
    <div class="container">
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
        // تعداد نفرات هر واحد ثابت است
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
        const totalResidents = residents.reduce((a, b) => a + b, 0); // 25 نفر

        function formatNumber(number) {
            return Math.round(number).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        }

        function calculateShares() {
            // گرفتن مقادیر ورودی
            const waterBill = parseFloat(document.getElementById('waterBill').value) || 0;
            const electricityBill = parseFloat(document.getElementById('electricityBill').value) || 0;
            const gasBill = parseFloat(document.getElementById('gasBill').value) || 0;
            const cleaningCost = parseFloat(document.getElementById('cleaningCost').value) || 0;
            const extraCost = parseFloat(document.getElementById('extraCost').value) || 0;

            // محاسبه سهم هر نفر برای آب
            const waterPerPerson = waterBill / totalResidents;
            // محاسبه سهم هر واحد برای بقیه هزینه‌ها
            const electricityShare = electricityBill / totalUnits;
            const gasShare = gasBill / totalUnits;
            const cleaningShare = cleaningCost / totalUnits;
            const extraShare = extraCost / totalUnits;

            // ساخت جدول اصلی
            let table = '<table>';
            table += '<tr><th>واحد</th><th>تعداد نفرات</th><th>سهم آب</th><th>سهم برق</th><th>سهم گاز</th><th>سهم نظافت</th><th>سهم اضافی</th><th>جمع کل</th></tr>';

            // ساخت جدول خلاصه
            let summaryTable = '<table id="summaryTable">';
            summaryTable += '<tr><th>واحد</th><th>جمع کل</th></tr>';

            for (let i = 0; i < totalUnits; i++) {
                const unitName = unitNames[i];
                const residentCount = residents[i];
                
                // محاسبه سهم آب: مبلغ هر نفر × تعداد نفرات واحد
                const waterShare = waterPerPerson * residentCount;
                const totalShare = waterShare + electricityShare + gasShare + cleaningShare + extraShare;

                // پر کردن جدول اصلی
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

                // پر کردن جدول خلاصه
                summaryTable += `<tr>
                    <td>${unitName}</td>
                    <td>${formatNumber(totalShare)} تومان</td>
                </tr>`;
            }

            table += '</table>';
            summaryTable += '</table>';

            document.getElementById('summary').innerHTML = summaryTable;
            document.getElementById('result').innerHTML = table;
            document.getElementById('resultTitle').style.display = 'block'; // نمایش عنوان هنگام محاسبه
        }
    </script>
</body>
</html>
