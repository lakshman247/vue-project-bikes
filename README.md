# Vue 3 + Vite
# 🚀 Two-Wheeler Vehicle Query App

This is a frontend application built with **Vue 3**, **Tailwind CSS**, and **Prime vue**. It allows users to query and filter two-wheeler vehicle data using natural search input and curd operations. The data is served locally via `db.json` using `json-server`.

---

## ✨ Features

- 🔍 Intelligent, query-based search for bikes and scooters  
- 🎨 Clean and modern UI with Tailwind CSS and Material UI  
- ⚡ Fast and smooth user experience with loading skeletons  
- 🧠 Handles queries for color, company, mileage, engine capacity, manufacture year, and price  
- 📂 Graceful fallback with “No records found” if nothing matches  
- 🔌 Runs fully locally using `json-server` – no backend required  

---

## 📦 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/lakshman247/vue-project-bikes.git
cd vue-project-bikes
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Start the App

Start both the frontend and JSON server:

```bash
npm run dev
```

Alternatively, run JSON Server separately:

```bash
npx json-server --watch db.json --port 3001
```

---

## 📡 API Endpoint

```bash
http://localhost:3001/bikes
```

---

## 💡 How It Works

- User enters a natural company name as query (e.g., "YAMAHA")
- The app parses the query for:
  - 🏍️ Company
- Matching records are filtered and displayed dynamically
- Skeleton loaders appear while fetching results
- Displays a "No data available" message if no results are found
- when input feild doesn't have content no call will happen
- when user want to see all list added reset button
- added toaster for better enduser understanding
- Giving options to user Add new bike, edit bike, fetch all bikes and delete bike

---

## 📁 Project Structure

```
src/
├── components/
│   └── AddBike.vue     # child component
├── App.vue             # main component
├── index.js            # entry point
└── db.json             # Sample bike data (used by json-server)
```

---

## 🛠 Tech Stack

- Vue 3
- Tailwind CSS
- Prime vue
- JSON Server
- HTML
- JavaScript
---


---

**Made with ❤️ by Lakshumaiah**








