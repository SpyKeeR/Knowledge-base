# Calendriers**🗂️ Où ça se passe ?**

- **Menu 1** : Accueil / Configuration / Intitulés / **Calendriers** (sous-menu Calendrier)
- **Menu 2** : Accueil / Configuration / Intitulés / **Périodes de fermeture** (sous-menu Calendrier aussi)



**📆 À quoi ça sert ?**

Les calendriers dans GLPI permettent de définir **quand le centre de service est ouvert** ou fermé, mais aussi de gérer ça pour plusieurs niveaux :

- Le centre de service lui-même (ton support IT)
- Les clients, ici appelés **entités** (ex : services internes ou départements)
- Les niveaux de service (SLA) qui peuvent avoir des horaires spécifiques

**Pourquoi c’est important ?**

Parce que ça influence comment GLPI gère les tickets, notamment pour :

- Calculer les délais SLA (ex : temps de réponse max pendant les heures d’ouverture)
- Planifier des interventions en évitant les jours fermés
- Automatiser la gestion des suivis



**🔧 Création et gestion d’un calendrier**

- **Nom** : donne un nom clair, par exemple « Centre de service – horaires standard »
- **Commentaires** : utile pour noter des précisions (ex : « horaires d’ouverture normales hors vacances »)
- **Propriétés** :
  - Tu peux modifier le nom, commentaires etc.
  - **Plages horaires** : c’est là que tu règles précisément les heures d’ouverture, jour par jour, ou par demi-journée. Par exemple, du lundi au vendredi, 9h-12h et 14h-18h, mais fermé le week-end.
  - **Périodes de fermetures** : définis ici les jours où tout est fermé, ex : vacances d’été, jours fériés, ponts…
  - 

**❄️ Gestion des périodes de fermeture**

Dans ce sous-menu dédié, tu peux créer des jours ou périodes où le centre de service (ou une entité, ou un SLA) est fermé. Ça permet d’indiquer clairement les périodes non travaillées.

Pour chaque période de fermeture, tu définis :

- **Nom** : ex « Vacances Noël 2024 »
- **Commentaires** : détail si besoin, ex « fermeture annuelle »
- **Début et fin** : dates précises
- **Récurrence** :
  - Oui, si c’est un événement annuel (comme Noël, 25 décembre chaque année)
  - Non, si c’est une fermeture ponctuelle (ex : congés exceptionnels)
