
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Valyuta Kursi | Live Converter</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        
        body {
            background: #0f172a;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            color: white;
        }

        .converter-card {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(20px);
            padding: 40px;
            border-radius: 30px;
            width: 400px;
            border: 1px solid rgba(255,255,255,0.1);
            box-shadow: 0 25px 50px rgba(0,0,0,0.3);
            text-align: center;
        }

        h2 { margin-bottom: 30px; font-weight: 600; color: #38bdf8; }

        .input-group {
            margin-bottom: 20px;
            text-align: left;
        }

        label { display: block; font-size: 12px; margin-bottom: 8px; opacity: 0.6; text-transform: uppercase; }

        .field {
            display: flex;
            background: rgba(255,255,255,0.05);
            border-radius: 12px;
            padding: 10px 15px;
            border: 1px solid rgba(255,255,255,0.1);
            align-items: center;
        }

        input, select {
            background: transparent;
            border: none;
            color: white;
            outline: none;
            font-size: 18px;
            width: 100%;
        }

        select { cursor: pointer; font-weight: bold; width: 85px; }
        option { background: #1e293b; }

        .swap-icon {
            margin: 10px 0;
            font-size: 20px;
            color: #38bdf8;
            cursor: pointer;
            transition: 0.3s;
            display: inline-block;
        }
        .swap-icon:hover { transform: rotate(180deg); }

        .result-info {
            margin-top: 25px;
            padding: 15px;
            background: rgba(56, 189, 248, 0.1);
            border-radius: 12px;
            border-left: 4px solid #38bdf8;
            text-align: left;
        }

        .rate-text { font-size: 14px; opacity: 0.8; }
        #finalResult { font-size: 20px; font-weight: bold; margin-top: 5px; display: block; color: #fff; }

        .update-time { font-size: 10px; margin-top: 15px; opacity: 0.4; }
    </style>
</head>
<body>

<div class="converter-card">
    <h2>Valyuta Kalkulyatori</h2>

    <div class="input-group">
        <label>Sizda bor</label>
        <div class="field">
            <input type="number" id="amount" value="1" step="any">
            <select id="fromCurrency">
                <option value="USD" selected>USD</option>
                <option value="EUR">EUR</option>
                <option value="RUB">RUB</option>
                <option value="UZS">UZS</option>
            </select>
        </div>
    </div>

    <i class="fas fa-retweet swap-icon" onclick="swapCurrencies()"></i>

    <div class="input-group">
        <label>Aylantirish</label>
        <div class="field">
            <input type="text" id="convertedAmount" readonly placeholder="Natija...">
            <select id="toCurrency">
                <option value="UZS" selected>UZS</option>
                <option value="USD">USD</option>
                <option value="EUR">EUR</option>
                <option value="RUB">RUB</option>
            </select>
        </div>
    </div>

    <div class="result-info">
        <div class="rate-text" id="rateInfo">1 USD = ... UZS</div>
        <div id="finalResult">Hisoblanmoqda...</div>
    </div>

    <p class="update-time">Ma'lumotlar real vaqt rejimida yangilanadi</p>
</div>

<script>
    const API_URL = "https://open.er-api.com/v6/latest/";


const amountInput = document.getElementById('amount');
    const fromSelect = document.getElementById('fromCurrency');
    const toSelect = document.getElementById('toCurrency');
    const resultField = document.getElementById('convertedAmount');
    const rateInfo = document.getElementById('rateInfo');
    const finalResult = document.getElementById('finalResult');

    async function convert() {
        const from = fromSelect.value;
        const to = toSelect.value;
        const amount = amountInput.value;

        if (amount === "" || amount <= 0) {
            resultField.value = "";
            finalResult.innerText = "Miqdorni kiriting";
            return;
        }

        try {
            const response = await fetch(${API_URL}${from});
            const data = await response.json();
            
            if (data.result === "success") {
                const rate = data.rates[to];
                const total = (amount * rate);
                
                // Natijani chiroyli formatda chiqarish
                resultField.value = total.toLocaleString(undefined, {minimumFractionDigits: 2, maximumFractionDigits: 2});
                rateInfo.innerText = 1 ${from} = ${rate.toLocaleString()} ${to};
                finalResult.innerText = ${Number(amount).toLocaleString()} ${from} = ${total.toLocaleString()} ${to};
            } else {
                finalResult.innerText = "Ma'lumot olishda xatolik!";
            }
        } catch (error) {
            finalResult.innerText = "Internet aloqasini tekshiring!";
            console.error("API xatosi:", error);
        }
    }

    function swapCurrencies() {
        const temp = fromSelect.value;
        fromSelect.value = toSelect.value;
        toSelect.value = temp;
        convert();
    }

    // Hodisalarni kuzatish
    amountInput.addEventListener('input', convert);
    fromSelect.addEventListener('change', convert);
    toSelect.addEventListener('change', convert);

    // Dastlabki hisob
    convert();
</script>

</body>
</html>
