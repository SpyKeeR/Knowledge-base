# Niveaux de service

**📊 Accueil / Configuration / Niveaux de services (Sous-menu dédié aux niveaux de services)**

### **🎯 Objectifs des niveaux de services**

- Enregistrer les **contrats clients** (SLA, OLA),
- Gérer les **horaires d’ouverture** (via un calendrier attribué),
- Définir les **niveaux d’escalade** automatiques (pour ne jamais laisser un ticket sans suivi),
- Faire évoluer automatiquement le cycle de vie des tickets (de l’ouverture à la clôture),
- Respecter les **délais d’intervention** (prise en charge, résolution)

### **✍️ Création d’un niveau de service**

- **Nom** : donne un nom clair au niveau de service
- **Commentaires** : ajouter des précisions utiles
- **Calendrier** : associe un calendrier pour définir les heures/jours où ce niveau s’applique (exemple : horaires bureau du client)

### **⚙️ Propriétés d’un niveau de services**

Modifier les infos de base (nom, commentaires, calendrier) : 
  - **SLAs** : liste les SLA rattachés (les accords de service avec le client)
  - **OLAs** : liste les OLAs liés (Operational Level Agreements, accords internes)

## **⚡ Création d’un SLA / OLA**

- **Nom** : nom du SLA
- **SLM** : le niveau de service parent où tu ajoutes ce SLA
- **Commentaires** : notes éventuelles
- **Type** :
  - **TTO (Time To Own)** : délai max pour **prendre en charge** un ticket
  - **TTR (Time To Resolve)** : délai max pour **résoudre** un ticket
- **Durée maximale** : durée limite en minutes/heures/jours selon type
- **Récursivité** : est-ce que ce SLA se répète régulièrement ?

### **🔁 Modifier un SLA**

- Modifier infos paramétrées
- Voir la liste des règles où il s’applique
- Voir la liste des tickets concernés

## **🚨 Gestion des niveaux d’escalade automatique**

Les niveaux d’escalade permettent de faire avancer automatiquement le ticket dans son cycle de vie en fonction du temps, pour éviter qu’un ticket stagne trop longtemps.

### **✍️ Création d’un niveau d’escalade**

- **Nom** : nom du niveau d’escalade
- **Exécution** : moment où il se déclenche (exemple : -10m avant la fin du SLA) — attention, c’est en négatif, ça veut dire combien de temps il reste avant la fin du délai.
- **Actif** : oui/non, pour activer ou désactiver ce niveau
- **SLA** : SLA dans lequel ce niveau est créé
- **Opérateur logique** :
  - **et** : tous les critères doivent être remplis
  - **ou** : au moins un critère doit être rempli
- **Critères** : conditions qui sélectionnent les tickets concernés (ex : priorité haute, client X)
- **Actions** : ce que tu fais sur le ticket à ce niveau (ex : envoi de mail, changement d’état, affectation à un autre technicien)

### **⚙️ Modifier un niveau d’escalade**

- Modifier le nom, l’exécution, le SLA
- Modifier les critères ou les actions
- Activer/désactiver

