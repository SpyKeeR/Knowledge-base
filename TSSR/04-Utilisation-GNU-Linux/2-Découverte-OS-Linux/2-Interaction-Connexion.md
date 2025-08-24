# Interaction/Connexion

Pour utiliser un système Linux, il faut avoir un **compte utilisateur** avec :

- 👤 un **identifiant (login)**
- 🔑 un **mot de passe**



## **🧱 1. Connexion locale (console)**

- Utilisée lors de l’**installation** ou en **cas de panne** grave
- Mode **terminal uniquement** (sans interface graphique)
- Aussi appelée **"boîte noire"** → à éviter autant que possible car fonctionnalités limitées



## **🪟 2. Connexion avec interface graphique**

- Via un **écran de login** (GUI) si l’environnement est installé (Gnome, KDE…)



## **🌍 3. Connexion distante (SSH)**

### **Depuis un terminal (ex : sur Windows récent) >** Commande : ssh utilisateur@adresse_ip

👉 Permet de se connecter à distance à un serveur Linux

### **Depuis un logiciel SSH (Windows)**

#### 🧰 **PuTTY** :

- Logiciel SSH **libre** (licence MIT) et très utilisé
- Permet aussi : 🔁 **Transfert de fichiers** via **PSCP** et **PSFTP /** 📁 Connexion **SFTP**
- Tu tapes l’IP + le port (par défaut : 22) et tu te connectes

#### 🧩 **Alternatives à PuTTY** :

- **Kitty** → fork amélioré avec fonctions supplémentaires
- **MobaXterm** → interface moderne, gère aussi le **RDP** (connexion à des serveurs Windows)

🔧 Ces outils permettent de gérer plusieurs connexions (SSH, RDP...) depuis **un seul logiciel**

### **🔚 Commandes pour fermer une session :**

- exit → fonctionne dans presque tous les shells
- logout → compatible avec **Bash**, **mais pas avec sh (Bourne)**
- Ctrl + D → raccourci clavier pour envoyer un **EOF** (end of file) et quitter le Shell

