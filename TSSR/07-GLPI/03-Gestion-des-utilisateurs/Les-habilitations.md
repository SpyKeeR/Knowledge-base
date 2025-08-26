# Habilitations dans GLPI

## 🛡️ Définition
Une **habilitation** détermine :
- **Ce qu’un utilisateur peut faire** → via un **profil** (droits fonctionnels)  
- **Où il peut le faire** → dans une **entité**  
- **Si ça s’applique aux sous-entités** → via la **récursivité**

👉 Un utilisateur peut avoir **plusieurs habilitations** (ex. technicien dans une entité, simple utilisateur dans une autre).

---

## 🧱 Composition d’une habilitation
1. **Profil** : définit les actions autorisées (lecture, création, gestion tickets, admin…).  
2. **Entité** : périmètre d’action (service, filiale, client).  
3. **Récursivité** : étend (ou non) les droits aux sous-entités.  

**Exemple :**  
- Dans la filiale A → rôle "Utilisateur simple"  
- Dans la filiale B → rôle "Technicien support"

---

## 🔄 Modes d’attribution

### ✅ Habilitations statiques (manuelles)
- Ajoutées directement dans la fiche utilisateur.  
- 📍 Menu : **Accueil > Administration > Utilisateurs > Habilitations**  
- Étapes : choisir l’entité → le profil → activer ou non la récursivité → ajouter.  
- Simple mais fastidieux si beaucoup d’utilisateurs.

---

### 🔁 Habilitations dynamiques (automatiques)
Attribuées via le **moteur de règles**.  
📍 Menu : **Accueil > Administration > Règles > Règles d’affectation d’habilitations**

**Fonctionnement :**
- Les règles sont lues **à chaque connexion utilisateur**.  
- Une règle = un critère clair + une action.  
- Évite les règles trop complexes → privilégier la lisibilité.

**Exemples :**
- Tous les comptes dont l’email contient `@entreprise.fr` → profil "Utilisateur" dans l’entité "Siège".  
- Tous les membres du groupe AD "Techniciens" → profil "Technicien" avec récursivité activée.

**Étapes de création d’une règle :**
1. Créer une nouvelle règle, lui donner un nom explicite.  
2. Définir les critères (ex. mail contient "client-a.com", groupe AD = IT).  
3. Choisir les actions (profil, entité, récursivité).  
4. Activer la règle et tester.  

---

## 🌳 La récursivité
- **Sans récursivité** : accès limité uniquement à l’entité affectée.  
- **Avec récursivité** : l’utilisateur accède aussi aux objets des sous-entités.  

**Exemple :**
- Entité "France" avec sous-entités "Paris" et "Lyon".  
- → Si récursivité activée : accès France + Paris + Lyon.  
- → Si désactivée : accès seulement à France.

---

## 🎯 Bonnes pratiques
- **Toujours documenter** les règles (nom clair, description).  
- Préférer des règles simples et indépendantes.  
- Bien tester avec différents utilisateurs avant déploiement massif.  
- Attention aux opérateurs de critères :  
  - `est égal à` = strict  
  - `contient` = plus souple mais peut générer des faux positifs.  

💡 En résumé :  
Les habilitations = **clé de la sécurité et de l’organisation** dans GLPI.  
Leur bonne gestion permet de cloisonner les accès tout en automatisant au maximum via LDAP/AD et règles dynamiques.
