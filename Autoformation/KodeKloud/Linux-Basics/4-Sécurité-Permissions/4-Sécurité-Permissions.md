# 4 - Sécurité/Permissions🛡️ **Concepts de sécurité**
- **Access Controls** : gestion des droits d'accès aux fichiers, ressources, et services via des ACLs, permissions, groupes.
- **PAM (Pluggable Auth Modules)** : chaîne modulaire qui contrôle l’authentification, le verrouillage de compte, l'expiration de mot de passe, etc.
- **Network Security** : sécurisation du trafic réseau via pare-feux, filtrage IP, VPN, IDS/IPS.
- **SSH Hardening** : désactiver root login, changer le port par défaut, activer les clés, limiter les utilisateurs.
- **SELinux** : sécurité obligatoire (MAC) → règles très strictes sur accès fichiers/procès → Enforcing, Permissive, Disabled.



👥 **Fichiers système liés aux comptes**

- **passwd** ➜ USERNAME:PASSWORD:UID:GID:GECOS:HOMEDIR:SHELL → identifiants de base
- **shadow** ➜ USERNAME:PASSWORD:LASTCHANGE:MINAGE:MAXAGE:WARN:INACTIVE:EXPDATE → mots de passe chiffrés & politiques
- **group** ➜ GROUPNAME:PASSWORD:GID:MEMBERS → groupe et membres
- **gshadow** ➜ même but que shadow mais pour groupes → mot de passe de groupe, admins, permissions



🧾 **Commandes utiles comptes et groupes**

- **id** ➜ affiche UID, GID, groupes du user
- **getent** ➜ interroge la base passwd, group, etc. comme si c’était un DB → getent passwd, getent group



👤 **Types de comptes**

- **User Account** : compte utilisateur standard (UID ≥ 1000)
- **Superuser** : root (UID 0), tous les droits
- **System Account** : comptes système internes sans shell (UID < 100 ou 500 ou 1000 selon distro)
- **Service Account** : créés pour des services (ex : www-data, mysql) → souvent sans login ni mot de passe

🎯 **Différence service/system** : *System* = structure OS (daemon, mail, syslog), *Service* = liés à des applications (Apache, DB…)
