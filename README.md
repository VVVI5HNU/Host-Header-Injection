# Host Header Injection Leading to Account Takeover

> ⚠️ **Disclaimer**  
> This repository is intended strictly for **educational and authorized security testing purposes**.  
> Perform these tests **only on applications you own or have explicit permission to assess**.  
> The author is not responsible for misuse of this information.

---

## 📌 Overview

This repository documents a **critical security vulnerability** where improper handling of the HTTP `Host` header in the **password reset / change password functionality** leads to **account takeover**.

The issue occurs when an application **trusts user-controlled Host headers** while generating password reset links, allowing an attacker to **capture sensitive password reset tokens**.

---

## 🧠 Vulnerability Summary

- **Vulnerability Type:** Host Header Injection  
- **Impact:** Account Takeover  
- **Attack Vector:** Password Reset Link Poisoning  
- **User Interaction:** Required (victim clicks the reset link)

---

## 🔍 Root Cause

The application:
- Uses the HTTP `Host` header to construct password reset URLs
- Does not validate or restrict trusted host values
- Sends the generated reset link via email to the user

Because the `Host` header is **fully attacker-controlled**, this allows URL poisoning.

---

## 🧪 Attack Scenario (High-Level)

1. Attacker intercepts a **change password / forgot password request**
2. Attacker modifies the `Host` header to an **attacker-controlled domain** (e.g., Burp Collaborator)
3. Application generates a password reset link using the malicious host
4. Victim receives the reset link via email
5. Victim clicks the link (user interaction required)
6. The request is sent to the attacker-controlled domain
7. Attacker captures the **password reset token**
8. Attacker reuses the token on the legitimate application
9. Attacker gains access to the victim’s account

---

## 🔐 Security Impact

Successful exploitation can result in:

- Full account takeover
- Unauthorized password reset
- Access to sensitive user data
- Loss of user trust

This vulnerability is considered **high to critical severity**.

---

## 📎 Proof of Concept (Conceptual)

- Replace the original `Host` header with an attacker-controlled domain
- Observe that the password reset email contains a poisoned URL
- When accessed, the reset token is exposed to the attacker
- The token can be reused to reset the account password

> No exploit code is included to prevent misuse.

---

## 🛡 Mitigation & Prevention

To fix this issue, applications should:

- Never trust user-supplied `Host` headers
- Use a hardcoded, allowlisted domain for reset links
- Validate `X-Forwarded-Host` and similar headers
- Generate absolute URLs using server-side configuration
- Implement additional token validation (IP, user-agent binding)
- Add expiration and single-use enforcement on reset tokens

---

## 🧩 CWE & OWASP Mapping

- **CWE-346** – Origin Validation Error  
- **CWE-640** – Weak Password Recovery Mechanism  
- **OWASP A01** – Broken Access Control  
- **OWASP A04** – Insecure Design  

---

## 📝 Reporting Title (Recommended)

```
Host Header Injection Leading to Account Takeover via Password Reset Poisoning
```

---

## 📚 References

- OWASP: Host Header Injection  
- OWASP: Forgot Password Cheat Sheet  
- PortSwigger Web Security Academy  

---

## 📜 License

This repository is intended for **educational and authorized security testing purposes only**.

---

## ✅ End of Documentation
