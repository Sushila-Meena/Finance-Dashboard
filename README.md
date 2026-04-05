# Finance-Dashboard
Finance Dashboard UI  A responsive and interactive finance dashboard built with React, Tailwind CSS, and Recharts. This project demonstrates frontend development skills by providing a clean interface to track personal financial activity, explore transactions, visualize spending patterns, and simulate role-based interactions. 

##Project Folder Structure

finance-dashboard/
├─ node_modules/
├─ public/
│  └─ index.html
├─ src/
│  ├─ components/
│  │  ├─ Charts.jsx
│  │  ├─ Insights.jsx
│  │  ├─ RoleSwitcher.jsx
│  │  ├─ SummaryCards.jsx
│  │  └─ Transactions.jsx
│  ├─ context/
│  │  └─ AppContext.jsx
│  ├─ pages/
│  │  └─ Dashboard.jsx
│  └─ index.css
├─ package.json
├─ tailwind.config.js
├─ postcss.config.cjs
├─ vite.config.js
└─ README.md




 ## Dashboard Overview
Summary cards for Total Balance, Income, and Expenses
Line chart showing balance trends
Pie chart showing spending breakdown by category
Transactions
View all transactions with date, category, type, and amount
Admin role can add new transactions
Simple search and filtering
Role-Based UI
Viewer: Can only view data
Admin: Can view and add transactions
Easily switch roles using the dropdown for demonstration
Insights Section
Highlights highest spending category
Monthly comparisons and other key observations
Responsive Design
Works on mobile, tablet, and desktop
Clean, modern UI built with Tailwind CSS
State Management
Managed using React Context
Handles transactions, filters, and selected roles
Charts & Visualization
Interactive line chart for trends
Interactive pie chart for category breakdown
Built using Recharts
Tech Stack
Frontend: React, JSX
Styling: Tailwind CSS
Charts: Recharts
State Management: React Context
Bundler: Vite


 ## Setup Instructions
Clone the repository:
git clone https://github.com/yourusername/finance-dashboard.git
cd finance-dashboard
Install dependencies:
npm install
Run the development server:
npm run dev
Open the browser at the URL shown (e.g., http://localhost:5179)



## Project Screenshot
<img width="1895" height="906" alt="Screenshot 2026-04-06 015632" src="https://github.com/user-attachments/assets/2ee41814-996d-4bca-acc0-adeef196773e" />
<img width="1907" height="913" alt="Screenshot 2026-04-06 015744" src="https://github.com/user-attachments/assets/b15a8395-980e-4118-9258-9ba1adefae2a" />


## Code section 

Step 1: App Context (src/context/AppContext.jsx)
import React, { createContext, useContext, useState } from "react";

const AppContext = createContext();

export const AppProvider = ({ children }) => {
  const [role, setRole] = useState("Viewer");
  const [transactions, setTransactions] = useState([
    { date: "2026-04-01", category: "Salary", type: "income", amount: 2000 },
    { date: "2026-04-02", category: "Food", type: "expense", amount: 500 },
    { date: "2026-04-03", category: "Rent", type: "expense", amount: 1000 },
  ]);

  return (
    <AppContext.Provider value={{ role, setRole, transactions, setTransactions }}>
      {children}
    </AppContext.Provider>
  );
};

export const useAppContext = () => useContext(AppContext);

Step 2: RoleSwitcher (src/components/RoleSwitcher.jsx)
import React from "react";
import { useAppContext } from "../context/AppContext";

const RoleSwitcher = () => {
  const { role, setRole } = useAppContext();
  return (
    <div className="mb-4">
      <span className="font-bold mr-2">Role:</span>
      <select
        value={role}
        onChange={(e) => setRole(e.target.value)}
        className="border rounded p-1"
      >
        <option value="Viewer">Viewer</option>
        <option value="Admin">Admin</option>
      </select>
    </div>
  );
};

export default RoleSwitcher;

Step 3: SummaryCards (src/components/SummaryCards.jsx)
import React from "react";
import { useAppContext } from "../context/AppContext";

const SummaryCards = () => {
  const { transactions } = useAppContext();

  const income = transactions
    .filter((t) => t.type === "income")
    .reduce((a, b) => a + b.amount, 0);
  const expenses = transactions
    .filter((t) => t.type === "expense")
    .reduce((a, b) => a + b.amount, 0);
  const balance = income - expenses;

  const cards = [
    { title: "Total Balance", value: balance },
    { title: "Income", value: income },
    { title: "Expenses", value: expenses },
  ];

  return (
    <div className="grid grid-cols-3 gap-4 mb-6">
      {cards.map((card) => (
        <div key={card.title} className="p-4 bg-white shadow rounded">
          <h3 className="text-gray-500">{card.title}</h3>
          <p className="text-xl font-bold">${card.value}</p>
        </div>
      ))}
    </div>
  );
};

export default SummaryCards;

Step 4: Charts (src/components/Charts.jsx)

(Line chart + Pie chart with Recharts)

import React from "react";
import { useAppContext } from "../context/AppContext";
import {
  LineChart,
  Line,
  XAxis,
  YAxis,
  CartesianGrid,
  Tooltip,
  PieChart,
  Pie,
  Cell,
  Legend,
} from "recharts";

const Charts = () => {
  const { transactions } = useAppContext();

  const lineData = [];
  const dates = [...new Set(transactions.map(t => t.date))];
  dates.forEach(date => {
    const income = transactions.filter(t => t.type === "income" && t.date === date)
      .reduce((a, b) => a + b.amount, 0);
    const expense = transactions.filter(t => t.type === "expense" && t.date === date)
      .reduce((a, b) => a + b.amount, 0);
    lineData.push({ date, Income: income, Expense: expense });
  });

  const expenseCategories = {};
  transactions.filter(t => t.type === "expense").forEach(t => {
    if (expenseCategories[t.category]) expenseCategories[t.category] += t.amount;
    else expenseCategories[t.category] = t.amount;
  });
  const pieData = Object.keys(expenseCategories).map(key => ({ name: key, value: expenseCategories[key] }));
  const COLORS = ["#0088FE", "#00C49F", "#FF8042", "#FFBB28"];

  return (
    <div className="grid grid-cols-2 gap-4 mb-6">
      <div className="p-4 bg-white shadow rounded">
        <h3 className="text-lg font-bold mb-2">Income vs Expenses</h3>
        <LineChart width={400} height={250} data={lineData}>
          <CartesianGrid strokeDasharray="3 3" />
          <XAxis dataKey="date" />
          <YAxis />
          <Tooltip />
          <Line type="monotone" dataKey="Income" stroke="#00C49F" />
          <Line type="monotone" dataKey="Expense" stroke="#FF8042" />
        </LineChart>
      </div>
      <div className="p-4 bg-white shadow rounded">
        <h3 className="text-lg font-bold mb-2">Expenses by Category</h3>
        <PieChart width={400} height={250}>
          <Pie
            data={pieData}
            dataKey="value"
            nameKey="name"
            cx="50%"
            cy="50%"
            outerRadius={80}
            fill="#8884d8"
            label
          >
            {pieData.map((entry, index) => <Cell key={index} fill={COLORS[index % COLORS.length]} />)}
          </Pie>
          <Legend />
          <Tooltip />
        </PieChart>
      </div>
    </div>
  );
};

export default Charts;


Step 5: Transactions (src/components/Transactions.jsx)

(Admin can add transactions, Viewer read-only)

import React, { useState } from "react";
import { useAppContext } from "../context/AppContext";

const Transactions = () => {
  const { transactions, setTransactions, role } = useAppContext();
  const [newTransaction, setNewTransaction] = useState({
    date: "",
    category: "",
    type: "income",
    amount: 0,
  });

  const handleAdd = () => {
    if (!newTransaction.date || !newTransaction.category || newTransaction.amount <= 0) return;
    setTransactions([...transactions, { ...newTransaction, amount: Number(newTransaction.amount) }]);
    setNewTransaction({ date: "", category: "", type: "income", amount: 0 });
  };

  return (
    <div className="p-4 bg-white shadow rounded mb-6">
      <h2 className="text-lg font-bold mb-2">Transactions</h2>
      
      {role === "Admin" && (
        <div className="mb-4 flex gap-2">
          <input type="date" value={newTransaction.date} onChange={(e) => setNewTransaction({...newTransaction, date: e.target.value})} className="border rounded p-1"/>
          <input type="text" placeholder="Category" value={newTransaction.category} onChange={(e) => setNewTransaction({...newTransaction, category: e.target.value})} className="border rounded p-1"/>
          <select value={newTransaction.type} onChange={(e) => setNewTransaction({...newTransaction, type: e.target.value})} className="border rounded p-1">
            <option value="income">Income</option>
            <option value="expense">Expense</option>
          </select>
          <input type="number" placeholder="Amount" value={newTransaction.amount} onChange={(e) => setNewTransaction({...newTransaction, amount: e.target.value})} className="border rounded p-1"/>
          <button onClick={handleAdd} className="bg-blue-500 text-white px-3 rounded">Add</button>
        </div>
      )}

      <table className="w-full text-left border-collapse">
        <thead>
          <tr>
            <th className="border-b p-2">Date</th>
            <th className="border-b p-2">Category</th>
            <th className="border-b p-2">Type</th>
            <th className="border-b p-2">Amount</th>
          </tr>
        </thead>
        <tbody>
          {transactions.map((t, i) => (
            <tr key={i}>
              <td className="border-b p-2">{t.date}</td>
              <td className="border-b p-2">{t.category}</td>
              <td className="border-b p-2">{t.type}</td>
              <td className="border-b p-2">${t.amount}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
};

export default Transactions;

Step 6: Insights (src/components/Insights.jsx)
import React from "react";
import { useAppContext } from "../context/AppContext";

const Insights = () => {
  const { transactions } = useAppContext();
  const expenseTransactions = transactions.filter(t => t.type === "expense");
  const highest = expenseTransactions.reduce((max, t) => t.amount > max.amount ? t : max, { amount: 0, category: "" });

  return (
    <div className="p-4 bg-white shadow rounded">
      <h2 className="text-lg font-bold mb-2">Insights</h2>
      <p>Highest Spending Category: {highest.category}</p>
    </div>
  );
};

export default Insights;

Step 6: Insights (src/components/Insights.jsx)
import React from "react";
import { useAppContext } from "../context/AppContext";

const Insights = () => {
  const { transactions } = useAppContext();
  const expenseTransactions = transactions.filter(t => t.type === "expense");
  const highest = expenseTransactions.reduce((max, t) => t.amount > max.amount ? t : max, { amount: 0, category: "" });

  return (
    <div className="p-4 bg-white shadow rounded">
      <h2 className="text-lg font-bold mb-2">Insights</h2>
      <p>Highest Spending Category: {highest.category}</p>
    </div>
  );
};

export default Insights;

Step 8: index.css
@tailwind base;
@tailwind components;
@tailwind utilities;

Step 9: tailwind.config.js
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html","./src/**/*.{js,jsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};

Step 10: postcss.config.cjs (important for ES module)
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};

Step 11: package.json

{
  "name": "finance-dashboard",
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "recharts": "^2.8.0"
  },
  "devDependencies": {
    "autoprefixer": "^10.4.14",
    "postcss": "^8.4.30",
    "tailwindcss": "^3.3.3",
    "vite": "^4.4.9"
  }
}

Step 12: Start Dev Server
npm install
npm run dev

Open http://localhost:5179/ →  I have:

Real charts
Admin role can add transactions
Viewer role read-only
Fully responsive Tailwind Dashboard
Insights auto-update




 ## Role Simulation
Use the Role Switcher dropdown to switch between Viewer and Admin roles
Admin can add transactions; Viewer can only view data




