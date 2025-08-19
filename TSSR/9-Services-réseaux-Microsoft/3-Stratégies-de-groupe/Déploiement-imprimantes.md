# Déploiement imprimantes**🖨️ Serveur d’impression : le point de départ**

Le **serveur d'impression Windows** est le cœur de la gestion centralisée des imprimantes dans un domaine.

🎛️ Il te permet de :

- Ajouter et partager les imprimantes
- Gérer les files d’attente
- Installer et maintenir les pilotes
- **Déployer les imprimantes via GPO directement depuis la console !**



**🎯 Déploiement via le serveur d'impression**

📍 Dans la console "Gestion de l'impression" > clic droit sur l'imprimante > **Déployer via stratégie de groupe**

Tu peux :

- Associer une imprimante à une **GPO existante**
- Ou **créer une nouvelle GPO** directement depuis l’interface
- Choisir si le déploiement cible les **utilisateurs ou les ordinateurs**

🧠 L'avantage ? Tout se fait depuis le **serveur d’impression**, pas besoin d’ouvrir GPMC manuellement. C’est rapide, centralisé et bien intégré à l’environnement.



**🧩 Alternative via GPMC**

Si tu préfères ou si tu as besoin de plus de contrôle :

- Ouvre la **GPMC**
- Va dans **Configuration utilisateur / ordinateur > Préférences > Paramètres Windows > Imprimantes**
- Ajoute une nouvelle imprimante partagée du domaine
- Spécifie si elle est par défaut, ou uniquement visible

🛠️ C’est un peu plus long, mais utile si tu veux intégrer ça dans une GPO plus large (avec d'autres paramètres comme le fond d'écran, le proxy, etc.)



**🔐 Gestion des droits : attention aux permissions**

Que tu passes par le serveur d'impression ou la GPMC, **les droits utilisateurs sur les imprimantes sont fixés côté serveur** :

- ⚠️ Pas tous les utilisateurs peuvent gérer les files d’attente, suspendre des tâches, ou accéder aux propriétés
- 🔐 Bien penser à sécuriser les imprimantes partagées avec des **droits NTFS et de partage adaptés**
