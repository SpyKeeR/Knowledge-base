# Vérification/Gestion

## **🛠️ Accès aux outils d’administration**

- 📁 Ouvrir les **outils d'administration Windows**.
- 🏛️ Accéder à **"Autorité de certification"**.
- 🏷️ Vérifier le **nom de la PKI** installée.
- ⚙️ Clic droit > **Propriétés** pour consulter les paramètres de l'autorité.
- 📜 Onglet "Général" > **Afficher le certificat** délivré par l’AC.



## **📄 Vérification du certificat racine**

- 🧭 Ouvrir la console MMC via `certmgr.msc`.
- 📂 Aller dans **"Autorités de certification racines de confiance"**.
- 🔐 Vérifier la présence du **certificat de l'autorité racine** nouvellement installée.



## **👤 Vérification des certificats utilisateur**

- 📂 Toujours dans `certmgr.msc`, naviguer vers **"Personnel" > "Certificats"**.
- 👁️ Contrôler que les **certificats utilisateurs** ont bien été générés et installés.



## 🖥️ **Gestionnaire AD CS (Certsrv)**

📋 Contenu :

- 📌 **Certificats révoqués**
- 📌 **Certificats émis**
- 📌 **Demandes en attente**
- 📌 **Demandes échouées**
- 📌 **Modèles de certificats**

💡 Les **modèles de certificat** sont des gabarits prédéfinis.



➡️ Il est conseillé de **dupliquer un modèle existant** puis de le **personnaliser** avant émission.

- 📜 **Certificats délivrés >** Contiennent : numéro de série, date d’émission, expiration, destinataire…
- 👤 **Certificats utilisateur >** Pour les individus (signature, e-mails sécurisés, etc.)
- 🖥️ **Certificats ordinateur >** Pour les machines (authentification, chiffrement réseau…)



### 🌐 **Accès web : certsrv**

📍 URL : <http://@IPduADCS/certsrv>

- Utilise un **compte avec privilèges de domaine**
- Permet à un utilisateur de **demander un certificat** sans passer par un admin
