# Bonus - Ports - Protocoles**🚪 Types de ports (numérotation IANA)**

**🔒 Well-Known/Reserved Ports → 0 - 1023**

🔹 Réservés aux **services systèmes** (HTTP, FTP, DNS, etc.)

🔹 Nécessitent les **droits admin/root** pour être utilisés côté serveur 🛡️



**📘 Registered Ports → 1024 - 49151**

🔹 Utilisés pour des applis **enregistrées par des orga (via IANA)**

🔹 **Pas besoin de droits spéciaux** pour les utiliser côté client/serveur



**🌀 Dynamic/Private/Ephemeral Ports → 49152 - 65535**

🔹 Pas enregistrables

🔹 Souvent utilisés **temporairement** (ex : ports source aléatoires pour les connexions sortantes)

🔹 Parfait pour les **communications client → serveur**

🔹 N'est pas appliqué à la lettre par tous les OS : (respect de la **RFC** pour **FreeBSD/Windows** tandis que **Linux** utilise : **32768–60999**)**  **

| 69      | UDP     | TFTP (simple transfert) 🔄     |
|---------|---------|--------------------------------|
| 80      | TCP     | HTTP 🌐                        |
| 110     | TCP     | POP3 (mail) 📬                 |
| 123     | UDP     | NTP (heure) 🕒                 |
| 137-139 | UDP/TCP | NetBIOS 🧱                     |
| 143     | TCP     | IMAP 📥                        |
| 161/162 | UDP     | SNMP (monitoring) 📊           |
| 389     | TCP/UDP | LDAP / AD 🔎                   |
| 443     | TCP     | HTTPS 🔐                       |
| 445     | TCP     | SMB/CIFS (partages Windows) 📁 |
| 465     | TCP     | SMTP over SSL (⚠️ obsolète)    |
| 514     | UDP     | Syslog (logs réseaux) 🧾       |
| 587     | TCP/UDP | SMTP avec STARTTLS ✉️🔐        |
| 636     | TCP     | LDAP SSL/TLS 🔒                |
| 993     | TCP     | IMAP SSL/TLS 📬                |
| 995     | TCP/UDP | POP3 SSL/TLS 📥🔒              |
| 1433    | TCP     | Microsoft SQL Server 💾        |
| 3306    | TCP     | MySQL 🐬                       |
| 3389    | TCP/UDP | RDP (Bureau à distance) 💻     |



**🧠 Ports Courants à Connaître par Cœur 💡**

| **Port** | **Protocole** | **Service**                |
|----------|---------------|----------------------------|
| 20       | TCP           | FTP (données) 📁           |
| 21       | TCP           | FTP (contrôle) 🕹️          |
| 22       | TCP           | SSH 🔐                     |
| 23       | TCP           | Telnet (obsolète) ❌       |
| 25       | TCP           | SMTP (mail) ✉️             |
| 53       | UDP/TCP       | DNS 🌍                     |
| 67/68    | UDP           | DHCP (Serveur/Client) 🖥️📡 |
