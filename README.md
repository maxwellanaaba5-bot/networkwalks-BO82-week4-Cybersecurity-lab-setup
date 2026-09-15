# networkwalks-BO82-week4-Cybersecurity-lab-setup
# 🔐 Mediroza General Hospital — Penetration Testing Project

**Networkwalks Cybersecurity Training — Batch B082 | Week 4**

> **Project Type:** Authorized Web Application Penetration Testing Lab
> **Target:** `https://medirozahospital.com`
> **Focus Areas:** Reconnaissance, Web Authentication Testing, SQL Injection, Password Cracking, PDF Decryption, Metadata Analysis, Sensitive File Discovery
> **Status:** Completed

---

## 📌 Table of Contents

* [1. Project Overview](#1-project-overview)
* [2. Objectives](#2-objectives)
* [3. Scope and Authorization](#3-scope-and-authorization)
* [4. Tools Used](#4-tools-used)
* [5. Methodology](#5-methodology)
* [6. Milestone 1 — Initial Access](#6-milestone-1--initial-access)
* [7. Milestone 2 — Crack the Encryption](#7-milestone-2--crack-the-encryption)
* [8. Milestone 3 — Deep Reconnaissance](#8-milestone-3--deep-reconnaissance)
* [9. Key Findings](#9-key-findings)
* [10. Security Impact](#10-security-impact)
* [11. Recommendations](#11-recommendations)
* [12. Evidence](#12-evidence)
* [13. Lessons Learned](#13-lessons-learned)
* [14. Conclusion](#14-conclusion)

---

# 1. Project Overview

This project was completed as part of the **Networkwalks Cybersecurity Training Program, Batch B082 — Week 4**.

The assessment simulated a web application penetration test against **Mediroza General Hospital**. The objective was to identify security weaknesses across the application's authentication mechanism, encrypted documents, web server configuration, and exposed database backup.

The assessment followed a progressive attack path in which information discovered during reconnaissance was used to support subsequent stages of the investigation.

The assessment demonstrated how multiple individually significant weaknesses can be chained together to increase the overall security impact of a compromised application.

---

# 2. Objectives

The primary objectives of this assessment were to:

* Perform reconnaissance against the target web application.
* Identify hidden directories using `robots.txt`.
* Test the application's authentication mechanism.
* Identify username enumeration vulnerabilities.
* Test for SQL injection.
* Demonstrate an authentication bypass.
* Obtain protected PDF reports through the authorized training portal.
* Extract PDF password hashes.
* Crack password-protected PDF files using wordlists.
* Decrypt a protected PDF while preserving the original file.
* Analyze PDF metadata.
* Correlate metadata findings with previously discovered reconnaissance information.
* Identify exposed directories and backup files.
* Extract structured information from an exposed SQL database backup.
* Document security findings and recommend remediation measures.

---

# 3. Scope and Authorization

This assessment was performed strictly within the scope of the assigned **Networkwalks cybersecurity training laboratory**.

**Target:**

```text
https://medirozahospital.com
```

Testing activities were limited to the systems, files, and techniques specified by the training exercise.

No unauthorized third-party systems were intentionally targeted.

> **Note:** All techniques documented in this repository should only be performed against systems for which explicit authorization has been obtained.

---

# 4. Tools Used

| Tool                          | Purpose                                                 |
| ----------------------------- | ------------------------------------------------------- |
| `curl`                        | Web reconnaissance and HTTP requests                    |
| Web Browser                   | Application navigation and authentication testing       |
| Networkwalks Hash Calculator  | PDF hash extraction                                     |
| Networkwalks Password Cracker | Password recovery using wordlists                       |
| JTR Wordlist                  | Extended password dictionary                            |
| `qpdf`                        | PDF decryption and creation of an unlocked working copy |
| `ExifTool`                    | PDF metadata analysis                                   |
| `wget`                        | Downloading the exposed SQL backup                      |
| `grep`                        | Searching SQL data                                      |
| `sed`                         | Extracting relevant SQL statements                      |
| Kali Linux                    | Security testing environment                            |

---

# 5. Methodology

The assessment followed a structured penetration-testing workflow:

```text
Reconnaissance
      ↓
Authentication Testing
      ↓
SQL Injection Testing
      ↓
Authentication Bypass
      ↓
Document Acquisition
      ↓
Password Hash Extraction
      ↓
Password Cracking
      ↓
PDF Decryption
      ↓
Metadata Analysis
      ↓
Directory Reconnaissance
      ↓
Backup File Discovery
      ↓
Database Analysis
      ↓
Findings & Recommendations
```

---

# 6. Milestone 1 — Initial Access

## 6.1 Reconnaissance with robots.txt

The first step was to inspect the target's `robots.txt` file.

Command:

```bash
curl https://medirozahospital.com/robots.txt
```

The response revealed the following directories:

```text
Disallow: /patient/
Disallow: /staff/
Disallow: /old/
```

The `/patient/` directory was selected as the target for the initial-access portion of the assessment.

The `/old/` directory was also significant because it became relevant during the later reconnaissance stage.

### Evidence

**Suggested screenshot filename:**

```text
01_robots_txt_recon.png
```

---

## 6.2 Identifying the Login Page

The `/patient/` directory led to the patient login page:

```text
https://medirozahospital.com/patient/login.php
```

The login page was identified as a potential entry point for authentication testing.

### Evidence

```text
02_patient_login_page.png
```

---

## 6.3 Username Enumeration

A test was performed to determine whether the application revealed whether usernames existed.

### Test 1

```text
Username: bob
Password: test123
```

Response:

```text
Username not found
```

### Test 2

```text
Username: admin
Password: test123
```

Response:

```text
Incorrect password
```

The application generated different responses depending on whether the username existed.

This behavior confirmed **username enumeration**.

### Security Issue

A malicious user could potentially use the different responses to determine valid usernames before attempting further attacks.

### Evidence

```text
03_username_enumeration.png
```

---

## 6.4 SQL Injection Testing

A single quote was entered into the username field:

```text
admin'
```

The application returned a MySQL error indicating a SQL syntax problem.

This demonstrated that user input was being incorporated into a database query without sufficient input handling.

### Finding

**SQL Injection vulnerability identified.**

### Evidence

```text
04_sql_injection_error.png
```

---

## 6.5 Authentication Bypass

A SQL injection payload was tested in the authorized lab environment:

```text
admin' --
```

The application accepted the input and loaded the portal.

This demonstrated that the authentication mechanism could be bypassed because the SQL query was vulnerable to injection.

### Security Impact

An attacker could potentially access functionality intended for authenticated users without possessing the legitimate account password.

### Evidence

```text
05_authentication_bypass.png
```

---

## 6.6 Downloading the Reports

After accessing the portal, three PDF reports were available:

```text
patient_report_1.pdf
patient_report_2.pdf
patient_report_3.pdf
```

All three reports were downloaded for the next stage of the assessment.

### Evidence

```text
06_reports_portal.png
07_downloaded_reports.png
```

---

# 7. Milestone 2 — Crack the Encryption

## 7.1 PDF Hash Extraction

The password-protected PDFs required password recovery before their contents could be examined.

The Networkwalks Hash Calculator was used to extract the password hashes.

The extracted hashes began with:

```text
$pdf$
```

Each report was processed individually.

### Evidence

```text
08_report1_pdf_hash.png
09_report2_pdf_hash.png
10_report3_pdf_hash.png
```

---

## 7.2 Cracking Report 1

The extracted hash for `patient_report_1.pdf` was submitted to the Networkwalks Password Cracker using the built-in wordlist.

Result:

```text
patient_report_1.pdf → 123456
```

### Evidence

```text
11_report1_password_cracked.png
```

---

## 7.3 Cracking Report 2

The extracted hash for `patient_report_2.pdf` was tested using the built-in wordlist.

Result:

```text
patient_report_2.pdf → password
```

### Evidence

```text
12_report2_password_cracked.png
```

---

## 7.4 Cracking Report 3

The initial attempt against `patient_report_3.pdf` used the built-in wordlist.

Result:

```text
Exhausted wordlist.
No match.
ACCESS DENIED.
```

This demonstrated that the password was not contained in the smaller dictionary.

A larger JTR wordlist supplied for the training exercise was then used.

Result:

```text
patient_report_3.pdf → !@#$%^&
```

### Lesson

When a limited wordlist fails, a larger and more comprehensive wordlist may contain the required password.

### Evidence

```text
13_report3_builtin_wordlist_failed.png
14_report3_jtr_password_cracked.png
```

---

## 7.5 Decrypting Report 3

The recovered password was used with `qpdf` to create an unlocked working copy.

Command:

```bash
qpdf --password='!@#$%^&' --decrypt \
~/Downloads/patient_report_3.pdf \
~/Downloads/report3_open.pdf
```

The original encrypted PDF was preserved while a separate decrypted copy was created.

```text
patient_report_3.pdf
        │
        │ qpdf --decrypt
        ↓
report3_open.pdf
```

This unlocked copy was required for subsequent metadata analysis.

### Evidence

```text
15_qpdf_decryption.png
16_report3_open_verified.png
```

---

# 8. Milestone 3 — Deep Reconnaissance

## 8.1 PDF Metadata Analysis

The unlocked PDF was analyzed using ExifTool.

Command:

```bash
exiftool ~/Downloads/report3_open.pdf
```

ExifTool was used to inspect metadata fields associated with the document.

Metadata analysis can reveal information such as:

* File properties
* PDF version
* Creation information
* Modification information
* Creator software
* Producer software
* Page count
* Other document properties

The metadata investigation provided a clue associated with the `/old/` directory.

### Evidence

```text
17_exiftool_report3_metadata.png
```

---

## 8.2 Confirming `/old/` Using robots.txt

The `/old/` directory had already been identified during the initial reconnaissance phase.

The earlier `robots.txt` output contained:

```text
Disallow: /old/
```

The later metadata clue therefore correlated with information discovered during Milestone 1.

This demonstrates the importance of maintaining and correlating reconnaissance findings throughout a penetration test.

### Evidence

```text
01_robots_txt_recon.png
```

---

## 8.3 Directory Listing Exposure

The `/old/` directory was accessed:

```text
https://medirozahospital.com/old/
```

The server returned an automatically generated directory index:

```text
Index of /old/
```

The listing exposed:

```text
mediroza_db_backup_2019.sql
```

The response also identified the web server as:

```text
LiteSpeed Web Server
```

### Security Finding

**Directory listing was enabled on the `/old/` directory.**

This configuration allowed users to enumerate files stored in the directory.

### Evidence

```text
19_old_directory_listing.png
```

---

## 8.4 Downloading the Database Backup

The exposed SQL backup was downloaded using:

```bash
wget https://medirozahospital.com/old/mediroza_db_backup_2019.sql
```

The downloaded file was:

```text
mediroza_db_backup_2019.sql
```

### Evidence

```text
20_sql_backup_download.png
```

---

## 8.5 Staff Database Analysis

The SQL backup contained an `INSERT INTO staff` statement.

The relevant records were extracted using:

```bash
sed -n '/INSERT INTO `staff`/,/;/p' mediroza_db_backup_2019.sql
```

The table contained employee information including:

* Full name
* Job title
* Department
* Monthly salary
* Other employee-related fields

For reporting purposes, the relevant fields were organized into a structured table.

### Evidence

```text
21_staff_sql_records.png
22_staff_data_table.png
```

---

## 8.6 Shareholder Database Analysis

The SQL backup also contained a `shareholders` table.

The relevant records were extracted using:

```bash
sed -n '/INSERT INTO `shareholders`/,/;/p' mediroza_db_backup_2019.sql
```

The relevant fields included:

* Shareholder name
* Share percentage
* Share class

The information was converted from raw SQL syntax into a human-readable structure for analysis.

### Evidence

```text
23_shareholders_sql_records.png
24_shareholders_data_table.png
```

---

# 9. Key Findings

The assessment identified several security weaknesses.

| ID   | Finding                                                 | Severity |
| ---- | ------------------------------------------------------- | -------- |
| F-01 | Username enumeration                                    | Medium   |
| F-02 | SQL injection in authentication                         | Critical |
| F-03 | Authentication bypass                                   | Critical |
| F-04 | Weak PDF passwords                                      | High     |
| F-05 | Sensitive information exposed through document metadata | Medium   |
| F-06 | Directory listing enabled                               | High     |
| F-07 | Database backup publicly accessible                     | Critical |
| F-08 | Sensitive employee/shareholder information exposed      | Critical |

> **Severity ratings are based on the potential security impact demonstrated within the training environment and should be validated against the organization's actual risk model.**

---

# 10. Security Impact

The vulnerabilities identified during the assessment demonstrate how several weaknesses can be chained together.

The most significant risk was the publicly accessible database backup.

An exposed database backup can potentially reveal sensitive organizational information without requiring direct access to the application's database server.

The combination of:

```text
SQL Injection
      +
Authentication Bypass
      +
Weak Document Passwords
      +
Exposed Directory
      +
Public Database Backup
```

creates a significantly larger attack surface than any single vulnerability considered independently.

Potential impacts include:

* Unauthorized access to application functionality
* Exposure of confidential documents
* Disclosure of employee information
* Disclosure of organizational ownership information
* Exposure of salary information
* Increased risk of targeted phishing or social engineering
* Potential compromise of additional systems if exposed credentials or secrets exist in backups

---

# 11. Recommendations

## 11.1 Fix SQL Injection

Use parameterized queries or prepared statements instead of directly concatenating user input into SQL queries.

Example:

```text
User Input
    ↓
Prepared Statement
    ↓
Database
```

Input validation should also be implemented as an additional security layer.

---

## 11.2 Prevent Username Enumeration

The application should return a generic authentication error such as:

```text
Invalid username or password.
```

The same response should be provided whether the username or password is incorrect.

---

## 11.3 Secure Authentication

Authentication logic should be redesigned to prevent SQL injection-based authentication bypasses.

Additional protections should include:

* Secure password hashing
* Multi-factor authentication where appropriate
* Rate limiting
* Account lockout or progressive delays
* Secure session management

---

## 11.4 Remove Public Database Backups

Database backups should **never be stored in publicly accessible web directories**.

The exposed file:

```text
mediroza_db_backup_2019.sql
```

should be removed from the web root immediately.

Backups should instead be stored in protected storage with appropriate:

* Access controls
* Encryption
* Authentication
* Backup retention policies
* Monitoring

---

## 11.5 Disable Directory Listing

Directory indexing should be disabled on directories that do not intentionally require public file browsing.

The `/old/` directory should either be removed or protected with appropriate access controls.

---

## 11.6 Review Existing Backups

All old and archived files should be reviewed for:

* Database dumps
* Credentials
* API keys
* Configuration files
* Personal information
* Internal documents
* Source code
* Logs

Sensitive files should not remain accessible from the public web.

---

## 11.7 Use Strong Document Passwords

Password-protected documents should use strong, unique passwords rather than common passwords or easily guessable patterns.

Password policies should encourage sufficiently long and unpredictable passwords.

---

## 11.8 Review Metadata Exposure

Before publishing documents externally, organizations should review document metadata and remove unnecessary internal information.

A controlled document sanitization process should be implemented where appropriate.

---

# 12. Evidence

The following screenshots should be included in the repository as evidence of the assessment activities.

| Screenshot                                 | Description                        |
| ------------------------------------------ | ---------------------------------- |
| `01_robots_txt_recon.png`                  | robots.txt reconnaissance          |
| `02_patient_login_page.png`                | Patient login page                 |
| `03_username_enumeration.png`              | Username enumeration               |
| `04_sql_injection_error.png`               | SQL injection error                |
| `05_authentication_bypass.png`             | Authentication bypass              |
| `06_reports_portal.png`                    | Reports portal                     |
| `07_downloaded_reports.png`                | Downloaded PDF reports             |
| `08_report1_pdf_hash.png`                  | Report 1 hash                      |
| `09_report2_pdf_hash.png`                  | Report 2 hash                      |
| `10_report3_pdf_hash.png`                  | Report 3 hash                      |
| `11_report1_password_cracked.png`          | Report 1 password recovery         |
| `12_report2_password_cracked.png`          | Report 2 password recovery         |
| `13_report3_builtin_wordlist_failed.png`   | Report 3 failed wordlist attempt   |
| `14_report3_jtr_password_cracked.png`      | Report 3 JTR password recovery     |
| `15_qpdf_decryption.png`                   | qpdf decryption                    |
| `16_report3_open_verified.png`             | Verification of unlocked PDF       |
| `17_exiftool_report3_metadata.png`         | PDF metadata analysis              |
| `18_robots_old_directory_confirmation.png` | `/old/` confirmation               |
| `19_old_directory_listing.png`             | Exposed directory listing          |
| `20_sql_backup_download.png`               | SQL backup download                |
| `21_staff_sql_records.png`                 | Staff SQL records                  |
| `22_staff_data_table.png`                  | Structured staff information       |
| `23_shareholders_sql_records.png`          | Shareholder SQL records            |
| `24_shareholders_data_table.png`           | Structured shareholder information |

> **Screenshot naming convention:** Use lowercase filenames with underscores to keep evidence organized and consistent.

---

# 13. Lessons Learned

This project provided practical experience with several important penetration-testing concepts.

### Reconnaissance

Information such as `robots.txt` can reveal application paths that may deserve further investigation.

### Vulnerability Chaining

A vulnerability does not always exist in isolation. Multiple weaknesses can be combined to increase their overall impact.

### SQL Injection

Poorly handled user input can allow attackers to manipulate database queries and potentially bypass authentication.

### Password Security

A password that is not present in a small wordlist may still be recoverable using a larger dictionary.

### File Security

Old directories and backup files can become a major security risk when exposed through the web server.

### Metadata

Files can contain useful information beyond their visible content. Metadata should therefore be considered during reconnaissance.

### Database Exposure

A publicly accessible database backup can expose large amounts of sensitive organizational information even if the live database itself is properly protected.

---

# 14. Conclusion

The Mediroza General Hospital penetration-testing project demonstrated a complete attack chain beginning with reconnaissance and progressing through authentication testing, SQL injection, password recovery, document analysis, directory enumeration, and database reconnaissance.

The assessment showed how seemingly small configuration and application weaknesses can combine to create significant security exposure.

The most critical findings were the **SQL injection vulnerability, authentication bypass, publicly accessible database backup, and exposure of sensitive information through the web server**.

Proper remediation should prioritize eliminating direct database backup exposure, correcting the authentication implementation, using parameterized SQL queries, disabling unnecessary directory listing, and establishing secure procedures for handling backups and sensitive documents.

This exercise strengthened practical skills in:

```text
Reconnaissance
Web Application Testing
SQL Injection
Authentication Testing
Password Cracking
Linux Security Tools
PDF Analysis
Metadata Analysis
Web Enumeration
Database Analysis
Security Reporting
```

---

## 📚 Project Information

**Program:** Networkwalks Cybersecurity Training
**Batch:** B082
**Week:** 4
**Project:** Mediroza General Hospital Penetration Testing
**Assessment Type:** Authorized Training Laboratory
**Environment:** Kali Linux
**Author:** Anaaba Maxwell Apuswini
---

> **Confidentiality Notice:** This documentation was prepared for authorized cybersecurity training purposes. Techniques demonstrated in this project must only be used against systems where explicit permission to test has been granted.
