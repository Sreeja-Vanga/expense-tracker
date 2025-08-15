# expense-tracker
A simple HTML + JavaScript expense tracker
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Smart Expense Tracker</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background: #f7f7f7;
        margin: 0;
        padding: 0;
    }
    .container {
        max-width: 800px;
        margin: auto;
        padding: 20px;
        background: white;
        box-shadow: 0 0 10px rgba(0,0,0,0.1);
        border-radius: 10px;
        margin-top: 20px;
    }
    h1 {
        text-align: center;
        color: #333;
    }
    input, select, button {
        padding: 8px;
        margin: 5px;
        width: calc(50% - 20px);
    }
    button {
        background-color: #28a745;
        color: white;
        border: none;
        cursor: pointer;
        border-radius: 5px;
    }
    button:hover {
        background-color: #218838;
    }
    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 15px;
    }
    table, th, td {
        border: 1px solid #ddd;
    }
    th, td {
        padding: 8px;
        text-align: center;
    }
    .summary {
        background: #e9ecef;
        padding: 10px;
        border-radius: 5px;
        margin-top: 10px;
    }
</style>
</head>
<body>
<div class="container">
    <h1>Smart Expense Tracker 💰</h1>
    <div>
        <input type="text" id="name" placeholder="Expense Name">
        <select id="category">
            <option>Food</option>
            <option>Travel</option>
            <option>Shopping</option>
            <option>Others</option>
        </select>
        <input type="number" id="amount" placeholder="Amount">
        <input type="date" id="date">
        <button onclick="addExpense()">Add Expense</button>
    </div>

    <div class="summary" id="summary">
        <h3>📊 Summary</h3>
        <p>Total: ₹ 0</p>
    </div>

    <h3>📜 Expense List</h3>
    <table id="expenseTable">
        <tr>
            <th>Date</th>
            <th>Name</th>
            <th>Category</th>
            <th>Amount</th>
        </tr>
    </table>
    <button onclick="downloadCSV()">Download as CSV</button>
</div>

<script>
let expenses = JSON.parse(localStorage.getItem("expenses")) || [];

function renderTable() {
    let table = document.getElementById("expenseTable");
    table.innerHTML = `
        <tr>
            <th>Date</th>
            <th>Name</th>
            <th>Category</th>
            <th>Amount</th>
        </tr>
    `;
    let total = 0;
    let categoryTotals = {};

    expenses.forEach(exp => {
        let row = `<tr>
            <td>${exp.date}</td>
            <td>${exp.name}</td>
            <td>${exp.category}</td>
            <td>₹ ${exp.amount}</td>
        </tr>`;
        table.innerHTML += row;
        total += parseFloat(exp.amount);
        categoryTotals[exp.category] = (categoryTotals[exp.category] || 0) + parseFloat(exp.amount);
    });

    let summaryHTML = `<h3>📊 Summary</h3><p>Total: ₹ ${total.toFixed(2)}</p>`;
    for (let cat in categoryTotals) {
        summaryHTML += `<p>${cat}: ₹ ${categoryTotals[cat].toFixed(2)}</p>`;
    }
    document.getElementById("summary").innerHTML = summaryHTML;

    localStorage.setItem("expenses", JSON.stringify(expenses));
}

function addExpense() {
    let name = document.getElementById("name").value;
    let category = document.getElementById("category").value;
    let amount = document.getElementById("amount").value;
    let date = document.getElementById("date").value;

    if (!name || !amount || !date) {
        alert("Please fill all fields");
        return;
    }

    expenses.push({ name, category, amount, date });
    renderTable();

    document.getElementById("name").value = "";
    document.getElementById("amount").value = "";
    document.getElementById("date").value = "";
}

function downloadCSV() {
    let csv = "Date,Name,Category,Amount\n";
    expenses.forEach(exp => {
        csv += `${exp.date},${exp.name},${exp.category},${exp.amount}\n`;
    });
    let blob = new Blob([csv], { type: 'text/csv' });
    let link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = "expenses.csv";
    link.click();
}

renderTable();
</script>
</body>
</html>
