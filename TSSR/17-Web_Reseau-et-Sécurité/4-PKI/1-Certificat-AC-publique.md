# Certificat AC publique

## **🧾 Certificats racines de confiance intégrés**

- 🖥️ Les OS (Windows, Linux…) et certains navigateurs comme **Firefox** embarquent une **liste de certificats racines** de confiance.
- 🏢 Ces certificats proviennent d’**autorités de certification publiques (CA)**.
- ✅ Tout certificat généré par une de ces CA ou de ses **serveurs secondaires** sera automatiquement **considéré comme fiable**.
- 💰 La plupart de ces certificats sont **payants**.
- 💸 **Exception notable** : le service **Let’s Encrypt** propose des certificats **gratuits**.
- 🌐 Ces certificats permettent de **publier des services SSL/TLS sur Internet** (HTTPS, etc.).



## **🛠️ Rôle AD CS sur Windows Server (PKI interne)**

- 🏗️ Microsoft propose le rôle **AD CS (Active Directory Certificate Services)** sur ses OS serveurs, aussi appelé **PKI**.
- 🔐 Ce rôle permet de **générer des certificats** pour une utilisation **interne** (ex : sites web, Exchange, services applicatifs…).
- 🧩 Les **clients du domaine Active Directory** font automatiquement **confiance** à ces certificats.
  - 📥 Le **certificat racine est automatiquement distribué** à tous les postes via l’AD.
- ⚙️ Une fois en place, la **PKI permet un déploiement simple et rapide** de nouveaux services internes.
- 🚫 Il reste cependant **très difficile de publier ces services internes sur Internet**, car ils **ne sont pas reconnus publiquement**.



## 🛡️ **PKI (Infrastructure à Clé Publique) avec Windows Server – AD CS**

L’Infrastructure à Clé Publique (PKI) de **Windows Server** est mise en œuvre via le service **Active Directory Certificate Services (AD CS)**.

Elle permet de **créer, gérer et distribuer** des certificats numériques pour **sécuriser les communications** et **authentifier les entités** d’une organisation.

👉 Dans ce contexte, on utilise une **PKI privée (Private/Internal)** : le CA est **accessible uniquement depuis le réseau local**.



## 🏛️ **Autorité de Certification (CA)**

Une **hiérarchie de confiance** est mise en place avec des autorités racine et subordonnées :

### 🔹 **AC racine (Root CA)**

- Plus haut niveau de la hiérarchie
- Sert d’**ancre de confiance**
- Intégrée au système d’exploitation, navigateur, etc.
- Très sécurisée et souvent **hors ligne**

### 🔹 **AC subordonnée (Subordinate CA)**

- Entre la Root CA et les certificats finaux
- Peut être segmentée par usage (SSL, S/MIME…), localisation ou type de clé (RSA, ECC)

### 🔹 **Certificats d’entité finale (End-Entity)**

- Installés sur **serveurs, postes, équipements**
- Utilisation : SSL/TLS, signature de code, chiffrement mail, authentification…



🌍 **Exemples d’AC Publiques**

- 🔹 DigiCert
- 🔹 GlobalSign
- 🔹 Sectigo (anciennement Comodo CA)
- 🔹 Amazon Certificate Manager (ACM)
- 🔹 **Let’s Encrypt** :
  - Gratuit, automatisé, ouvert
  - Pour tout possesseur de nom de domaine
  - Facilite l’activation de **HTTPS**
  - Vise un Web **plus sécurisé** et **respectueux de la vie privée**
