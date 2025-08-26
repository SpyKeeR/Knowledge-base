# Les intitulés🧭 **À quoi servent les intitulés ?** ↳ Accueil > Configuration > Intitulés

Les **intitulés** dans GLPI sont **des listes personnalisables** utilisées dans **les menus déroulants**, **les filtres de recherche**, et les **fiches d’objets**. 👉 En gros, c’est grâce à eux que tu peux adapter GLPI à ton environnement réel (noms, types, statuts, modèles, etc.) 💡 **Contexte concret :** T’es en train d’inventorier un nouveau parc. Les technos que tu utilises ne sont pas dans les listes par défaut ? Tu les ajoutes ici ! Tu as un type spécifique de câblage dans ton entreprise ? Hop, un intitulé.



🛠️ **Fonctionnement global**

Tu retrouveras les intitulés **classés par catégories**. Voici comment ça s’articule : **🧰 Général : Lieux** : les **emplacements physiques** des objets (≠ entités), **Statuts** : exemple → *Installé / En maintenance / En panne / Recyclé,* **Fabricants** : Canon, HP, Lenovo, etc., **Listes noires** : infos à éviter (ex. adresses mail bannies), **Contenu de courriel interdit** : pour bloquer certains termes dans les mails d’assistance



📦 **Intitulés par grandes familles**

**💻 Machines virtuelles :** Systèmes/modèles de virtualisation, États des VM

**🧬 Systèmes d'exploitation :** OS, versions, service packs, architectures, éditions, noyaux…

**📦 Logiciels :** Catégories des logiciels (bureautique, sécurité, etc.)

**🔐 Authentification :** Gestion des identifiants via HTTP (cas SSO / LDAP)

**🧩 Autres :** Fournisseurs USB, PCI…

**🎯 Assistance (ITIL) :** Catégories de demandes, tâches, solutions, projets, évènements…, Très utile pour organiser le **Helpdesk**

**📚 Gestion :** Rubriques documentaires, criticité, types de contrats ou de bases de données

**🌐 Réseau :** Interfaces, VLANs, domaines, fibres, opérateurs…

**👤 Utilisateur :** Titres et catégories d’utilisateurs

**🔌 Alimentation / Énergie :** Prises, alimentations, types d'énergie

**🛠️ Types :** Types de tout ce qui existe : imprimantes, moniteurs, serveurs, cartouches, téléphones, disques durs, composants…

**🧪 Outils :** Catégories de la base de connaissances

**🧷 Câblage :** Types de câbles, torons, prises…

**🧾 Habilitations :** Règles d’affectation d’autorisations (souvent couplé à LDAP)

**📱 Applicatifs :** Types et environnements logiciels

**🖨️ Modèles :** Modèles de tous les objets matériels (PC, moniteurs, périphériques…)

**📆 Calendriers :** Périodes de travail et de fermeture (utile pour SLA ou planification)

**🌍 Internet :** Domaines, réseaux IP, Wifi, noms réseau

**🔁 Unicité :** Contrôle des doublons (tu définis ce qui doit être unique)

**🎥 Caméra :** Formats d’image utilisés



🎓 **Exemple pratique**

Tu veux ajouter un nouveau type de matériel réseau appelé *“Routeur 5G Pro”*. 🧩 Tu vas dans : Configuration > Intitulés > Types > Types de matériels réseau ➕ Tu ajoutes : **Routeur 5G Pro** ✅ Désormais, tu peux le sélectionner lors de la création ou modification d’un objet. Même chose si tu veux suivre des modèles très spécifiques, créer de nouveaux statuts (*En transit*, *À recycler*), ou gérer des types de câbles comme *CAT8 blindé*.
