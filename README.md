[index.html.html](https://github.com/user-attachments/files/21918159/index.html.html)
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>樹速配 Send tree pay</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            background-color: #f0f2f5;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            color: #333;
        }

        .app-container {
            width: 375px; /* Common mobile width */
            background-color: #fff;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }

        .header {
            background-color: #4CAF50; /* Green theme for 'tree' */
            color: white;
            padding: 20px;
            text-align: center;
            font-size: 24px;
            font-weight: bold;
        }
        .header h1 {
            margin: 0;
            font-size: 1.5em;
        }
        .header p {
            margin: 5px 0 0;
            font-size: 0.8em;
            opacity: 0.8;
        }

        .balance-section {
            padding: 30px;
            text-align: center;
            background-color: #e8f5e9;
        }

        .balance-section h2 {
            margin: 0 0 10px;
            font-size: 1.2em;
            color: #555;
        }

        .balance-section .amount {
            font-size: 3em;
            font-weight: bold;
            color: #4CAF50;
        }

        .main-menu {
            display: flex;
            justify-content: space-around;
            padding: 20px 0;
            background-color: #fff;
        }

        .menu-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            cursor: pointer;
            width: 100px;
            text-align: center;
            transition: transform 0.2s, color 0.2s;
        }

        .menu-item:hover {
            transform: translateY(-2px);
            color: #4CAF50;
        }

        .menu-item img {
            width: 50px;
            height: 50px;
            margin-bottom: 8px;
        }

        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.6);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .modal-content {
            background-color: #fff;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
            width: 85%;
            max-width: 400px;
            position: relative;
            text-align: center;
        }

        .close-button {
            position: absolute;
            top: 15px;
            right: 15px;
            font-size: 24px;
            cursor: pointer;
            color: #888;
        }

        .modal-content h2 {
            margin-top: 0;
            color: #4CAF50;
            font-size: 1.5em;
        }

        .modal-content input {
            width: 90%;
            padding: 12px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 1em;
        }

        .modal-content button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1em;
            width: 100%;
            margin-top: 10px;
        }
        
        .qr-code-section {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-top: 20px;
        }
        .qr-code-section img {
            width: 150px;
            height: 150px;
            border: 1px solid #eee;
            padding: 5px;
            border-radius: 5px;
        }
        
        /* Utility classes */
        .hidden {
            display: none;
        }
    </style>
</head>
<body>

<div class="app-container">
    <div class="header">
        <h1>樹速配 Send tree pay.</h1>
        <p>穩定幣交易平台</p>
    </div>

    <div class="balance-section">
        <h2>帳戶餘額 (USD)</h2>
        <div class="amount" id="balanceAmount">2,500.00</div>
    </div>

    <div class="main-menu">
        <div class="menu-item" onclick="openModal('pay')">
            <img src="https://www.flaticon.com/svg/static/icons/svg/1057/1057303.svg" alt="Pay">
            <span>付款</span>
        </div>
        <div class="menu-item" onclick="openModal('request')">
            <img src="https://www.flaticon.com/svg/static/icons/svg/1057/1057297.svg" alt="Request">
            <span>收款</span>
        </div>
    </div>
</div>

<div id="payModal" class="modal">
    <div class="modal-content">
        <span class="close-button" onclick="closeModal('payModal')">&times;</span>
        <h2>付款</h2>
        <input type="text" id="payAddress" placeholder="輸入收款人地址">
        <input type="number" id="payAmount" placeholder="輸入金額 (USD)">
        <button onclick="processPayment()">確認付款</button>
    </div>
</div>

<div id="requestModal" class="modal">
    <div class="modal-content">
        <span class="close-button" onclick="closeModal('requestModal')">&times;</span>
        <h2>收款</h2>
        <input type="number" id="requestAmountInput" placeholder="輸入收款金額 (USD)">
        <button onclick="generateQRCode()">生成收款碼</button>
        <div id="qrCodeSection" class="qr-code-section hidden">
            <p>請掃描此QR Code進行付款：</p>
            <img id="qrCodeImage" src="" alt="QR Code">
        </div>
    </div>
</div>

<script>
    function openModal(modalId) {
        document.getElementById(modalId + 'Modal').style.display = 'flex';
    }

    function closeModal(modalId) {
        document.getElementById(modalId).style.display = 'none';
        // Reset QR code and input when closing
        if (modalId === 'requestModal') {
            document.getElementById('qrCodeSection').classList.add('hidden');
            document.getElementById('requestAmountInput').value = '';
        }
    }

    function processPayment() {
        const address = document.getElementById('payAddress').value;
        const amount = document.getElementById('payAmount').value;
        
        if (address && amount > 0) {
            alert(`正在向地址 ${address} 付款 ${amount} USD...`);
            // Here you would add real payment logic
            closeModal('payModal');
        } else {
            alert('請輸入有效的地址和金額');
        }
    }

    function generateQRCode() {
        const amount = document.getElementById('requestAmountInput').value;
        if (amount > 0) {
            // In a real app, this would generate a unique QR code.
            // For this demo, we use a placeholder image and alert a message.
            const qrCodeSection = document.getElementById('qrCodeSection');
            const qrCodeImage = document.getElementById('qrCodeImage');
            
            // This is a dummy QR code URL. A real app would use a QR code library.
            // Data encoded: 'sendtreepay://request?amount=' + amount
            qrCodeImage.src = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=sendtreepay://request?amount=${amount}`;
            
            qrCodeSection.classList.remove('hidden');
            alert(`已生成收款碼，金額為 ${amount} USD。`);
        } else {
            alert('請輸入有效的收款金額');
        }
    }
</script>

</body>
</html>
