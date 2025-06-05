# 🔐 Secure Client-Server Communication with Fernet Encryption and Wireshark
![Wireshark Preview](https://github.com/RolandVictorMusa/Project/blob/8ea4547f7300f1cd05aef132c9bb32f885c588b9/ChatGPT%20Image%20May%2026%2C%202025%2C%2011_15_00%20AM.png)

## 📌 Introduction

This hands-on cybersecurity project demonstrates the dangers of unencrypted network communication and how to secure it using symmetric encryption with **Fernet (AES)**.
We’ll observe both plaintext and encrypted traffic using **Wireshark**.

---

## 🎯 Project Goals

* Understand network vulnerabilities through packet sniffing
* Secure client-server communication using **Fernet encryption**
* Compare plaintext vs encrypted traffic in **Wireshark**

---

## 🧰 Tools Required

* Python 3
* "cryptography" Python package
* Wireshark (packet analyzer)
* Two terminals or machines (or use localhost)

---

## 🌐 Topology Overview

A basic client-server system exchanging messages on TCP port "9090".

---

## 🔓 Step 1 – Plaintext Client-Server Setup

Simulate a client sending a message (e.g. badge check-in) to a server.

### 💻 Server Code (Plaintext)


# server_plaintext.py
import socket

server = socket.socket()

server.bind(('localhost', 9090))

server.listen(1)

print("Server listening on port 9090...")

conn, addr = server.accept()

data = conn.recv(1024)

print(f"Received: {data.decode()}")

conn.close()


### 💻 Client Code (Plaintext)


# client_plaintext.py
import socket

client = socket.socket()

client.connect(('localhost', 9090))

client.send(b"BadgeNumber: 12345")

client.close()


---

## 📡 Step 2 – Analyze with Wireshark (Unsecured)

1. Open Wireshark
2. Filter: "tcp.port == 9090"
3. Run server, then client
4. Follow TCP stream

### 🔍 Message Capture (Plaintext)

The badge number appears in full. This exposes sensitive info.

---

## ⚡ Why Plaintext Is a Risk

* **No Confidentiality**: Anyone can read the traffic
* **No Integrity**: Data can be modified mid-transit
* **No Authentication**: No way to verify sender/receiver
* **Compliance Violations**: Fails standards like GDPR, HIPAA, PCI-DSS

---

## 🤔 Real-World Implications

If deployed on a company network:

* Attackers can sniff employee data
* Possible impersonation or badge spoofing
* Internal systems can be scripted or abused

---

## 💡 What This Teaches

**Encryption is necessary—even inside private networks.**
Without it, interception = compromise.

---

## 🔐 Step 3 – Secure the Communication (Fernet Encryption)

Use **Fernet** for symmetric AES-based encryption.

### 🔑 Generate Symmetric Key


# generate_key.py
from cryptography.fernet import Fernet

key = Fernet.generate_key()

with open("secret_key.key", "wb") as f:

   f.write(key)


Share "secret_key.key" with both client and server.

---

### 💻 Server Code (Encrypted)


# server_encrypted.py
import socket
from cryptography.fernet import Fernet

with open("secret_key.key", "rb") as f:

   key = f.read()
    
fernet = Fernet(key)

server = socket.socket()

server.bind(('localhost', 9090))

server.listen(1)

print("Server listening on port 9090...")

conn, addr = server.accept()

encrypted_data = conn.recv(1024)

decrypted_data = fernet.decrypt(encrypted_data)

print(f"Decrypted: {decrypted_data.decode()}")

conn.close()


---

### 💻 Client Code (Encrypted)


# client_encrypted.py
import socket
from cryptography.fernet import Fernet

with open("secret_key.key", "rb") as f:

   key = f.read()
   
fernet = Fernet(key)

message = fernet.encrypt(b"BadgeNumber: 12345")

client = socket.socket()

client.connect(('localhost', 9090))

client.send(message)

client.close()


---

## 🔍 Step 4 – Analyze with Wireshark (Encrypted)

* Run Wireshark and filter: "tcp.port == 9090"
* Start encrypted server and client
* Follow TCP stream

### 🔒 Message Capture (Encrypted)

The packet now shows encrypted, unreadable data.

---

## 🔐 Why This Works

* **AES + HMAC** = encryption + integrity
* **Fernet** = simple API, strong cryptography
* Wireshark captures ciphertext, but without the key, it's meaningless

---

## ✅ Benefits of Encryption

* **Confidentiality**: Message can’t be read if intercepted
* **Tamper Resistance**: Any changes invalidate the payload
* **Compliance**: Meets security requirements (GDPR, HIPAA, etc.)
* **Zero Trust**: Assumes no internal traffic is safe without encryption

---

## 🔄 Real-World Security Reflection

Encryption protects internal communication between:

* Enterprise applications
* Healthcare systems
* IoT devices
* ICS/SCADA networks
* Cloud services and APIs

Even if your system is “private,” treat it as exposed.

---

## 📓 Summary

* Unencrypted data = exposed data
* Encryption ensures that only intended parties can access message content
* Wireshark is a powerful tool to verify security and educate teams

---

## 📌 Key Takeaways

* **Always encrypt**: Even simple badge numbers matter
* **Symmetric encryption**: Fernet is practical and effective
* **Use Wireshark**: To test, teach, and verify encryption works

---

## 🧠 Final Reflection

Security isn’t an add-on—it’s a design requirement.
If your data can be intercepted and read, it’s vulnerable.
Encrypt everything. Test everything. Build securely from day one.

> “If someone can intercept your traffic and read it, you have no security.
> If they can intercept it but not read or alter it, you're encrypted and safe.”
