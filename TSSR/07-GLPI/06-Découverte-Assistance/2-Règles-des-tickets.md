# Règles des tickets

🔍 **Chemin d’accès** : **Accueil > Administration > Règles > Règles métier pour les tickets**

## **🛠️ Création d’une règle**

### **➕ Ajouter une règle**

- **Nom** : Donne un nom clair (ex : « Affectation auto si catégorie = Réseau »)
- **Opérateur logique** : ET → Tous les critères doivent être vrais / OU → Un seul critère suffit
- **Actif** : OUI = règle appliquée / NON = règle ignorée
- **Règle utilisée pour** : Ajouter = à la création du ticket / Mettre à jour = quand on modifie le ticket (tech ou escalade auto) / Ajouter / Mettre à jour = tout le temps
- **Description / Commentaire** : pour documenter à quoi elle sert (optionnel mais utile si tu en as beaucoup)



### **🎯 Critères**

Ce sont **les conditions** pour que la règle s’applique.

Exemples : 
- **Catégorie** est « Réseau »
- **Demandeur** commence par « Jean »
- **Objet impacté** contient « imprimante »
- **Urgence** est « Haute »

👉 Tu peux combiner plusieurs critères avec les opérateurs logiques.

**Liste des opérateurs possibles** :

- est / n’est pas
- contient / ne contient pas
- commence par / finit par
- expression régulière (puissant !)
- existe / n’existe pas
- sous / pas sous (ex : sous-catégorie)



### **🧾 Actions**

Ce sont **les modifications automatiques** à faire si les critères sont remplis.

Exemples d’actions :

- Attribuer le ticket à un groupe/technicien
- Changer la priorité
- Appliquer un SLA
- Modifier l'entité
- Remplir un champ personnalisé

💡 **Cas courant** : Si catégorie = "Téléphonie", alors attribuer au groupe "Support Télécom"

- Appliquer SLA "Standard"
- Passer le statut à "En cours (attribué)"



## **🕓 Historique**

GLPI garde une trace de l’application des règles (super pour déboguer si un ticket ne réagit pas comme prévu).

