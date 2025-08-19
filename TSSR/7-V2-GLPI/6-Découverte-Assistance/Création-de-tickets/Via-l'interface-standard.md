# Via l'interface standard**Menu :** Assistance > + Créer un ticket ou Assistance > Tickets > + Ajouter 🔒 Accessible uniquement aux utilisateurs autorisés (profil "Technicien", "Super-Admin", etc.)

**🧭 Structure du formulaire > 🟩 Partie centrale (contenu principal du ticket) 🟦 Partie droite (paramètres détaillés)**

**🟩 Partie centrale – Contenu du ticket**

| **Champ** | **Description** |
|----|----|
| **Entité** | Le ticket sera rattaché à l'entité sélectionnée. ⚠️ À vérifier dès le départ |
| **Titre** | Intitulé court, clair et compréhensible |
| **Description** | Détails de la demande ou de l’incident. Peut être préremplie par un **gabarit** (template) |
| **Gabarits de tickets** | Peuvent guider l’utilisateur, préremplir des champs ou en masquer d'autres |
|  |  |

**🟦 Partie droite – Paramètres détaillés**

**📁 Ticket**

| **Champ** | **Description** |
|----|----|
| **Entité** | Entité GLPI à laquelle rattacher le ticket |
| **Date d’ouverture** | Automatique ou manuelle |
| **Type** | Incident ou Demande |
| **Catégorie** | ITIL, déclenche l’usage d’un gabarit |
| **Statut** | Géré automatiquement (ex. : Nouveau, En cours, Résolu...) |
| **Source de la demande** | Canal utilisé (mail, téléphone, portail, etc.) |
|  |  |

**🔧 Éléments liés**

- **Tickets Liés** : Possibilité d’associer plusieurs tickets ensemble.
  - La résolution du ticket principal peut clôturer les tickets liés
- **Eléments Liés (CI)** : Ordinateurs, Imprimantes, Périphériques…)
- **Niveaux de service (SLA)** ➡️ Souvent remplis par des règles automatiques.
  - TTO (Time To Own) : Délai pour **la prise en charge.**
  - TTR (Time To Resolve) : Délai pour **résoudre** le ticket.

**🟢 Finaliser**

👉 Une fois le formulaire rempli, cliquer sur **+ Ajouter** pour créer le ticket.

**⚠️ Priorisation**

| **Urgence** | Gravité estimée (souvent définie par des règles automatiques) |
|----|----|
| **Impact** | Étendue de la perturbation (utilisateur seul, service complet, etc.) |
| **Priorité** | Calculée automatiquement à partir de l’Urgence et de l’Impact |

**📍 Informations supplémentaires**

| **Lieu** | Localisation de l’objet concerné |
|----|----|
| **Contrat** | Contrat associé (champ souvent masqué) |
| **Durée totale** | Temps total passé par les techniciens (renseignement manuel ou via le suivi d’activité) |
| **Demande de validation** | Ticket nécessite-t-il une validation avant traitement ? (manager, référent, etc.) |

**🧑‍🤝‍🧑 Acteurs**

| **Demandeur**    | Personne concernée par le problème       |
|------------------|------------------------------------------|
| **Observateurs** | Suivront le ticket sans y intervenir     |
| **Attribué à**   | Technicien ou groupe en charge du ticket |
