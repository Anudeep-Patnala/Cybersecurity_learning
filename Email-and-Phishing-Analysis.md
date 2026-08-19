# 📧 Email & Phishing Analysis

## Overview

I studied the fundamentals of email analysis and phishing detection,
with a focus on understanding email headers, identifying suspicious
emails, and analyzing indicators of phishing.

I also performed practical email-header analysis using Sublime Text
to examine raw email data and understand how an email travels through
different mail servers.

---

## 📩 Email Header Analysis

I learned how to analyze raw email headers and understand the
information contained within them.

### Topics Covered

- From
- To
- Return-Path
- Received headers
- Subject
- Date
- Message-ID
- MIME information
- Sender information
- Mail server information
- IP addresses
- Email routing

### Practical Analysis

I practiced analyzing raw email headers using **Sublime Text**.

I examined the different header fields and used them to understand:

- Where an email originated
- How the email was routed
- Which mail servers handled the message
- Whether sender information appeared suspicious
- Differences between `From` and `Return-Path`
- Information contained in `Received` headers

---

## 🎣 Phishing Analysis

I studied phishing attacks and the techniques commonly used to
deceive users into revealing information or interacting with
malicious content.

### Topics Covered

- Phishing emails
- Email spoofing
- Malicious links
- Suspicious attachments
- Social engineering
- Sender impersonation
- Fake domains
- Urgency-based attacks
- Credential harvesting

### Identifying Suspicious Emails

I learned to look for indicators such as:

- Suspicious sender addresses
- Mismatched domains
- Unexpected attachments
- Suspicious URLs
- Urgent or threatening language
- Requests for credentials or sensitive information
- Inconsistent email headers
- Suspicious email routing

---

## 🔐 Email Authentication

I studied the basic purpose of email authentication mechanisms
used to help verify legitimate senders.

### SPF

Sender Policy Framework helps identify which mail servers are
authorized to send email for a domain.

### DKIM

DomainKeys Identified Mail uses cryptographic signatures to help
verify that an email was authorized by the sending domain and
has not been modified in transit.

### DMARC

DMARC builds on SPF and DKIM and allows domain owners to specify
how receiving mail servers should handle authentication failures.

---

## 🛠️ Tools Used

- Sublime Text
- Web-based email header analyzers
- Command-line tools where applicable

---

## 🎯 Key Takeaways

Through this learning, I developed an understanding of how to:

1. Read and interpret raw email headers.
2. Trace email routing using `Received` headers.
3. Examine sender and recipient information.
4. Understand `From` and `Return-Path`.
5. Identify common phishing indicators.
6. Understand the purpose of SPF, DKIM, and DMARC.
7. Analyze suspicious emails in a structured manner.

---

## 📚 Learning Status

**Status:** Completed fundamentals and practical exercises.

I will continue improving my email-analysis skills through
additional practical investigations and phishing-analysis
exercises.
