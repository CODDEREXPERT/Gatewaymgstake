<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>MJStacks Gateway</title>

<style>
body{
    margin:0;
    font-family:Arial;
    background:#f4f6f8;
}

/* LOGIN */
#login{
    height:100vh;
    display:flex;
    justify-content:center;
    align-items:center;
}
.box{
    background:white;
    padding:25px;
    width:300px;
    border-radius:10px;
    box-shadow:0 5px 20px rgba(0,0,0,0.1);
    text-align:center;
}
.logo{
    font-size:20px;
    font-weight:bold;
    margin-bottom:10px;
}
input{
    width:100%;
    padding:10px;
    margin:8px 0;
}
button{
    padding:10px;
    width:100%;
    border:none;
    border-radius:6px;
    cursor:pointer;
}
.login-btn{
    background:#0a2540;
    color:white;
}

/* APP */
#app{ display:none; }

.header{
    background:#0a2540;
    color:white;
    padding:15px;
}

/* LAYOUT */
.main{
    display:flex;
}

/* SIDEBAR */
.sidebar{
    width:200px;
    background:white;
    min-height:100vh;
    padding:10px;
}
.sidebar button{
    margin:5px 0;
    background:#e5e7eb;
}

/* CONTENT */
.content{
    flex:1;
    padding:20px;
}
.card{
    background:white;
    padding:20px;
    border-radius:10px;
    margin-bottom:15px;
}

/* WALLET */
.wallet{
    font-size:22px;
    font-weight:bold;
}

/* STATUS */
.success{ color:green; }
.fail{ color:red; }

/* TABLE */
table{
    width:100%;
    border-collapse:collapse;
}
td,th{
    padding:10px;
    border-bottom:1px solid #ccc;
}

/* FOOTER */
.footer{
    text-align:center;
    padding:10px;
    color:gray;
}

/* MOBILE */
@media(max-width:768px){
    .main{flex-direction:column;}
    .sidebar{width:100%;display:flex;overflow:auto;}
    .sidebar button{flex:1;}
}
</style>

</head>

<body>

<!-- LOGIN -->
<div id="login">
<div class="box">
    <div class="logo">MJStacks</div>
    <input id="user" placeholder="Username">
    <input id="pass" type="password" placeholder="Password">
    <button class="login-btn" onclick="loginUser()">Login</button>
</div>
</div>

<!-- APP -->
<div id="app">

<div class="header">💳 MJStacks Gateway</div>

<div class="main">

<!-- SIDEBAR -->
<div class="sidebar">
    <button onclick="showPage('dashboard')">Dashboard</button>
    <button onclick="showPage('upi')">UPI</button>
    <button onclick="showPage('bank')">Bank</button>
    <button onclick="showPage('history')">Transactions</button>
</div>

<!-- CONTENT -->
<div class="content">

<!-- DASHBOARD -->
<div id="dashboard" class="card">
    <div class="wallet">Wallet Balance: <span id="wallet">₹300000</span></div>
</div>

<!-- UPI -->
<div id="upi" class="card" style="display:none;">
    <h3>UPI Payment</h3>
    <input id="upiAmt" placeholder="Amount">

    <button onclick="upiPay()">Pay</button>
    <button onclick="upiInvalid()" style="background:red;color:white;">Invalid UPI</button>

    <h4 id="upiMsg"></h4>
</div>

<!-- BANK -->
<div id="bank" class="card" style="display:none;">
    <h3>Bank Payment</h3>
    <input id="amt" placeholder="Amount">

    <button onclick="bankPay()">Pay</button>
    <button onclick="bankInvalid()" style="background:red;color:white;">Invalid Bank</button>

    <h4 id="bankMsg"></h4>
</div>

<!-- HISTORY -->
<div id="history" class="card" style="display:none;">
    <h3>Transaction History</h3>
    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Type</th>
                <th>Status</th>
                <th>Amount</th>
                <th>Date</th>
            </tr>
        </thead>
        <tbody id="txnTable"></tbody>
    </table>
</div>

</div>
</div>

<div class="footer">Connected by MJStacks Game</div>

</div>

<script>

/* WALLET */
let wallet = 300000;

/* UPDATE WALLET */
function updateWallet(){
    document.getElementById("wallet").innerText = "₹" + wallet;
}

/* LOGIN */
function loginUser(){
    let u = document.getElementById("user").value.trim();
    let p = document.getElementById("pass").value.trim();

    if(u==="SUROJIT4545" && p==="SUROJIT4545"){
        document.getElementById("login").style.display="none";
        document.getElementById("app").style.display="block";
        loadFakeTransactions();
        updateWallet();
    } else {
        alert("Wrong Username/Password");
    }
}

/* NAV */
function showPage(page){
    document.querySelectorAll(".card").forEach(e=>e.style.display="none");
    document.getElementById(page).style.display="block";
}

/* ADD TXN */
function addTransaction(type,status,amt){
    let table = document.getElementById("txnTable");
    let row = document.createElement("tr");
    let date = new Date().toLocaleString();
    let color = status==="Success"?"green":"red";

    row.innerHTML = `
        <td>#TXN${Math.floor(Math.random()*10000)}</td>
        <td>${type}</td>
        <td style="color:${color}">${status}</td>
        <td>₹${amt}</td>
        <td>${date}</td>
    `;
    table.prepend(row);
}

/* LOADING */
function loading(box,callback){
    box.innerHTML="⏳ Processing...";
    setTimeout(callback,1200);
}

/* UPI */
function upiPay(){
    let amt = document.getElementById("upiAmt").value || 0;

    loading(upiMsg,()=>{
        wallet -= amt;
        updateWallet();
        upiMsg.innerHTML="<span class='success'>✅ Payment Successful</span>";
        addTransaction("UPI","Success",amt);
    });
}
function upiInvalid(){
    let amt = document.getElementById("upiAmt").value || 0;

    loading(upiMsg,()=>{
        upiMsg.innerHTML="<span class='fail'>❌ Invalid UPI</span>";
        addTransaction("UPI","Failed",amt);
    });
}

/* BANK */
function bankPay(){
    let amt = document.getElementById("amt").value || 0;

    loading(bankMsg,()=>{
        wallet -= amt;
        updateWallet();
        bankMsg.innerHTML="<span class='success'>✅ Payment Successful</span>";
        addTransaction("Bank","Success",amt);
    });
}
function bankInvalid(){
    let amt = document.getElementById("amt").value || 0;

    loading(bankMsg,()=>{
        bankMsg.innerHTML="<span class='fail'>❌ Invalid Bank</span>";
        addTransaction("Bank","Failed",amt);
    });
}

/* FAKE HISTORY */
function loadFakeTransactions(){
    let data=[
        {id:"#TXN1001",type:"UPI",status:"Success",amt:"500"},
        {id:"#TXN1002",type:"Bank",status:"Failed",amt:"1200"}
    ];

    data.forEach(t=>{
        addTransaction(t.type,t.status,t.amt);
    });
}

</script>

</body>
</html>
