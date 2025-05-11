# Breaking APIs: A Bug Hunter's Guide
Uncovering real-world vulnerabilities through exposed documentation and insecure endpoints.

---

## 🔍 What is API Testing?

**API Testing** involves validating Application Programming Interfaces (APIs) to ensure they meet functionality, performance, reliability, and security expectations. Unlike GUI testing, API testing is performed at the message layer and is essential for backend validation.

In cybersecurity, API testing is critical because APIs often expose sensitive business logic and internal endpoints that attackers can exploit. Misconfigured APIs are one of the top targets in bug bounty programs and real-world breaches.

---

## 🎯 API Testing in Bug Hunting

In bug bounty hunting and penetration testing, API Testing is a **core skill**. It helps uncover vulnerabilities such as:

* **Broken Object Level Authorization (BOLA)**
* **Insecure Direct Object References (IDOR)**
* **Excessive Data Exposure**
* **Mass Assignment**
* **Improper Rate Limiting**
* **Information Disclosure**

Attackers test APIs to see how the backend behaves, manipulate endpoints, bypass authentication, and even uncover undocumented functionality via introspection or leaked API docs.

---

## 📬 Understanding API Methods

### ✅ `GET`

* **Purpose**: Fetches data from the server.
* **Use Case**: View user profile, retrieve account information.
* **Bug Hunting**: Used for unauthorized data access, information disclosure.

### ➕ `POST`

* **Purpose**: Sends data to the server to create a resource.
* **Use Case**: Register new user, submit forms.
* **Bug Hunting**: Often vulnerable to mass assignment and improper input handling.

### 🔄 `PATCH`

* **Purpose**: Updates an existing resource.
* **Use Case**: Modify user details like email or password.
* **Bug Hunting**: Test for IDOR, improper access control when changing another user's data.

### ❌ `DELETE`

* **Purpose**: Removes an existing resource.
* **Use Case**: Delete user, revoke token.
* **Bug Hunting**: Deletion of unauthorized records — a high-severity vulnerability.

---

## 🔬 Practical: PortSwigger API Lab – *Exploiting an API Endpoint Using Documentation*

### 🧠 Objective:

Find the exposed API documentation, locate Carlos' account, and delete it.

---

## 💥 Proof of Concept (PoC)

### 1️⃣ Open the Lab

* Start the lab at: [Lab URL](https://portswigger.net/web-security/api-testing/lab-exploiting-api-endpoint-using-documentation)

![1](https://github.com/user-attachments/assets/9eb73635-8562-4ada-bc1b-e9c04ac4161e) <br/>

### 2️⃣ Login with Credentials

* Username: `wiener`
* Password: `peter`

![2](https://github.com/user-attachments/assets/b8e7d749-4377-489e-bb5a-be1b7a087978) <br/>

### 3️⃣ Change Email Functionality

* Navigate to "Update Email".
* Intercept the request using **FoxyProxy** (connected to **Burp Suite**).
* Submit a dummy email like `test@evil.com`.

![3](https://github.com/user-attachments/assets/aa557b84-7f0c-487c-89f0-b818b101044d) <br/>

### 4️⃣ Burp Analysis

* The intercepted request is:

  ```http
  PATCH /api/user/wiener HTTP/1.1
  Content-Type: application/json
  Cookie: session=...

  { "email": "test@evil.com" }
  ```
* Send this request to **Repeater**.

![4](https://github.com/user-attachments/assets/b08d760b-b54a-4015-ad93-1fb2892fc6d7) <br/>

### 5️⃣ Modify Endpoint to `/api/user`

* Sending to `/api/user` (without username) throws an error – the server needs a specific identifier.

![5](https://github.com/user-attachments/assets/77b54d1b-0b83-453d-8fa3-9aecb9fe17f7) <br/>

### 6️⃣ Discover API Docs

* Request `GET /api` returns API documentation in JSON format.
* Right-click → **Show response in browser** → Open the link.
* An **interactive API UI** appears (like Swagger).

![6](https://github.com/user-attachments/assets/d7df3ab5-dbf9-4b75-ae03-3c6d0773ee67) <br/>

### 7️⃣ API Enumeration

* Try `GET /api/user/wiener` without a body.
* You still get user data like email — **information disclosure**!

![7](https://github.com/user-attachments/assets/d88798a4-06ed-4861-9605-203c96597924) <br/>

### 8️⃣ Cookie Test

* Remove the session cookie → Request returns **unauthorized**.
* Re-add session → Back to normal.

![8](https://github.com/user-attachments/assets/2d4501ae-b460-4375-ae80-bcd5e3d40654) <br/>

### 9️⃣ Targeting Carlos

* Use `GET /api/user/carlos` → Returns **Carlos' username and email.**
* **Impact**: Exposes another user's data — often a P1/P2 issue in real-world apps.

![9](https://github.com/user-attachments/assets/17070371-c329-4ab9-80bb-53c64fdac4bf) <br/>

### 🔟 Delete Carlos

* Use `DELETE /api/user/carlos` in API docs or manually via Burp.
* Server returns:

  ```json
  { "success": true }
  ```

![10](https://github.com/user-attachments/assets/95207a16-ac31-4eda-94d6-a68b6e2b270a) <br/>

### ✅ Lab Completed

* Carlos is deleted.
* Refresh the lab site — confirmation shown: **“Congratulations, you solved the lab!”**

![11](https://github.com/user-attachments/assets/f6cdb65d-e7b1-49dc-869f-765d3a30b2b2) <br/>

---

## 📌 Summary

This lab demonstrates how **exposed API documentation** and insecure access controls can lead to **account deletion, information disclosure**, and other serious vulnerabilities. Bug bounty hunters often look for:

* Public Swagger UI or Postman collections.
* Misconfigured PATCH/DELETE access.
* Absence of proper authentication or authorization checks.

---

## 🏁 Conclusion & Goodbye Note

API Testing bridges the gap between traditional web testing and deep backend exploitation. By learning to **enumerate**, **manipulate**, and **abuse** API endpoints, you unlock a whole new class of vulnerabilities — from information leaks to full account takeovers.

Always check for:

* Insecure endpoints.
* Verb tampering.
* Lack of rate limiting and auth checks.
* Documentation leaks.

🫡 **Happy Hacking!** May your Burp always be intercepting and your bounties sky-high.
Until next time, hacker. 🛡️🕵️‍♂️💻

---
