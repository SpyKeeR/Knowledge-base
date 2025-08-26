# Statut de ticket

### 🟢 **Les statuts d’un ticket dans GLPI**



GLPI utilise **un cycle de vie automatisé** des tickets pour refléter leur état d’avancement.

La plupart du temps, **le statut est géré automatiquement**, mais **il peut aussi être modifié manuellement** selon le contexte (notamment en cas d’escalade).



### **🔁 Les différents statuts et leur signification :**

|     Statut                     |     Quand est-il appliqué ?                                                                                                                                                                         |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     🆕   Nouveau                |     Création du ticket. Aucun traitement encore.                                                                                                                                                  |
|     🔄   En cours (attribué)    |     Le ticket a été  attribué à un utilisateur ou groupe.   🔧 Appliqué automatiquement ou manuellement (ex :   escalade).                                                                       |
|     🗓️   En cours (planifié)    |     Une tâche ou un rendez-vous a été planifié dans le ticket.   📆 Ex : intervention prévue demain.                                                                                            |
|     ⏳   En attente             |     Le ticket est en pause : attente d’info, de validation, ou d’un événement (planification...).                                                                                                 |
|     ✅   Résolu                 |     Une solution a été proposée. Le ticket est traité, mais pas encore clôturé.                                                                                                                   |
|     🔒   Clos                   |     Le ticket est terminé et validé par l’utilisateur (validation manuelle) ou par un responsable (clôture manuelle) ou automatiquement (si délai défini dans l’entité)  |

#### **🧠 À noter :**

- Tu ne choisis **pas directement** le statut « Résolu » ou « Clos ».  
  C’est **l’ajout d’une solution ou la validation** qui déclenche ce changement.

- Les statuts **« En cours (attribué) »** et **« En cours (planifié) »** permettent de **mieux segmenter les tickets actifs**.
- Le statut **« En attente »** peut servir à **geler le ticket temporairement** (ex : en attente de matériel ou d’une réponse du demandeur).

