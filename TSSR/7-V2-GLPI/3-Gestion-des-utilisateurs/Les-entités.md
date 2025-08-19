# Les entitésC’est un **chapitre super important** pour bien comprendre comment GLPI gère l’organisation des données, des utilisateurs et des permissions. Les entités, c’est un peu le **squelette structurel** du système.



**🧩 Notion d'entité**

Une **entité**, c’est comme un **compartiment** dans GLPI. Elle permet d’isoler des données, des utilisateurs, des objets… pour que chacun reste dans **son périmètre**.

Tu peux l’utiliser pour représenter :

- 🔹 Un **service interne** : Ex. Commercial, RH, DSI…
- 🔹 Un **client externe** : Si tu es une ESN avec plusieurs clients gérés depuis une seule instance GLPI.

💡 Les entités sont hiérarchiques ! Tu peux créer :

- Des **sous-entités** (par ex. une entité “Siège” avec “Support”, “Infra” et “Développement” en dessous).
- Et appliquer des droits qui **se propagent** de manière récursive (ou pas, selon les réglages).

🔐 **Avantage clé** : chaque entité peut avoir ses **propres techniciens**, ses **propres règles**, sa **propre configuration**, ses **propres tickets**.

Un technicien peut donc **voir ou ne pas voir** certaines entités selon ses habilitations.

**⚙️ Paramétrage d’une entité**

🧭 Menu d’accès : **Accueil > Administration > Entité**

Tu peux gérer ici :

- **Entité** : Définition de base (nom, type, parent…)
- **Entités** : Création/visualisation des **sous-entités**
- **Adresse** : Infos postales, géolocalisation
- **Informations avancées** : Comportement dans l’inventaire, LDAP, etc.
- **Notifications** : Gestions des mails automatiques *propres à l'entité*
- **Assistance** : Paramétrage dédié aux tickets (modèles, calendriers…)
- **Parc** : Modèle de transfert, auto-remplissage de date, etc.
- **Interface** : Personnalisation visuelle (ex : CSS pour un thème spécial)
- **Utilisateurs** : Qui est dedans ? Quels droits ? Récursivité activée ?
- **Règles** : Règles spécifiques (ex : d’affectation automatique)
- **Documents & Notes** : Liés uniquement à cette entité
- **Base de connaissance** : Contenu associé à cette entité
- **Historique** : Log des actions/modifications liées à l’entité



**🎯 Exemple concret pour mieux visualiser**

Imagine que tu es dans une **DSI mutualisée** qui gère deux sociétés..

➡️ Chacune ne voit **que ses propres données**.

➡️ Tu peux même avoir un technicien qui voit **Société A + Société B**, s’il est habilité sur les deux entités.

➡️ Tu peux avoir un superviseur qui a une vue **globale**, si tu actives la récursivité sur une entité parent.



**📄 Contenu d’une fiche "Entité"**

Voici ce que tu peux renseigner/modifier quand tu ouvres une entité dans GLPI :

| **Onglet** | **Description** |
|----|----|
| **Entité** | Nom, description, entité parente éventuelle |
| **Entités** | Liste + création de sous-entités |
| **Adresse** | Adresse postale + carte |
| **Infos avancées** | LDAP, visibilité dans les règles, intégration externe |
| **Notifications** | Gestions spécifiques aux mails (template, timing…) |
| **Assistance** | Config de ticketing : gabarits, enquête satisfaction… |
| **Parc** | Réglages sur modèles, dates, transferts |
| **Interface** | CSS personnalisé, ajustements visuels |
| **Utilisateurs** | Qui est rattaché ? Quels profils ? Accès aux sous-entités ? |
| **Règles** | Affectation auto, imports, etc. |
| **Documents / Notes** | Stockage local à l’entité |
| **Base de connaissances** | Articles visibles selon l’entité |
| **Historique** | Traçabilité des modifications |
