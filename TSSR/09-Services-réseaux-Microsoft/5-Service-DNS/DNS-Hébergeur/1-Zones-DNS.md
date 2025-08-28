# Zones DNS

Une **zone** est un **conteneur d’enregistrements** pour un espace de noms. Imagine une boîte 📁 qui contient toutes les infos DNS d’un domaine donné.

Chaque zone = **fichier de zone** → chemin de stockage par défaut (hors Active Directory intégré).

## **🧭 Types de zones**

#### **🔹 Zone directe** : permet de **résoudre un nom → IP**

ex : srvfic.massociety.local → 192.168.0.1

#### **🔹 Zone inverse** : permet de **résoudre une IP → nom**

ex : 192.168.0.1 → srvfic.massociety.local

Ces deux types sont **complémentaires** : l'une traduit les noms, l'autre les IPs.

### **🧑‍🤝‍🧑 Zones primaire et secondaire**

Un domaine peut être **répliqué entre plusieurs serveurs DNS** :

- **Zone principale (ou maître)** : version **modifiable**
- **Zone secondaire (ou esclave)** : **copie en lecture seule**

⚠️ Les **deux** font **autorité** sur la zone → ils peuvent tous deux répondre aux requêtes DNS ✅

Objectif : **résilience** en cas de panne du serveur maître.



## **🧩 Types d’enregistrements DNS dans une zone**

Chaque **fichier de zone** contient des lignes appelées **enregistrements DNS**, selon leur rôle :

- **SOA** : identifie le serveur maître de la zone
- **NS** : liste des serveurs faisant autorité
- **A / AAAA** : nom → IPv4 / IPv6
- **CNAME** : alias (ex : blog → www)
- **MX** : redirection du courrier vers un serveur mail
- **PTR** (zone inverse) : IP → nom



## **🧪 Exemple d’enregistrements classiques**

🧾 Dans une **zone directe** :

- A → example.com → 192.0.2.1
- AAAA → example.com → 2001:db8::1
- CNAME → www → example.com
- MX → mail.example.com = serveur mail

📌 Dans une **zone inverse** :

- PTR → 192.0.2.1 → example.com

## **🧠 Comportement d’un serveur DNS**

Quand une requête arrive :

- Il **vérifie s’il fait autorité** sur la zone → il répond directement ✅
- Sinon, il **transmet la requête** à d’autres serveurs pour obtenir la réponse ❓➡️📡

C’est ce qui garantit **l’efficacité et la hiérarchisation** du système DNS mondial 🌐.

