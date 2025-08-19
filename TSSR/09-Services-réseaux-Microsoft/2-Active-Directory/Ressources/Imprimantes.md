# Imprimantes**🧰 Rôle de serveur d’impression**

Sur un **serveur Windows**, on installe le **service de rôle “Serveur d’impression”** :

🔧 Objectif : **centraliser la gestion des imprimantes**, qu’elles soient locales ou réseau.

📍 Résultat : une console unique pour superviser tous les périphériques d’impression du parc.



**🗂️ Imprimantes : vue logique vs physique**

📌 Il faut bien **distinguer la vue logique (imprimante installée/logique sur un poste)** de la **vue physique (le matériel réel connecté)**.

🖥️ Dans un réseau : les **postes client** impriment via des **imprimantes installées sur le serveur**, qui redirige ensuite vers le périphérique réel.



**📡 Types d’imprimantes**

- **Imprimante locale** : branchée en direct (USB) sur un poste ou serveur
- **Imprimante partagée** : une imprimante locale qu’on rend accessible via le serveur
- **Imprimante réseau** : imprimante autonome avec IP, carte réseau et file d’attente propre



**🧵 Notions clés à connaître**

- **Ports d’impression** 🛠️ : point de communication entre serveur et imprimante (ex : TCP/IP, USB, LPT)
- **Files d’attente** ⏳ : documents en attente de traitement par l’imprimante
- **Pilotes** ⚙️ : nécessaires sur le serveur pour permettre aux clients de s’y connecter sans souci
- **Formulaires** 📄 : formats prédéfinis de papier pour l'impression (A4, enveloppe, etc.)



**🖥️ Console MMC d'administration**

La console **“Gestion de l'impression”** (via MMC) permet :

👀 Vue globale sur les imprimantes, ports, pilotes, files d’attente

🔧 Ajout/suppression d’imprimantes, configuration de ports, installation de pilotes

📊 Surveillance des erreurs, files bloquées, imprimantes indisponibles



**📦 Déploiement via GPO**

On peut **déployer automatiquement des imprimantes** sur les postes via **stratégies de groupe (GPO)** :

➡️ Gain de temps pour les admins

➡️ Installation silencieuse, ciblée par utilisateur ou machine

➡️ Plus besoin de config manuelle sur chaque poste
