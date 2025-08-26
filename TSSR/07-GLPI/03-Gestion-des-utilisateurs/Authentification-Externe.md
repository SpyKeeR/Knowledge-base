# Habilitations et Authentification AD dans GLPI

## 🧠 Objectifs
- Organiser la **visibilité des ressources** via les **entités** et la **récursivité**.  
- Gérer les **profils utilisateurs** et leurs habilitations.  
- Intégrer une **authentification externe** (LDAP/Active Directory) pour centraliser la gestion des comptes.  

---

## 👥 Comptes et profils utilisateurs
- GLPI peut gérer des **comptes locaux** créés directement dans l’interface.  
- Mais il est préférable de se **connecter à une base externe** pour éviter la duplication (LDAP, AD, IMAP, certificats).  
- Les utilisateurs ont un **profil** (droits) qui définit leurs accès :  
  - **Interface simplifiée** (utilisateurs finaux)  
  - **Interface standard** (techniciens)  

💡 Les profils + l’appartenance à une entité déterminent ce que chaque utilisateur peut voir ou faire.

---

## 🌍 Sources d’authentification possibles
| Type                           | Exemple                          |
|--------------------------------|----------------------------------|
| **LDAP/AD**                    | Active Directory, OpenLDAP       |
| **Messagerie (IMAP)**          | Gmail, Exchange                  |
| **Certificats X.509**          | Auth par certificat              |
| **Interne**                    | Base de comptes GLPI (par défaut)|

---

## ⚙️ Configuration d’une source AD/LDAP
📍 Menu : **Configuration > Authentification > Annuaires LDAP**

1. Choisir **Ajouter une source** (LDAP, IMAP, etc.)  
2. Sélectionner **Préconfiguration Active Directory** (facilite les réglages)  
3. Renseigner :  
   - **Nom** : ex. “Helptech.bzh”  
   - **Serveur** : IP ou nom DNS (ex. 192.168.10.10)  
   - **Port** : 389 (LDAP) ou 636 (LDAPS)  
   - **BaseDN** : chemin de recherche (ex. `OU=Users,DC=entreprise,DC=local`)  
   - **Bind DN** : compte dédié à la liaison (⚠️ pas un admin de domaine)  
   - **Mot de passe** : du compte LDAP  
   - **Champs** : `samaccountname` (login), `objectGUID` (ID unique), `mail`  

💡 Un bouton **Tester la connexion** permet de vérifier immédiatement la config.

---

## 🔁 Fonctionnement de la synchronisation
### 1. Première connexion
- Si l’utilisateur existe dans AD mais pas dans GLPI → création auto dans GLPI (import des infos de base).  
- Authentification avec identifiants Windows/AD.

### 2. Synchronisation continue
- À chaque connexion, GLPI **met à jour** les infos (mail, service, etc.) selon AD.  
- Possible aussi de lancer une **synchronisation manuelle** ou planifiée.  

### 3. Import en masse
- Permet d’ajouter plusieurs utilisateurs d’un coup.  
- Peut être filtré **par entité** ou **par date** (ex. seulement les nouveaux).  

---

## 🔐 Gestion des habilitations
- Les **droits** sont définis par le **profil** + l’**entité** d’appartenance.  
- La **récursivité** permet de donner aux entités enfants l’accès à des ressources partagées (ou non).  
- Un utilisateur peut :  
  - Voir uniquement son entité  
  - Voir plusieurs entités (si habilité)  
  - Avoir une vue globale (si profil + récursivité activée)  

---

## ⚠️ Points de vigilance
- Le **Bind DN** doit avoir seulement les droits nécessaires (lecture).  
- Le **filtre LDAP** doit exclure les comptes désactivés (exemple) :  
  ```
  (&(objectClass=user)(objectCategory=person)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))
  ```
- Toujours **tester la connexion** après modification.  
- Préférer l’usage d’un compte **dédié** à la liaison GLPI ↔ AD.  

---

## 🎯 Avantages de la liaison AD
- Plus besoin de gérer les mots de passe dans GLPI.  
- Comptes créés/synchronisés automatiquement.  
- Gestion **centralisée** et **cohérente** des accès.  
- Gain de temps et réduction des erreurs.  
