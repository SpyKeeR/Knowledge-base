# Authentification ExterneNe **pas recréer manuellement** tous les comptes utilisateurs dans GLPI si tu as déjà une base d’authentification centralisée existante.

GLPI peut : Utiliser sa **propre base interne** (comptes créés dans GLPI directement) Ou se **connecter à une base externe** (LDAP/AD, serveur mail, certificat, etc.)

**🌍 Les sources d’authentification possibles**

GLPI peut s’interfacer avec plusieurs systèmes :

| **Type**                          | **Exemples**                    |
|-----------------------------------|---------------------------------|
| **LDAP**                          | OpenLDAP, Active Directory      |
| **Systèmes de messagerie (IMAP)** | Gmail, Exchange, etc.           |
| **Certificats X.509**             | Authentification par certificat |
| **Authentification interne**      | Base GLPI (par défaut)          |



**⚙️ Ajouter une source d’authentification externe >** 🛠️ Menu : **Administration > Authentification > Ajouter**

1.  **Type** : LDAP, IMAP, etc.
2.  **Préconfiguration AD** : Si tu utilises **Active Directory**, coche la préconfiguration dédiée : ça auto-remplit les bons filtres de recherche.
3.  **Nom du serveur** : adresse IP ou nom DNS de ton serveur LDAP/AD.
4.  **Base DN (BaseDN)** : C’est **le point de départ de la recherche** dans l’arbre LDAP. Ex : DC=tondomaine,DC=local
5.  **DN de liaison (Bind DN)** : Identité (DN complet) du compte **qui va faire les requêtes LDAP**. ❌ Évite les comptes admin de domaine ! ✅ Crée un compte spécifique, genre glpi_ldap.



**🔁 Fonctionnement de la synchronisation**

**🔹 1. Connexion utilisateur**

- Si l’utilisateur n’existe pas encore dans GLPI mais est présent dans la base externe (AD/LDAP) :
  - Il peut se connecter avec ses **identifiants de domaine**.
  - GLPI crée **automatiquement** un utilisateur local avec les infos importées (nom, mail, etc.).

**🔹 2. Synchronisation automatique**

- Si un attribut change dans AD (ex : nouvelle adresse mail), **GLPI peut le mettre à jour** à chaque connexion.
- Cette synchro repose sur des **attributs LDAP** comme : sAMAccountName → identifiant / objectGUID → identifiant unique / mail → courriel

**🧠 À retenir :**

- La **source LDAP devient la vérité** (GLPI suit ses infos). / La **connexion** de l'utilisateur déclenche **une mise à jour** locale dans GLPI. / Il est aussi possible de lancer des **synchronisations manuelles ou programmées**.

**🔑 Importance de la config LDAP**

✅ Type LDAP/AD bien sélectionné / ✅ Préconfiguration cochée si Active Directory / ✅ BaseDN correcte (ex : OU=Utilisateurs,DC=entreprise,DC=local) / ✅ DN de liaison bien défini (pas un compte admin !) / ✅ Mot de passe du compte LDAP / ✅ Champs LDAP bien configurés (sAMAccountName, mail, etc.)
