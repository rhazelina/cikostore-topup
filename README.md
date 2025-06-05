
# Chiko Store - Topup Website based

Kalian mw Topup murah dan secepat angin dari yaman? ya di Chiko Store 😋


## Tech Stack

**Frontend:** React Vites + TailwindCSS

**Server:** Laravel + Blade



#  API – Mock Server [ EXAMPLE ]

Mock API untuk simulasi layanan top-up pulsa, paket data, dan voucher game. Berguna untuk testing frontend atau integrasi sistem.

---

## API Reference

### Get all items

```http
GET /api/items
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `api_key` | `string` | **Required**. API key |

---

### Get item by ID

```http
GET /api/items/{id}
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `id`      | `string` | **Required**. ID item yang dicari |
| `api_key` | `string` | **Required**. API key        |

---

### Create transaction (Top-Up)

```http
POST /api/transactions
```

| Body Parameter | Type     | Description                       |
| :------------- | :------- | :-------------------------------- |
| `api_key`      | `string` | **Required**. API key        |
| `item_id`      | `string` | **Required**. ID item yang dibeli |
| `target`       | `string` | **Required**. Nomor/ID tujuan     |

---

### Check transaction status

```http
GET /api/transactions/{transaction_id}
```

| Parameter        | Type     | Description                |
| :--------------- | :------- | :------------------------- |
| `transaction_id` | `string` | **Required**. ID transaksi |
| `api_key`        | `string` | **Required**. API key  |

---

### Add (Utility/Test Only)

```http
GET /api/add?num1=10&num2=5
```

| Query Parameter | Type     | Description                 |
| :-------------- | :------- | :-------------------------- |
| `num1`          | `number` | **Required**. Angka pertama |
| `num2`          | `number` | **Required**. Angka kedua   |

---

## Menjalankan Mock Server

### 1. Instalasi

Pastikan Anda sudah menginstal Node.js. Lalu jalankan:

```bash
npm install
```

### 2. Jalankan Server

```bash
node index.js
```

Server akan berjalan di `http://localhost:3000`.

---

## index.js

```js
const express = require('express');
const app = express();
app.use(express.json());

const items = [
  { id: 'pulsa_10k', name: 'Pulsa Telkomsel 10K', price: 10500 },
  { id: 'game_ff_140', name: 'Diamond Free Fire 140', price: 17000 }
];

const transactions = {};

app.get('/api/items', (req, res) => {
  if (!req.query.api_key) return res.status(401).json({ error: 'API key required' });
  res.json(items);
});

app.get('/api/items/:id', (req, res) => {
  if (!req.query.api_key) return res.status(401).json({ error: 'API key required' });
  const item = items.find(i => i.id === req.params.id);
  if (!item) return res.status(404).json({ error: 'Item not found' });
  res.json(item);
});

app.post('/api/transactions', (req, res) => {
  const { api_key, item_id, target } = req.body;
  if (!api_key || !item_id || !target) {
    return res.status(400).json({ error: 'Missing required fields' });
  }
  const transaction_id = 'trx' + Date.now();
  transactions[transaction_id] = {
    transaction_id,
    status: 'pending',
    item_id,
    target
  };
  res.json({
    transaction_id,
    status: 'pending',
    message: 'Transaksi sedang diproses'
  });
});

app.get('/api/transactions/:id', (req, res) => {
  if (!req.query.api_key) return res.status(401).json({ error: 'API key required' });
  const trx = transactions[req.params.id];
  if (!trx) return res.status(404).json({ error: 'Transaksi tidak ditemukan' });
  res.json({ ...trx, status: 'success', message: 'Top-up berhasil' });
});

app.get('/api/add', (req, res) => {
  const num1 = Number(req.query.num1);
  const num2 = Number(req.query.num2);
  if (isNaN(num1) || isNaN(num2)) return res.status(400).json({ error: 'Invalid numbers' });
  res.json({ result: num1 + num2 });
});

app.listen(3000, () => console.log('Mock API running on http://localhost:3000'));
```

---

