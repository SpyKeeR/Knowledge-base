# Cron

- **crontab -e** : ouvrir le planificateur de l'utilisateur en mode édition
- **crontab -l** : lister les tâches planifiées de l'utilisateur

Au 1er lancement de crontab -e, Debian propose un éditeur (Nano, VimBasic…). Tu peux changer avec select-editor.



## **📋 Structure d'une ligne crontab**

Une ligne = 6 champs + commande à exécuter :
```bash
* * * * * commande

- - - - -

| | | | |

| | | | +---- Jour de la semaine (0 - 7) (Dimanche=0 ou 7)

| | | +------ Mois (1 - 12)

| | +-------- Jour du mois (1 - 31)

| +---------- Heure (0 - 23)

+------------ Minute (0 - 59)
```

| **Champ** | **Valeurs possibles** | **Signification** |
|----|----|----|
| Minute | 0–59 | Minute d’exécution |
| Heure | 0–23 | Heure d’exécution (24h) |
| Jour du mois | 1–31 | Jour du mois |
| Mois | 1–12 | Mois (ou JAN, FEB, etc.) |
| Jour de la semaine | 0–7 | 0 ou 7 = dimanche, 1 = lundi, etc. |



## **🧩 Caractères spéciaux dans cron**

- \* → tous les cas possibles (joker)
- \, → liste (ex : 1,15 pour le 1er et le 15 du mois)
- \- → intervalle (ex : 1-5 = lundi à vendredi)
- / → fréquence (ex : */10 = toutes les 10 minutes)


