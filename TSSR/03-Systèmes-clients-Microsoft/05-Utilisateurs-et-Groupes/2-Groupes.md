# Groupes

## **🆔 1️⃣️Groupes et Identifiants (SID)**

✔ **Tout utilisateur** doit appartenir à **au moins un groupe** pour utiliser le système.

✔ Comme les utilisateurs, les **groupes sont identifiés par un SID (Security Identifier)**.

✔ Quand un utilisateur est ajouté à un groupe, **le SID du groupe est ajouté au jeton d’accès de l’utilisateur**.

### 🔹 **Le jeton d’accès**, c’est quoi ?

✅ Un fichier contenant **l’identité** de l’utilisateur et **les permissions associées** à tous ses groupes.

✅ Ce jeton est utilisé à chaque action nécessitant une vérification des droits. (Consultable via ***whoami /user***)



## **📂 2️⃣️Les Différents Types de Groupes**

Windows propose **3 grandes catégories de groupes** :

### **🔹 1. Groupes Locaux**

✔ Permettent de **gérer les autorisations d'accès aux ressources locales** (fichiers, dossiers, imprimantes…).

✔ Peuvent aussi donner des **privilèges d’administration**.

✔ Surtout utilisés par les **techniciens** pour organiser les droits des utilisateurs sur un seul ordinateur.


### **🔹 2. Groupes Prédéfinis**

✔ Définis par **Windows** et déjà configurés avec des rôles précis.

✔ Permettent de **déléguer des tâches spécifiques** sans donner les pleins pouvoirs.

✔ Exemples :

- **Backup Operators** → Gestion des sauvegardes
- **Network Configuration Operators** → Configuration réseau
- **Event Log Readers** → Lecture des journaux d’événements


### **🔹 3. Groupes de Sécurité Intégrés**

✔ Groupes automatiques créés par **Windows**.

✔ Les utilisateurs y sont ajoutés **sans intervention manuelle**.

✔ Exemples :

- **Authenticated Users** → Tous les utilisateurs connectés au domaine
- **Everyone** → Tout le monde, y compris les invités
- **Administrators** → Accès total au système



## **🎯 3️⃣️À quoi servent ces groupes ?**

✅ **Simplifier la gestion des droits** → Pas besoin de gérer chaque utilisateur individuellement.

✅ **Sécuriser les accès** → En limitant les privilèges inutiles.

✅ **Automatiser l’organisation** → Windows ajoute certains utilisateurs aux groupes automatiquement.

