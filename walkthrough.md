# Dark Web Investigation: Midnite Collective & Forum Enumeration

## 📖 Overview
This document outlines an Open Source Intelligence (OSINT) and web exploitation exercise targeting a clandestine hacking collective known as **Midnite**. The investigation transitions from a clearnet landing page to an authenticated dark web forum (.onion site) by exploiting client-side script vulnerabilities to extract hardcoded credentials. It concludes with the decoding of obfuscated forum data.

---

## 🔍 Phase 1: Target Identification & Reconnaissance

The investigation begins with identifying the target's public-facing infrastructure and locating their hidden services.

### 1. Clearnet Discovery
We discover a public-facing website advertising the **Midnite** hacking collective. The page boasts about their digital espionage capabilities and openly offers tiered DDoS (Distributed Denial of Service) services for purchase.
*(Screenshot showing the Midnite collective's clearnet landing page)*
 <img width="1920" height="922" alt="VirtualBox_kali linux_31_05_2026_11_41_18" src="https://github.com/user-attachments/assets/3c3f8887-47fd-4025-9d7e-d29101f72347" />


### 2. Transition to Darknet
Further reconnaissance leads to a Tor hidden service (.onion address). Navigating to this address in the Tor Browser presents us with the "Dark Forum," serving as a gateway to the collective's internal community.
*(Screenshot showing the Dark Forum welcome screen in the Tor Browser)*
 <img width="1920" height="922" alt="VirtualBox_kali linux_31_05_2026_11_51_25" src="https://github.com/user-attachments/assets/c811642e-0756-4e7d-ac7c-d635f7c5c76f" />

---

## 💥 Phase 2: Exploitation (Authentication Bypass)

To access the internal forum, we must bypass the authentication portal. Instead of brute-forcing, we analyze the web application's client-side code for poor security practices.

### 1. The Login Portal
Clicking "Login" brings us to a standard username and password form. Without known credentials, we open the browser's Developer Tools to inspect the underlying source code and network requests.
*(Screenshot showing the standard login form)*
 <img width="1920" height="922" alt="VirtualBox_kali linux_31_05_2026_11_44_40" src="https://github.com/user-attachments/assets/1c98551d-3df0-40a3-9caf-d9ab5b32099d" />




### 2. Client-Side Credential Extraction
While inspecting the console and available JavaScript functions, we discover a severe vulnerability: hardcoded credentials or a poorly secured credential generation function. Executing/inspecting the `generateUserCredentials()` function in the browser console triggers an alert revealing **Base64 Encoded Credentials**.
*(Screenshot showing the Developer Tools console and the Base64 credentials alert)*
 <img width="1920" height="922" alt="VirtualBox_kali linux_31_05_2026_11_51_25" src="https://github.com/user-attachments/assets/ef99b63f-fb00-46b8-842c-f39193ff0e45" />

*Note: The Base64 string (`S0Y3eWJ1RDE6QWx5aGZvdDBWOVZJV202Vw==`) can be trivially decoded to `KF7ybuD1:Alyhfud0VO9JIwm6W`, providing the plaintext username and password required to log in.*

---

## ⚙️ Phase 3: Post-Exploitation & Data Decoding

Using the extracted credentials, we successfully authenticate and gain access to the internal dashboard. The forum relies on obfuscation to hide its illicit content.

### 1. Dashboard Enumeration
Upon logging in, the dashboard ("Welcome to the Dark Side!") presents three primary categories. However, the titles and descriptions of these categories are encoded in **Hexadecimal**. We also note a pinned post from "P.J.", claiming ownership of the forum.
*(Screenshot showing the authenticated dashboard with Hex-encoded cards)*
 <img width="1920" height="922" alt="VirtualBox_kali linux_31_05_2026_12_02_05" src="https://github.com/user-attachments/assets/c6ac6c0f-f0ab-4c64-a427-b0a4b42e19a6" />

### 2. Hexadecimal Decoding
To understand what is being discussed on this forum, we must translate the Hex strings into readable ASCII text. Using a tool like **CyberChef**, we input the title of the first card (`44 72 75 67 73`). Applying the "From Hex" recipe reveals the plaintext word: **"Drugs"**.
*(Screenshot showing the CyberChef decoding process)*
 <img width="1920" height="922" alt="VirtualBox_kali linux_31_05_2026_12_02_26" src="https://github.com/user-attachments/assets/87e5cd1f-16f1-463b-8500-e09ff37a09b1" />


### 3. De-obfuscating the Forum
Through further interaction with the site (or by decoding the remaining strings), the true nature of the forum is revealed. The hex codes translate to illicit topics, and the interface updates to display corresponding images (e.g., pills for the "Drugs" section).
*(Screenshot showing the de-obfuscated dashboard revealing the hidden images)*
 <img width="1920" height="922" alt="VirtualBox_kali linux_31_05_2026_12_06_57" src="https://github.com/user-attachments/assets/582af03a-6d52-48db-b3aa-44ea4d9b3274" />


---

## 🛡️ Remediation & Security Flaws Identified

This environment demonstrated several critical security failures:
1. **Client-Side Sensitive Data:** Never store credentials, authentication tokens, or validation logic in client-side JavaScript. Authentication must always be handled and validated securely on the backend server.
2. **Security by Obscurity:** Relying on basic Hexadecimal encoding to hide sensitive forum categories is ineffective against any competent analyst.
3.
