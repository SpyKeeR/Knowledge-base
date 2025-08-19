# Liaison Active Directory- **Où ? >** Accueil > Configuration > Authentification > Annuaires LDAP
- C’est ici que tu vas configurer la connexion entre GLPI et l’annuaire AD de ton entreprise, grâce au protocole LDAP.
- GLPI va lire les infos des utilisateurs directement dans AD, ce qui facilite la gestion (authentification, import utilisateurs, droits…).

**⚙️ Paramétrage de la liaison LDAP**

- Tu choisis une **préconfiguration “Active Directory”** (pour simplifier les réglages).
- Ensuite, tu remplis :
  - **Nom** : un nom simple pour identifier ton annuaire (exemple “Helptech.bzh”)
  - **Serveur** : IP ou nom DNS du serveur AD (ex : 192.168.10.10)
  - **Port** : généralement 389 (port LDAP par défaut)
  - **BaseDN** : le chemin dans AD où se trouvent les utilisateurs
  - **Utiliser bind** : Oui (permet d’authentifier GLPI auprès d’AD)
  - **DN du compte** : un compte AD créé pour cette liaison, par ex CN=GLPI_connect_AD…
  - **Mot de passe** : le mot de passe de ce compte
- Les filtres et champs d’identification (ex : samaccountname) restent souvent par défaut.
- Tu peux **tester la connexion** pour vérifier que tout marche bien.



**👤 Import et synchronisation des utilisateurs**

- **Import individuel**
  - Lorsqu’un utilisateur se connecte la première fois via AD, son compte est créé automatiquement dans la base locale GLPI.
  - Les infos (nom, email…) sont synchronisées depuis AD.
  - L’authentification se fait via LDAP, plus besoin de gérer le mot de passe dans GLPI.
- **Import en masse**
  - Tu peux importer plusieurs utilisateurs d’un coup (pratique pour démarrer ou faire un gros nettoyage).
  - La synchronisation peut se faire **entité par entité** (pour segmenter les utilisateurs par service).
  - On peut aussi filtrer par date, utile pour synchroniser seulement les nouveaux ou modifiés.
- **Synchronisation**
  - Elle met à jour les utilisateurs déjà importés avec les dernières infos de l’AD.
  - Ça évite d’avoir des infos périmées (exemple : si un utilisateur change d’adresse mail dans AD, ça se met à jour dans GLPI).



**🔥 Pourquoi ça déchire ?**

Sans liaison AD, tu dois créer les utilisateurs un par un dans GLPI, gérer les mots de passe, les profils, etc. Avec AD : Les utilisateurs peuvent se connecter avec leurs identifiants Windows, Tu importes automatiquement les infos et groupes, Tu gagnes un temps fou et évites les erreurs de saisie, Tu centralises la gestion des accès.



**⚠️ Points à surveiller**

- Le compte DN que tu utilises pour la liaison doit avoir les droits nécessaires pour lire les infos utilisateurs dans AD.
- Le filtre LDAP doit exclure les comptes désactivés (exemple du filtre dans la config :  
  (&(objectClass=user)(objectCategory=person)(!(userAccountControl:1.2.840.113556.1.4.803:=2))) exclut les comptes désactivés).

- Pense à tester la connexion régulièrement.

**📋 Exemple concret de config AD dans GLPI**

- **Nom** : Helptech.bzh
- **Serveur** : 192.168.10.10
- **BaseDN** : OU=_Societe_Helptech,DC=helptech,DC=bzh
- **DN du compte** : CN=GLPI_connect_AD,CN=Users,DC=helptech,DC=bzh
- **Mot de passe** : (caché pour sécurité)
- **Champ identifiant** : samaccountname (nom de connexion Windows)
- **Champ synchronisation** : objectguid (identifiant unique AD)
- Avec ça, GLPI sait où chercher, avec quel compte et comment reconnaître les utilisateurs.
