# 🎯 StreamIO SQL Injection Exploit Script Documentation

Welcome to the ultimate guide for the **StreamIO SQL Injection Exploit Script**! This Python-based tool is designed to tackle the **StreamIO** Capture The Flag (CTF) challenge from HackTheBox. Whether you're testing vulnerabilities, enumerating databases, or dumping user credentials, this script has you covered with a sleek, automated approach. Let’s dive in! 🚀

---

## 🌟 Overview

This script exploits an SQL injection vulnerability in the StreamIO application, targeting an MSSQL database. It’s packed with features to enumerate and extract data efficiently, all while presenting results in a clean, table-like format inspired by tools like `sqlmap`. With built-in retry logic, it even handles basic Web Application Firewall (WAF) interference. 🛡️

---

## ✨ Features

- **Test SQL Injection** 🧪: Confirm the injection point is exploitable.
- **Enumerate Databases** 📚: Retrieve a list of all databases on the server.
- **Enumerate Tables** 📑: Fetch tables from a specified database.
- **Enumerate Columns** 🗂️: Discover columns within a specific table.
- **Dump Table Data** 📦: Extract all data from a chosen table.
- **User Credential Dumping** 🔑: Specifically target and display usernames and passwords.
- **Pretty Table Output** 📊: Results formatted in a neat, readable table.

---

## 🛠️ How to Use

Run the script using Python 3 with simple command-line options. Here’s the syntax:

```bash
python exploit.py [OPTIONS]
```

### Command-Line Options

| Option                    | Description                                      |
|---------------------------|--------------------------------------------------|
| `--test`                  | Test if the SQL injection is exploitable         |
| `--databases`             | List all databases on the server                 |
| `--tables <database>`     | List tables in the specified database            |
| `--columns <db.table>`    | List columns in the specified table              |
| `--dump <db.table>`       | Dump all data from the specified table           |
| `--users`                 | Dump usernames and passwords from `STREAMIO.users` |

### Example Commands

1. **Test the Injection**:
   ```bash
   python exploit.py --test
   ```
   Checks if the SQL injection works and retrieves the MSSQL version.

2. **List All Databases**:
   ```bash
   python exploit.py --databases
   ```
   Displays all databases available on the server.

3. **List Tables in `STREAMIO`**:
   ```bash
   python exploit.py --tables STREAMIO
   ```
   Shows all tables in the `STREAMIO` database.

4. **List Columns in `STREAMIO.users`**:
   ```bash
   python exploit.py --columns STREAMIO.users
   ```
   Reveals the column names in the `users` table.

5. **Dump Data from `STREAMIO.users`**:
   ```bash
   python exploit.py --dump STREAMIO.users
   ```
   Extracts all data from the `users` table.

6. **Dump User Credentials**:
   ```bash
   python exploit.py --users
   ```
   Pulls `username` and `password` columns from `STREAMIO.users` in a formatted table.

---

## 💻 How It Works

The script leverages Python’s `requests` library to send HTTP requests with SQL injection payloads and `BeautifulSoup` to parse the responses. Here’s a breakdown of its core functions:

### 🔧 `run_query(payload, retries=3)`
- **Purpose**: Sends the SQL payload to the target URL.
- **Details**: Extracts injected data from the response, retrying up to 3 times if blocked by a WAF or network issues.
- **Output**: Raw data extracted from the injection.

### 🧪 `test_injection()`
- **Purpose**: Verifies the injection point.
- **Payload**: Uses a simple `OR 1=1` condition and fetches the MSSQL version.
- **Output**: Confirmation of vulnerability and database version.

### 📚 `get_databases()`
- **Purpose**: Lists all databases.
- **Technique**: Uses MSSQL’s `STRING_AGG` for efficiency; falls back to row-by-row if needed.
- **Output**: A list of database names.

### 📑 `get_tables(database)`
- **Purpose**: Enumerates tables in a given database.
- **Source**: Queries `INFORMATION_SCHEMA.TABLES`.
- **Output**: Table names within the specified database.

### 🗂️ `get_columns(database, table)`
- **Purpose**: Lists columns in a specific table.
- **Source**: Queries `INFORMATION_SCHEMA.COLUMNS`.
- **Output**: Column names for the table.

### 📦 `dump_data(database, table, columns)`
- **Purpose**: Dumps all data from specified columns.
- **Technique**: Uses `STRING_AGG` or row-by-row extraction.
- **Output**: Raw data from the table.

### 🔑 `dump_specific_data(database, table, columns)`
- **Purpose**: Dumps specific columns (e.g., `username`, `password`).
- **Output**: Formatted table with aligned columns and borders.

---

## 📊 Sample Output

Here’s what you’ll see when running `--users` to dump credentials from `STREAMIO.users`:

```
[+] Dumping specific data from 'STREAMIO.users'...
  [+] Dumping column 'username'
[DEBUG] Attempt 1: Response length: 2824
[DEBUG] Movie div text: admin|Alexendra|Austin|...|yoshihide3Watch
[DEBUG] Extracted injected data: admin|Alexendra|Austin|...|yoshihide3
  [+] Dumping column 'password'
[DEBUG] Attempt 1: Response length: 2824
[DEBUG] Movie div text: 0049ac57646627b8d7aeaccf8b6a936f|08344b85b329d7efd611b7a7743e8a09|...|fd78db29173a5cf701bd69027cb9bf6b3Watch
[DEBUG] Extracted injected data: 0049ac57646627b8d7aeaccf8b6a936f|08344b85b329d7efd611b7a7743e8a09|...|fd78db29173a5cf701bd69027cb9bf6b3

+----------+----------------------------------+
| username | password                         |
+----------+----------------------------------+
| admin    | 0049ac57646627b8d7aeaccf8b6a936f |
| Alexendra| 08344b85b329d7efd611b7a7743e8a09 |
| Austin   | 083ffae904143c4796e464dac33c1f7d |
| Barbra   | 0cfaaaafb559f081df2befbe66686de0 |
| Barry    | 1c2b3d827032114