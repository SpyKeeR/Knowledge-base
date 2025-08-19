# Gestion des actifs IT➡️ C’est la pratique qui consiste à **recenser, suivre et gérer** tous les **actifs informatiques** tout au long de leur cycle de vie (achat, utilisation, maintenance, mise au rebut). Un **actif**, c’est **tout ce qui a une valeur pour l’IT** : matériel, logiciel, licences, documents techniques, etc.

🎯 Objectif :

- **Connaître ce que possède l’organisation**
- **Gérer les coûts, les risques et les performances**
- S’assurer qu’on peut **agir vite** en cas de problème ou de changement



**🧱 Qu’est-ce qu’un CI (Configuration Item) ?**

Un **CI** (ou Élément de Configuration) est **un composant** (physique, logique ou documentaire) **que l’on veut suivre dans la CMDB**.

💡 La **CMDB** (Configuration Management Database) est **la base de données** qui contient **tous les CI** et **leurs relations**.

Exemples de CI : 💻 Ordinateur / 🧾 Licence logicielle / 🖨️ Périphérique (imprimante, scanner…) / 🧠 Composant serveur (RAM, CPU…) / 📦 Baie de stockage / 📂 NAS / 🌐 Switch, routeur, pare-feu / 📄 Documentation technique / 🧮 Base de données / 🏢 Salle serveur, local de stock



**🧬 Exemple de structure d’un CI**

Un CI a des **attributs** pour le décrire et des **liaisons** pour savoir à quoi il est connecté ou dépendant.

📌 Attributs communs à tous les types de CI : **Identifiant unique, Nom, Description, Localisation physique/logique, Liaisons avec d’autres CI,** Exemple : Un PC (CI) peut être relié : à un utilisateur 👤, à une imprimante partagée 🖨️, à un serveur de domaine 🌐



**💻 Types de CI : Attributs spécifiques**

**Ordinateur 🖥️:** Fabricant (HP, Dell…), Utilisateur principal, Type (portable, fixe…), Modèle, Numéro de série,Adresse IP, OS installé,

**Périphérique 🖨️:** Fabricant, Modèle / Type (imprimante, scanner…), Numéro de série, Adresse IP (si réseau)

**Application / Logiciel 📦 :** Éditeur (Microsoft, Adobe…), Utilisateur clé, Type (local, SaaS…), Mainteneur (personne/équipe qui gère le logiciel), Nombre de licences



**🔄 Lien entre CI et gestion des changements**

Quand tu fais une **modification** sur un CI (ex : remplacer une barrette RAM, changer un OS, migrer un serveur), tu dois : **Documenter ce changement** dans la CMDB, **Mettre à jour l’état du CI, Créer une trace pour le suivi** (ce qu’on appelle une "trace de changement")

📌 Ce lien entre les pratiques de **gestion de parc**, de **gestion des changements** et de **configuration** est **central en ITIL** : on ne change pas un composant sans suivre ses impacts et sa traçabilité !



**👀 Mise en situation terrain**

Tu travailles dans une PME. Un commercial change de poste et récupère un nouveau PC.

Voici ce que tu fais : Tu identifies le PC (CI) dans la CMDB, Tu modifies son **attribution à un nouvel utilisateur**, Tu mets à jour son **adresse IP** (il a changé de bureau), Tu ajoutes une remarque : « Prévu pour migration Windows 11 le 04/06 », Tu crées un **changement planifié** pour cette migration.

Tu as ainsi suivi **tout le cycle de l'actif** dans le respect d’ITIL ✅
