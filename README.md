<!DOCTYPE html>
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
            margin: 10px 0;
            display: flex;
            align-items: center;
            flex-wrap: wrap;
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
            margin: 5px;
        }
        #cardNumber {
            direction: ltr; /* چپ به راست برای شماره کارت */
            text-align: left;
        }
        .input-group input:focus {
            border-color: #4CAF50;
        }
        .button-group {
            text-align: center;
            margin: 15px 0;
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
            margin-top: 15px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
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
            margin: 15px auto;
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
            margin-top: 10px;
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
                max-width: 100%;
            }
            .input-group:not(#bankInfo), .button-group {
                display: none; /* مخفی کردن بقیه ورودی‌ها و دکمه‌ها */
            }
            #bankInfo {
                margin: 0;
                font-size: 8pt;
                display: flex;
                justify-content: space-between;
            }
            #bankInfo label {
                width: auto;
                margin: 0 5px;
            }
            #bankInfo input {
                width: 100px;
                padding: 2px;
                margin: 0 5px;
                border: none;
                direction: ltr; /* چپ به راست توی پرینت */
            }
            h2 {
                font-size: 11pt;
                margin: 2mm 0;
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
            <label>شماره کارت:</label>
            <input type="text" id="cardNumber" placeholder="شماره کارت">
            <label>صاحب حساب:</label>
            <input type="text" id="accountHolder" placeholder="نام صاحب حساب">
            <label>شماره تماس مدیر:</label>
            <input type="text" id="managerContact" placeholder="شماره تماس">
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

        // مدیریت ورودی شماره کارت و شماره تماس
        document.getElementById('cardNumber').addEventListener('input', function(e) {
            this.value = this.value.replace(/[^0-9-]/g, ''); // فقط عدد و خط تیره
        });
        document.getElementById('managerContact').addEventListener('input', function(e) {
            this.value = this.value.replace(/[^0-9-]/g, ''); // فقط عدد و خط تیره
        });

        function calculateShares() {
            // گرفتن مقادیر ورودی
            const waterBill = parseFloat(document.getElementById('waterBill').value) || 0;
            const electricityBill = parseFloat(document.getElementById('electricityBill').value) || 0;
            const gasBill = parseFloat(document.getElementById('gasBill').value) || 0;
            const cleaningCost = parseFloat(document.getElementById('cleaningCost').value) || 0;
            const extraCost = parseFloat(document.getElementById('extraCost').value) || 0;
            const cardNumber = document.getElementById('cardNumber').value || "نامشخص";
            const accountHolder = document.getElementById('accountHolder').value || "نامشخص";
            const managerContact = document.getElementById('managerContact').value || "نامشخص";

            // تنظیم مقادیر برای پرینت
            document.getElementById('cardNumber').value = cardNumber;
            document.getElementById('accountHolder').value = accountHolder;
            document.getElementById('managerContact').value = managerContact;

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
            document.getElementById('resultTitle').style.display = 'block';
        }
    </script>
</body>
</html>
