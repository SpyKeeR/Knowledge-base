# DNS et transport de mail

## 📌 **Enregistrements DNS essentiels au fonctionnement de la messagerie**

### 🔹 **SOA (Start of Authority)**

- 📍 Indique le **serveur maître** (primaire) de la zone DNS
- 📧 Contient l’**adresse e-mail de l’administrateur** de zone
- ⏱️ Définit les **paramètres d’expiration** (TTL, refresh, retry…)

### 🔹 **NS (Name Server)**

- 🔧 Définit les **serveurs DNS faisant autorité** pour le domaine

### 🔹 **A / AAAA**

- 🔗 Associe un **nom de domaine à une adresse IPv4 (A) ou IPv6 (AAAA)**
- 🖥️ Doit exister pour **chaque contrôleur de domaine, serveur et client**

### 🔹 **SRV (Service Record)**

- 🧭 Localise des **services spécifiques** (ex : _kerberos._tcp, _gc._tcp)
- Utilisé notamment dans les architectures **Active Directory**

### 🔹 **MX (Mail eXchange)**

- 📬 Spécifie **le ou les serveurs de réception de courriels** pour un domaine
- 🧭 Pointe généralement vers l’**adresse IP publique du routeur** ou d’un **smart host**
- ❗ Uniquement utilisé pour la **réception de mails**

### 🔹 **SPF (Sender Policy Framework)**

- 🔐 Enregistrement **TXT**
- ✅ Liste des **serveurs autorisés à envoyer des mails** pour un domaine
- ✔️ Vérifié par le **serveur de réception** pour valider l’expéditeur
- ❗ Utilisé uniquement pour **l’envoi de mails**



## 🌍 **Routage réseau : SNAT & DNAT**

### 🔄 **SNAT (Source NAT)**

- 🔁 Transforme une **adresse IP privée en adresse IP publique**
- 🌐 Nécessaire pour les **communications sortantes**

### 🔀 **DNAT (Destination NAT)**

- 📩 Redirige un **paquet entrant** sur une IP publique vers une **IP privée** (souvent vers la **DMZ**)
- 🎯 Utilisé pour exposer un **serveur interne** (ex : relais SMTP) à l’extérieur

## 🧱 **Interopérabilité et sécurité (via serveur Edge dans DMZ)**

- 🔁 Le **DNAT** redirige les connexions SMTP (port 25) vers le **serveur Edge**
- 🧪 Le **serveur Edge** effectue :
  - ✅ **Vérification SPF**
  - 🧹 **Filtrage anti-virus & anti-spam**
- 📮 Si tout est valide, le mail est transféré en interne au **MTA du domaine de destination**



## 📥 **Réception du message par Micheline**

- 📨 Le **MTA** fait une requête **LDAP (port 389)** pour localiser la **boîte aux lettres de Micheline**
- 🗃️ Dépôt du mail dans sa boîte via le **MDA (Exchange Mailbox)**
- 📲 Micheline récupère le message via :
  - **IMAP**
  - **POP3**
  - **Outlook WebApp**

