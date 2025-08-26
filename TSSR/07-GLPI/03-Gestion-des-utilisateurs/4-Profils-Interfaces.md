# Profils et Interfaces dans GLPI

## 👥 C’est quoi un profil ?
Un **profil** = un ensemble de **droits et d’autorisations** qui définit :
- **Qui peut faire quoi** (actions autorisées)
- **Dans quelle interface** (standard ou simplifiée)
- **Sur quel périmètre** (selon l’entité et la récursivité)

👉 Un utilisateur peut avoir **plusieurs profils** selon son rôle dans différentes entités.

---

## 🗂️ Profils standards fournis par GLPI
Voici les profils disponibles par défaut (tous sont modifiables, mais ⚠️ **ne pas supprimer Super-Admin** !) :

| **Profil** | **Droits Parc** | **Droits Tickets** | **Interface** | **Usage typique** |
|----|----|----|----|----|
| **Super-Admin** | ✅ Tout | ✅ Tout | Standard | Dieu dans GLPI 🤣 |
| **Admin** | ✅ Large | ✅ Large | Standard | Gestion avancée (sauf config critique) |
| **Supervisor** | ✅ Comme Tech | ✅ + gestion équipe | Standard | Chef d’équipe IT |
| **Technician** | ✅ Créa/Modif/Supp | ✅ Cycle de vie complet | Standard | Technicien support |
| **Hotliner** | ❌ | ✅ Cycle de vie complet | Standard | Hotline / Helpdesk |
| **Observer** | ✅ Lecture seule | 🔶 Participe (limité) | Standard | Suivi / reporting |
| **Self-Service** | ❌ | ✅ Ses propres tickets | Simplifiée | Utilisateurs finaux |
| **Read-only** | ✅ Lecture seule | ❌ | Standard | Consultation pure |

🔶 Cas particulier → **Observer** peut participer à un ticket, mais n’a pas tous les droits de gestion.

---

## 🔐 Droits configurables dans un profil
Pour chaque **module GLPI**, on peut attribuer des droits précis :

- **Parc** : gestion du matériel et des inventaires.  
- **Assistance** : création, suivi et résolution des tickets.  
- **Cycle de vie** : gestion des statuts de tickets (de l’ouverture à la clôture).  
- **Gestion** : contrats, licences, fournisseurs, budgets…  
- **Outils** : FAQ, base de connaissances, projets, tâches.  
- **Administration** : entités, utilisateurs, groupes, règles, profils.  
- **Configuration** : lieux, calendriers, intitulés, paramètres système.  

👉 Chaque module accepte différents niveaux de droits :  
- Lecture | Création | Mise à jour | Suppression | Purge (suppression définitive)

---

## 💻 Les interfaces GLPI

### 1. Interface standard
- Complète, destinée aux **techniciens et administrateurs**.  
- Tous les modules y apparaissent (selon les droits du profil).  
- 3 vues disponibles :  
  - *Personnelle* : ce qui concerne l’utilisateur.  
  - *Groupe* : ce qui concerne son équipe.  
  - *Globale* : tout ce qu’il est autorisé à voir.  

### 2. Interface simplifiée
- Ultra légère, destinée aux **utilisateurs finaux**.  
- Fonctionnalités limitées à :  
  - Créer un ticket  
  - Suivre ses propres tickets  
  - Réserver du matériel  
  - Consulter la FAQ  
- 👉 Utilisée surtout avec le profil **Self-Service**.  
⚠️ Ne jamais attribuer cette interface à un profil admin.

---

## 🧠 Exemple concret (scénario d’entreprise)
- **Technicien IT interne** → Profil *Technician* → interface standard.  
- **Superviseur IT** → Profil *Supervisor* → interface standard avec gestion d’équipe.  
- **Hotliner** → Profil *Hotliner* → interface standard (tickets uniquement).  
- **Employé classique** → Profil *Self-Service* → interface simplifiée (tickets personnels + FAQ).  

🎯 Résultat : chacun a **l’outil adapté à son rôle**, la sécurité est respectée, et les erreurs sont limitées.
