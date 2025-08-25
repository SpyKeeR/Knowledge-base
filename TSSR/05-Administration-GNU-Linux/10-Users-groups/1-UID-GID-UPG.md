# UID/GID/UPG

## **🔐 Notions clés : UID & GID**

### 🔹 **UID (User ID)** 
Chaque utilisateur Linux possède un **identifiant unique**, appelé UID. Ce n’est pas le *login* qui fait foi, mais bien ce nombre 🔢. C’est lui que le système utilise pour identifier les utilisateurs en interne.

### 🔹 **GID (Group ID)**
De la même manière, chaque **groupe** possède un identifiant unique, le **GID**. L'utilisateur appartient à un **groupe principal** (via son GID) et peut aussi faire partie de **groupes secondaires**.



**🧠 Petit rappel essentiel :**

👉 Le **login**, c’est juste un nom sympa pour nous humains.

👉 Le **système**, lui, se base uniquement sur les **UID et GID** pour accorder ou refuser l’accès à un fichier ou une commande.



## **🧪 Types d’utilisateurs sous Linux :**

### 👑 **root (UID 0/GID 0)** 
C’est le super-utilisateur. Il a tous les droits… et donc tous les risques. On l’utilise **avec modération** ⚠️.



### 🛠️ **Utilisateurs systèmes (UID 1 à 999/GID 1 a 999)** 
Des comptes techniques pour les services et démons (comme mysql, sshd). Tu ne les utilises pas directement.

- **System Account** : comptes système internes sans shell (UID < 100 ou 500 ou 1000 selon distro)
- **Service Account** : créés pour des services (ex : www-data, mysql) → souvent sans login ni mot de passe

### 🎯 **Différence service/system**
- *System* = structure OS (daemon, mail, syslog)
- *Service* = liés à des applications (Apache, DB…)



### 👨‍💻 **Utilisateurs classiques (UID ≥ 1000 / GID ≥ 1000)**
C’est toi, moi, et tous les autres humains. Sur les distributions modernes, c’est souvent à partir de **UID 1000** qu'on commence à créer les comptes utilisateurs.



## **🔄 Convention UPG : User Private Group**

Sur beaucoup de systèmes Linux, quand tu crées un utilisateur, un **groupe du même nom** est automatiquement créé et affecté comme **groupe principal**. Exemple :

- utilisateur : maxime
- groupe principal : maxime
- GID différent de UID (mais même nom)

🎯 Avantage : ça permet d’avoir une meilleure isolation des permissions et facilite la gestion des accès collaboratifs sans tout casser.



## **🔐 À propos de l’utilisateur root :**

🚫 Se connecter directement en root (surtout en GUI) est souvent désactivé pour des raisons de sécurité.

✅ À la place, on **monte temporairement en privilèges** avec :

- `sudo` (commande ponctuelle)
- `su` (pour basculer dans un shell root)


