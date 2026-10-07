# UPI Without Internet

### Offline UPI Payment Simulation Using a Bluetooth Mesh Network

A simulation of an offline digital payment system that enables transactions to propagate through a **device-to-device mesh network** without requiring continuous internet connectivity.

The project demonstrates how an offline payment can be created on a virtual device, propagated through neighboring devices using a gossip mechanism, delivered to a bridge device, and finally synchronized with a backend for verification and settlement.

> This project is an adaptation and extension of an existing offline-payment mesh concept. The frontend and project-level modifications in this repository are maintained by Aryan Yash.

---

## 🚀 Overview

Traditional UPI payments rely on an active internet connection to communicate with banking infrastructure.

This project explores an alternative approach where payment instructions can initially travel through a network of nearby devices without requiring every device to have internet access.

The system simulates:

```text
Sender Device
      │
      ▼
Offline Payment
      │
      ▼
Mesh Network
      │
      ▼
Gossip Propagation
      │
      ▼
Bridge Device
      │
      ▼
Backend Server
      │
      ▼
Verification & Settlement
      │
      ▼
Transaction Ledger
```

---

## ✨ Features

- **Offline payment creation**: compose a payment on a simulated sender phone with no connectivity.
- **Encrypted packets**: each payment is encrypted before it enters the mesh, so relaying devices cannot read it.
- **Gossip propagation**: packets hop from device to device, limited by a TTL (time to live).
- **Bridge nodes**: devices with 4G connectivity upload the packets they hold to the backend.
- **Idempotent settlement**: if several bridges upload the same packet, the backend settles it only once.
- **Live dashboard**: watch devices, packets, balances and the ledger update in real time.
- **Activity log**: every injection, gossip round and bridge upload is recorded step by step.

---

## 🧭 How It Works

| Step | What happens |
|------|--------------|
| **1. Compose** | A sender creates a payment (sender VPA, receiver VPA, amount, PIN). The payload is encrypted into a packet and injected into the mesh at the sender's device. |
| **2. Gossip** | In each gossip round, devices share the packets they hold with neighbours. Each hop reduces the packet's TTL, and the hop count is tracked. |
| **3. Bridge upload** | A device that gains internet access (a *bridge*) uploads its packets to the backend. Several bridges may upload the same packet at once. |
| **4. Verify and settle** | The backend decrypts and validates each packet, checks the idempotency cache, and settles the transfer between accounts. |
| **5. Ledger** | Every outcome is recorded in the transaction ledger with its status, bridge node and hop count. |

### Transaction statuses

| Status | Meaning |
|--------|---------|
| `SETTLED` | The payment was verified and the balances were updated. |
| `REJECTED` | The payment failed validation (for example, a wrong PIN or insufficient funds). |
| `DUPLICATE_DROPPED` | The packet was already processed, so the duplicate upload was ignored. |
| `INVALID` | The packet could not be decrypted or was malformed. |

---

## 🖥️ Demo Walkthrough

1. **Compose a payment.** Choose a sender and receiver, enter an amount and PIN, then click **Inject into mesh**.
2. **Run gossip rounds.** Click **Run gossip round** a few times and watch the packet spread across the devices.
3. **Upload from bridges.** Click **Upload from bridges**. Bridges upload in parallel, which exercises the idempotency check.
4. **Inspect the results.** Check the updated balances, the transaction ledger and the activity log.
5. **Start over.** Click **Reset mesh and cache** to clear everything.

### Demo accounts

| VPA | Holder |
|-----|--------|
| `alice@demo` | Alice |
| `bob@demo` | Bob |
| `carol@demo` | Carol |
| `dave@demo` | Dave |

---

## 🔌 API Reference

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/mesh/state` | Devices, the packets they hold, and the idempotency cache size |
| `GET` | `/api/accounts` | All accounts and their balances |
| `GET` | `/api/transactions` | The transaction ledger |
| `POST` | `/api/demo/send` | Create, encrypt and inject a payment packet into the mesh |
| `POST` | `/api/mesh/gossip` | Run one gossip round |
| `POST` | `/api/mesh/flush` | Make bridge devices upload their packets to the backend |
| `POST` | `/api/mesh/reset` | Clear the mesh and the idempotency cache |

**Example request body for `/api/demo/send`:**

```json
{
  "senderVpa": "alice@demo",
  "receiverVpa": "bob@demo",
  "amount": 500,
  "pin": "1234",
  "ttl": 5,
  "startDevice": "phone-alice"
}
```

---

## 🛠️ Getting Started

### Prerequisites

- A JDK installed (use the version your project targets)
- Maven or Gradle, depending on your build setup

### Run locally

```bash
# Clone the repository
git clone <your-repo-url>
cd <your-repo-folder>

# Start the application (use the command that matches your build tool)
./mvnw spring-boot:run
# or
./gradlew bootRun
```

Then open the dashboard in your browser, usually at `http://localhost:8080`.

---

## 🗂️ Project Structure

```text
.
├── src/main/java/...          # Backend: mesh simulation, crypto, settlement, REST APIs
├── src/main/resources/
│   └── templates/index.html   # Dashboard UI (Thymeleaf template, vanilla HTML/CSS/JS)
└── README.md
```

---

## ⚠️ Disclaimer

This is an **educational simulation**. It does not use real Bluetooth radios, does not connect to real banking or UPI infrastructure, and must not be used to move real money. The accounts, PINs and balances are demo data.

---

## 🙌 Credits

**Created and maintained by [Aryan Yash](https://github.com/your-username)**

- Project adaptation and extension to solve a Very real world issue UPI users face.
- Frontend design and implementation of the backend using Springboot.
- Documentation

Inspired by an existing offline-payment mesh concept.