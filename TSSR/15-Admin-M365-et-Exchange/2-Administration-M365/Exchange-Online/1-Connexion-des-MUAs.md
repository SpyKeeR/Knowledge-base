# Connexion des MUAs


## 🔧 **Prérequis techniques & ports essentiels**

- Certains ports doivent être **ouverts et accessibles**, notamment :
  - ⚠️ **Port 25** (SMTP) souvent **bloqué** par les FAI (ex : Orange) à cause du **phishing**.
  - 🔎 Vérification indispensable avant toute configuration SMTP ou relais.



## 📬 **Choix des connexions selon les clients**

- 🖥️ **Outlook desktop** : utilise **MAPI sur HTTPS**.
- 📱 **Clients mobiles** : utilisent **ActiveSync**.
- 📤 **POP / IMAP** :
  - IMAP : 🟢 synchronisation multi-appareils.
  - POP : 🔴 tire les mails → peu recommandé (perte de cohérence).



## 🧭 **Découverte automatique – Autodiscover**

- Permet de **configurer automatiquement Outlook**.
- 🔁 Fonctionnement :

  1.  L’utilisateur entre son adresse mail.
  2.  Outlook interroge l’enregistrement **CNAME Autodiscover**.
  3.  Le serveur génère un fichier **XML de configuration**.
  4.  Outlook télécharge ce fichier et se connecte à **Exchange Online**.



- 🌐 **Enregistrement DNS requis :** 
Alias : `autodiscover` > Cible : `autodiscover.outlook.com`

## 🛠️ **Outils de test :**

- Outlook > 🎯 **Tester la configuration automatique de la messagerie (Ne plus utiliser Guessmart (obsolète))**
- 🌐 **Remote Connectivity Analyzer Tool** (Microsoft)



## 🗂️ **Fichiers de données Outlook**

- 📦 .pst (**Personal Storage Table**) : stockage local, souvent lié à POP → risque de perte.
- 🌍 .ost (**Offline Storage Table**) : mode hors ligne pour **Exchange / IMAP**.
- 🧑‍🤝‍🧑 .nst (**Group Storage Table**) : données des **groupes de discussion**.
- 📇 spscoll.dat : suggestions d’adresses mail dans Outlook.

## 🧰 **Outils de gestion des fichiers :**

- 🛠️ **scanpst.exe** pour réparer les fichiers .pst
- 🔄 Supprimer un fichier .ost le régénère automatiquement
- 📦 Utilisation possible d’**outils tiers** pour gérer ou réparer les fichiers corrompus.

## 📋 **Protocole / Port :**

| **Protocole**          | **Port non sécurisé** | **Port sécurisé (TLS/SSL)** |
|------------------------|-----------------------|-----------------------------|
| HTTP                   | 80                    | 443                         |
| POP3                   | 110                   | 995                         |
| IMAP4                  | 143                   | 993                         |
| MAPI / HTTPS           | –                     | 443                         |
| ActiveSync / HTTPS     | –                     | 443                         |
| SMTP                   | 25                    | –                           |
| Soumission client SMTP | 2525                  | 587 / 465                   |

