# basicWS
// FRONTEND: React.js (client/src/App.js)

import React, { useState, useEffect } from 'react';
import axios from 'axios';
import './App.css';
import "./modern.css"; // Modern styling file

function App() {
  const [transactions, setTransactions] = useState([]);
  const [income, setIncome] = useState(0);
  const [expense, setExpense] = useState(0);
  const [description, setDescription] = useState('');
  const [amount, setAmount] = useState('');

  useEffect(() => {
    axios.get('/api/transactions').then((response) => {
      setTransactions(response.data);
      calculateTotals(response.data);
    });
  }, []);

  const calculateTotals = (transactions) => {
    let totalIncome = 0;
    let totalExpense = 0;
    transactions.forEach((item) => {
      if (item.amount > 0) totalIncome += item.amount;
      else totalExpense += Math.abs(item.amount);
    });
    setIncome(totalIncome);
    setExpense(totalExpense);
  };

  const addTransaction = async (e) => {
    e.preventDefault();
    const newTransaction = { description, amount: parseFloat(amount) };
    const response = await axios.post('/api/transactions', newTransaction);
    setTransactions([...transactions, response.data]);
    calculateTotals([...transactions, response.data]);
    setDescription('');
    setAmount('');
  };

  return (
    <div className="App">
      <header className="app-header">
        <h1>PocketPal</h1>
        <p>Your smart pocket money manager</p>
      </header>

      <main className="app-main">
        <section className="summary">
          <h2>Summary</h2>
          <div className="summary-cards">
            <div className="card income-card">
              <p>Total Income</p>
              <h3>₹{income}</h3>
            </div>
            <div className="card expense-card">
              <p>Total Expenses</p>
              <h3>₹{expense}</h3>
            </div>
            <div className="card balance-card">
              <p>Balance</p>
              <h3>₹{income - expense}</h3>
            </div>
          </div>
        </section>

        <section className="transactions">
          <h2>Recent Transactions</h2>
          <ul className="transaction-list">
            {transactions.map((item, index) => (
              <li key={index} className={item.amount > 0 ? "transaction-income" : "transaction-expense"}>
                {item.description}
                <span>₹{item.amount}</span>
              </li>
            ))}
          </ul>
        </section>

        <section className="add-transaction">
          <h2>Add Transaction</h2>
          <form className="transaction-form" onSubmit={addTransaction}>
            <input
              type="text"
              className="form-input"
              placeholder="Description"
              value={description}
              onChange={(e) => setDescription(e.target.value)}
              required
            />
            <input
              type="number"
              className="form-input"
              placeholder="Amount (+ for income, - for expense)"
              value={amount}
              onChange={(e) => setAmount(e.target.value)}
              required
            />
            <button type="submit" className="form-button">Add Transaction</button>
          </form>
        </section>
      </main>
    </div>
  );
}

export default App;

// MODERN.CSS: modern design for a high-class frontend

body {
  font-family: 'Poppins', sans-serif;
  margin: 0;
  background-color: #f4f4f9;
  color: #333;
}

.app-header {
  background: linear-gradient(135deg, #6c63ff, #8f94fb);
  padding: 20px;
  color: white;
  text-align: center;
  box-shadow: 0px 2px 10px rgba(0, 0, 0, 0.1);
}

.app-header h1 {
  font-size: 2.5rem;
  margin: 0;
}

.app-main {
  padding: 20px;
}

.summary {
  margin-bottom: 20px;
}

.summary-cards {
  display: flex;
  gap: 20px;
}

.card {
  background: white;
  border-radius: 10px;
  box-shadow: 0px 2px 10px rgba(0, 0, 0, 0.1);
  padding: 15px;
  text-align: center;
  flex: 1;
}

.card h3 {
  margin: 10px 0 0;
  font-size: 1.8rem;
  color: #6c63ff;
}

.transaction-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.transaction-list li {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  background: white;
  margin-bottom: 10px;
  border-radius: 5px;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
}

.transaction-income span {
  color: green;
}

.transaction-expense span {
  color: red;
}

.transaction-form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.form-input {
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ddd;
}

.form-button {
  padding: 10px;
  border: none;
  border-radius: 5px;
  background: #6c63ff;
  color: white;
  font-size: 1rem;
  cursor: pointer;
}

.form-button:hover {
  background: #574bff;
}
