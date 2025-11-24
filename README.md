# 🛡️ Static Application Security Testing (SAST): OWASP Mutillidae II

![Semgrep](https://img.shields.io/badge/Scanner-Semgrep-green?style=for-the-badge&logo=semgrep)
![Vulnerability](https://img.shields.io/badge/Vulnerability-SQLi%20%7C%20XSS-red?style=for-the-badge)
![Language](https://img.shields.io/badge/Target-PHP-blue?style=for-the-badge&logo=php)

## 📖 Project Overview
This repository documents a security analysis of the **OWASP Mutillidae II** vulnerable web application. 

The goal of this project was to implement **Static Application Security Testing (SAST)** into the development lifecycle. Instead of using pre-built rule sets, I developed **custom Semgrep rules** to identify specific coding flaws that lead to critical security vulnerabilities.

## 🎯 Learning Objectives
- **Understand** the mechanics of Rule-Based Static Analysis.
- **Identify** vulnerable code patterns (Sources and Sinks) manually.
- **Develop** custom YAML rules for the Semgrep engine.
- **Automate** the detection of SQL Injection and Cross-Site Scripting.

---

## 🔍 Vulnerability Analysis

During the reconnaissance phase, the following vulnerabilities were identified in the source code:

### 1. 💉 SQL Injection (SQLi)
*   **Severity:** `CRITICAL`
*   **Location:** `src/classes/SQLQueryHandler.php`
*   **Root Cause:** The application concatenates user-supplied variables directly into SQL query strings without using Prepared Statements or Parameterized Queries.
*   **Impact:** Attackers can manipulate database queries to steal data, bypass authentication, or modify records.

### 2. 🌐 Cross-Site Scripting (XSS)
*   **Severity:** `HIGH`
*   **Location:** `src/index.php`
*   **Root Cause:** The application utilizes the PHP `echo` statement to output user input directly to the browser DOM without using sanitization functions like `htmlspecialchars()`.
*   **Impact:** Attackers can execute malicious JavaScript in the victim's browser to steal session cookies or perform actions on their behalf.

---

## 🛠️ Custom Semgrep Rules

I developed a custom rule file (`sast-rules/sast-custom-rules.yaml`) to enforce secure coding standards.

### Rule 1: SQL Injection Detection
This rule flags any SQL `SELECT` statement that contains a PHP variable.
```yaml
- id: sast-sqli-check
  patterns:
    - pattern: '$query = "SELECT ... $VAR ...";'
  message: "SAST ALERT: Possible SQL Injection. Variable used directly in SQL."
  severity: ERROR
  languages: [php]
```

### Rule 2: Reflected XSS Detection
This rule flags `echo` statements but allows them if `htmlspecialchars` is used.
```yaml
- id: sast-xss-check
  patterns:
    - pattern: echo $VAR;
    - pattern-not: echo htmlspecialchars(...);
  message: "SAST ALERT: Possible XSS. Variable echoed without HTML encoding."
  severity: WARNING
  languages: [php]
```

---

## 🚀 How to Replicate This Scan

**Prerequisites:** Python 3.x and Semgrep installed.

1.  **Clone the Repository:**
    ```bash
    git clone https://github.com/jeromearsany/Mutillidae-SAST-Analysis.git
    cd Mutillidae-SAST-Analysis
    ```

2.  **Run the Semgrep Scan:**
    Target the `src` directory using the custom config file.
    ```bash
    semgrep --config sast-rules/sast-custom-rules.yaml src
    ```

3.  **Interpret Results:**
    - 🔴 **ERROR:** Indicates a high-confidence SQL Injection.
    - 🟡 **WARNING:** Indicates a potential XSS vulnerability.

---

## 👤 Author
**Jerome Arsany**  
*Cybersecurity Program | Fall 2025*  
*Secure Software Development Lab*
