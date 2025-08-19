# Règles - CyberEdu**📋 Importance de l'inventaire**

L'inventaire est **la base de toute stratégie de sécurité** :

- 🔎 Identifier les **actifs métiers** : données, applis, systèmes, matériels (PC, serveurs, switches, box, smartphones…).
- 📇 Identifier par **IMEI (mobiles)**, **MAC (matériel réseau)**.
- 🛠️ Inclure : logiciels, OS, versions, docs sensibles, accès réseau, interconnexions (ADSL, fibre, VPN, 4G, liaisons privées, satellites).
- 🧩 Ne pas oublier les **points d'entrée et de sortie réseau** = surface d’attaque.



**🛡️ Sécurisation du réseau interne**

- 🧱 Cloisonnement via **VLAN**, **VRF**, **sous-réseaux**. 🔐 Contrôle d’accès réseau **802.1X** → authn. avant connexion. / 🗂️ Centralisation des accès via serveur **RADIUS**.
- ⚠️ Attention au **BYOD** : terminaux perso = risques de malwares & fuites. / 🌐 Accès distants = sécurisés via **VPN**, **authentification forte** (TACACS/RADIUS).
- 🔁 Filtrage interne : créer une **matrice de flux** (zones, ports, protocoles autorisés). 🔥 **DMZ** pour exposer les services Internet + pare-feux, **IDS/IPS** pour détection/prévention.



**📶 Sécurisation du Wi-Fi >** 🔒 WPA2 + **CCMP** obligatoire et 🔑 Clé > 15 caractères alphanumériques.



**👥 Gestion des utilisateurs**

- 👤 1 utilisateur = 1 compte (pas de comptes partagés). 🕵️‍♂️ Traçabilité = indispensable pour les audits.
- 📜 Faire signer une **charte USI** : usage, responsabilités, sanctions. ⚙️ Attribution des droits via **gpedit.msc** (Windows).
- 🔄 Gérer cycle de vie utilisateur : création ➝ usage ➝ suppression (départ, changement de poste).
- 🧑‍💻 Définir les rôles : **Admin** : accès total. / **Utilisateur** : accès limité aux besoins. / **Invité** : accès minimal.



**🔐 Politique de mot de passe**

- ✔️ Complexité : ≥ 8 caractères (≥ 10 pour les admins). 🔁 Rotation tous les 6 mois. 🔎 Se baser sur les recommandations **ANSSI**. ⚠️ Pas de réutilisation sur plusieurs comptes. 🧠 Utiliser un **gestionnaire de mots de passe** (stockage chiffré recommandé). 🔐 Authentification renforcée : **biométrie, OTP, carte à puce, SSO**.



**🛑 Réagir aux menaces**

- 📩 Attention aux mails suspects, ne **jamais cliquer ni répondre**. > 🚫 En cas de menace : **ne pas céder, couper le contact, signaler aux autorités**.



**🧾 Supervision et journalisation**

- 📚 **Journalisation activée** : accès réussis/échoués, modifs sensibles. 👁️ Consulter les logs régulièrement.
- 🔔 Politique d’alerte : seuils critiques (espace disque, tentatives anormales), envoi via SMS, mail, etc.



**💾 Plan de secours & continuité**

⚠️ Prévoir **PRA (reprise)** et **PCA (continuité)** → testés & mis à jour régulièrement. 🔌 Redondance (alims, connexions), **sauvegardes régulières** des données critiques.
