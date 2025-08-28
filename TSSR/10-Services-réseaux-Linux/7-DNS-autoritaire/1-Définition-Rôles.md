# **🌐 DNS faisant autorité — Définition & rôle**

🔹 Un **DNS faisant autorité** (authoritative DNS server) est **la source officielle d’information** pour un ou plusieurs domaines.

🔹 Il **répond directement** aux requêtes DNS concernant les zones qu’il héberge.

🔹 Il peut **héberger des zones primaires** (maître) ou **des zones secondaires** (copie de la primaire).

🔹 Il est **interrogé par les résolveurs DNS** pour répondre à une demande de résolution.



## **🧩 Qu’est-ce qu’une zone DNS ?**

Une **zone** est un **ensemble d'enregistrements DNS** associés à :

🔸 Un **nom de domaine** (zone directe) → nom ↔ IP

🔸 Un **réseau IP** (zone inverse) → IP ↔ nom

Exemples :

- **Zone directe** : demo.eni avec db10-fs1 A 10.9.9.5
- **Zone inverse** : 42.168.192.in-addr.arpa avec PTR 192.168.42.103 → spx.pe.jo

## **📚 Enregistrements DNS principaux**

Les **données d’une zone** sont structurées en **enregistrements** (RR : Resource Records) :

| **Type** | **Rôle**                                                     |
|----------|--------------------------------------------------------------|
| 🧾 SOA   | Caractéristiques de la zone (serveur maître, TTL, serial...) |
| 🧭 NS    | Serveur(s) DNS faisant autorité sur la zone                  |
| 🏠 A     | Nom → adresse IPv4                                           |
| 🌐 AAAA  | Nom → adresse IPv6                                           |
| 📦 SRV   | Service (ex : SIP, LDAP...) → FQDN du serveur                |
| 📬 MX    | Serveur(s) de messagerie du domaine                          |
| 🔁 CNAME | Alias : un nom → un autre nom                                |
| 🔙 PTR   | Inverse : IP → nom (présent uniquement en zone **inverse**)  |

➡️ Chaque enregistrement est lié à un **nom**, un **type**, une **valeur**, et un **TTL**.



## **🧠 Zone primaire vs zone secondaire**

### 🔹 **Zone primaire** :

- Contient les **données maîtres** (modifiable).
- Stockée localement, c’est **la source officielle**.
- Exemple : demo.eni sur serveur dns1.demo.eni.

### 🔹 **Zone secondaire** :

- **Copie** de la zone primaire, reçue via une **transfert de zone (AXFR/IXFR)**.
- **Non modifiable localement**, elle se synchronise avec la primaire.
- Sert à la **redondance**, **répartition de charge**, et **résilience**.

