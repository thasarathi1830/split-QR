# SplitQR

### Split a large payment amount into smaller QR-based payment requests — all displayed on one page.

SplitQR is a lightweight full-stack utility that takes a large payment amount, divides it into configurable smaller chunks, and generates individual payment QR codes for each chunk.

For example, entering **₹75,000** with a maximum chunk size of **₹1,999** produces:

```text
₹1,999 × 37 = ₹73,963
Remaining = ₹1,037

Total = ₹75,000
QRs generated = 38
```

All generated QR codes are displayed together on a single responsive page, making the payment process simple and organized.

---

## 🚀 Features

* 💰 Enter any total payment amount
* ✂️ Automatically split the amount into smaller chunks
* ⚙️ Configurable maximum amount per QR
* 📱 Generate multiple payment QR codes
* 🖥️ Display all QR codes on a single page
* 🔢 Automatic calculation of the final remaining amount
* 💳 Razorpay integration for supported payment/QR workflows
* ⚡ FastAPI backend
* ⚛️ React frontend
* 📱 Responsive UI
* 🔐 Razorpay secret credentials kept securely on the backend

---

## 🏗️ Architecture

```text
              ┌─────────────────────┐
              │    React Frontend   │
              │                     │
              │  Enter Total Amount │
              └──────────┬──────────┘
                         │
                         │ POST /api/split
                         ▼
              ┌─────────────────────┐
              │   FastAPI Backend   │
              │                     │
              │  Amount Validation  │
              │        ↓            │
              │  Splitter Service   │
              │        ↓            │
              │   QR/Payment        │
              │     Service         │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │      Razorpay       │
              │ Payment / QR APIs   │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │   QR Information    │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │    React QR Grid    │
              │                     │
              │ QR1 QR2 QR3 ... QR38│
              └─────────────────────┘
```

---

## 🧮 How It Works

Suppose the user enters:

```text
Total Amount = ₹75,000
Maximum Per QR = ₹1,999
```

The backend calculates:

```text
75,000 ÷ 1,999
```

The result is:

```text
37 complete chunks
1 remaining chunk
```

Therefore:

```text
37 × ₹1,999 = ₹73,963

₹75,000 - ₹73,963 = ₹1,037
```

Final result:

```text
37 × ₹1,999
+
1 × ₹1,037

= ₹75,000
```

The application then generates **38 individual payment QR entries**.

The splitting algorithm guarantees:

```text
SUM(all chunks) = original amount
```

Currency calculations are handled using integer values to avoid floating-point rounding issues.

---

## 🛠️ Tech Stack

### Frontend

* React
* Vite
* Tailwind CSS
* QR Code library

### Backend

* Python
* FastAPI
* Pydantic
* Razorpay SDK/API

### Development

* Git
* GitHub
* REST API
* Swagger / OpenAPI

---

## 📂 Project Structure

```text
SplitQR/
│
├── backend/
│   ├── main.py
│   ├── schemas.py
│   │
│   ├── routes/
│   │   └── split.py
│   │
│   ├── services/
│   │   ├── splitter.py
│   │   ├── razorpay_service.py
│   │   └── qr_service.py
│   │
│   ├── requirements.txt
│   └── .env.example
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── AmountForm.jsx
│   │   │   ├── Summary.jsx
│   │   │   ├── QRCard.jsx
│   │   │   └── QRGrid.jsx
│   │   │
│   │   ├── pages/
│   │   │   ├── Home.jsx
│   │   │   └── Batch.jsx
│   │   │
│   │   ├── services/
│   │   │   └── api.js
│   │   │
│   │   ├── App.jsx
│   │   └── main.jsx
│   │
│   └── package.json
│
└── README.md
```

---

## 🔌 API

### Split Amount

```http
POST /api/split
```

### Request

```json
{
  "amount": 75000,
  "max_chunk": 1999
}
```

### Response

```json
{
  "total_amount": 75000,
  "max_chunk": 1999,
  "number_of_qrs": 38,
  "chunks": [
    {
      "number": 1,
      "amount": 1999
    },
    {
      "number": 2,
      "amount": 1999
    },
    {
      "number": 3,
      "amount": 1999
    }
  ]
}
```

The actual response contains all generated chunks and their corresponding payment/QR information.

---

## ⚙️ Installation

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/splitqr.git

cd splitqr
```

### 2. Backend

```bash
cd backend

python -m venv venv
```

Activate the environment.

Windows:

```bash
venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Create `.env`:

```env
RAZORPAY_KEY_ID=your_key_id
RAZORPAY_KEY_SECRET=your_key_secret
```

Run FastAPI:

```bash
uvicorn main:app --reload
```

Backend will be available at:

```text
http://127.0.0.1:8000
```

Swagger documentation:

```text
http://127.0.0.1:8000/docs
```

---

### 3. Frontend

Open another terminal:

```bash
cd frontend

npm install

npm run dev
```

The frontend will be available at the Vite development URL shown in the terminal.

---

## 🔐 Environment Variables

Never commit your Razorpay secret credentials.

Create a `.env` file:

```env
RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
```

Add `.env` to `.gitignore`.

Only the public Razorpay key, where required by the integration, may be exposed to the frontend.

---

## 🧪 Example Test Cases

|  Amount | Max Per QR | Number of QRs |
| ------: | ---------: | ------------: |
|  ₹1,000 |     ₹1,999 |             1 |
|  ₹1,999 |     ₹1,999 |             1 |
|  ₹2,000 |     ₹1,999 |             2 |
|  ₹3,000 |     ₹1,999 |             2 |
| ₹10,000 |     ₹1,999 |             6 |
| ₹75,000 |     ₹1,999 |            38 |

---

## 🎯 Why I Built This

SplitQR was built as a focused backend-oriented project to explore:

* Payment API integration
* QR-based payment workflows
* REST API development
* Financial amount calculations
* Backend validation
* Service-based architecture
* React ↔ FastAPI communication
* Handling multiple generated payment requests

The project intentionally keeps the infrastructure lightweight and focuses on solving one specific problem well.

---

## 🔮 Future Improvements

Possible future additions:

* Payment status tracking
* QR-level success indicators
* Batch payment progress
* Download/print QR sheet
* QR regeneration
* Payment history
* Authentication
* Database persistence
* Webhook-based payment confirmation
* Analytics dashboard

These features are intentionally outside the initial MVP.

---

## ⚠️ Disclaimer

SplitQR is a technical/educational project demonstrating payment splitting and QR generation.

Actual payment and QR functionality depends on the payment provider's supported APIs, merchant configuration, applicable limits, fees, terms, and regulatory requirements.

The application should not be used to circumvent payment-network restrictions, fees, KYC requirements, transaction limits, or applicable laws.

---

## 👨‍💻 Author

**Thasarathi**

Built with React + FastAPI + Razorpay.
