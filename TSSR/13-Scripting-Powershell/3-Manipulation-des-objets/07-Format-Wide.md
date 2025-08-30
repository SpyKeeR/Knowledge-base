# **📦 Objectif de Format-Wide**

Afficher **une seule propriété** d’un objet PowerShell sous **forme de colonnes**, pour une présentation compacte et visuelle.



## **🛠️ Différences avec Format-Table**

- `Format-Table` ➜ Affiche **plusieurs propriétés** en tableau.
- `Format-Wide` ➜ Affiche **une seule propriété**, plus lisible quand on a beaucoup d’objets.



## **📋 Utilisation de base**

- S’utilise avec un pipe | après une commande type Get-XXX.
- Par défaut, affiche la propriété **Name**, sauf si -Property est précisé.

### **Exemple** :

`Get-Service | Format-Wide -Column 10` ➜ Affiche les noms des services en 10 colonnes.



## **🧠 Paramètres essentiels**

- `-Column [n]` ➜ Définit manuellement le **nombre de colonnes** (ex: 3, 5, 10).
- `-AutoSize` ➜ Calcule **automatiquement** le meilleur nombre de colonnes selon la taille de la console.
- `-Property [nom]` ➜ Définit la propriété à afficher (ex : GivenName, Name...).

### **Exemples** :

- `Get-AdUser -Filter * | Format-Wide -AutoSize` ➜ Affiche les noms d'utilisateurs avec un nombre auto de colonnes.
- `Get-AdUser -Filter * | Format-Wide -Property GivenName -Column 5` ➜ Affiche 5 colonnes avec les prénoms.



## **⚠️ À retenir**

- Ne gère **qu’une propriété à la fois**.
- Trop de colonnes = **données tronquées** si largeur insuffisante.
- Très utile pour une **lecture rapide**, mais peu adapté à l’analyse poussée.

