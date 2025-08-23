# Types

## **🖥️ 1️⃣️Les Utilisateurs Locaux**

Un **utilisateur local** est un compte **spécifique à un seul ordinateur**.

📌 Il est stocké dans la **base SAM (Security Account Manager)**, une base locale de l’ordinateur.

📌 Il **ne peut utiliser que les ressources** du PC sur lequel il est défini.

📌 Pour se connecter, il sélectionne son compte sur l’écran d’accueil et entre son **mot de passe**.



## **🌐 2️⃣️Les Utilisateurs du Domaine**

Dans un **réseau d’entreprise**, un utilisateur a besoin d’accéder à plusieurs **ordinateurs et serveurs**.

📌 Son compte est alors géré par un **annuaire centralisé**, appelé **Active Directory (AD)**.

📌 Cet annuaire est stocké sur un **Contrôleur de Domaine (DC-On-Premise) ou sur Entra ID (Cloud Azure)**.

📌 Lors de la connexion, l’ordinateur contacte le **DC** de manière sécurisée :

- 1️⃣ Il envoie l’**identifiant et le mot de passe**.

- 2️⃣ Le **serveur AD** vérifie l’identité et **autorise ou refuse** l’accès.

📌 L’utilisateur est identifié par un **SID (Security Identifier)**, un code unique.

📌 **L'authentification se fait sur le réseau grâce au protocole Kerberos.**



## **🔗 3️⃣️L’Intégration d’un Ordinateur au Domaine**

Pour qu’un utilisateur du domaine puisse se connecter, son ordinateur doit être **joint au domaine**.

📌 Une fois ajouté au domaine, l’ordinateur reconnaît les **comptes AD**.

📌 Par défaut, l’écran d’accueil propose la connexion au domaine.

📌 **Format d’identification** :

- **Utilisateur local** : entre directement son nom d’utilisateur et son mot de passe.
- **Utilisateur du domaine** : doit s’authentifier avec **son compte Active Directory**.



## **⚠️ 4️⃣ Ce qu’il faut savoir sur les Comptes Locaux et Domaines**

✔ **Les comptes locaux existent toujours**, même après l’intégration au domaine.

✔ Ils ne sont simplement **plus affichés sur l’écran de connexion** par défaut.

✔ Il est possible de s’authentifier sur **un autre domaine** si l’ordinateur est autorisé.

