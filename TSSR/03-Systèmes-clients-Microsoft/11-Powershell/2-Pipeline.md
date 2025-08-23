# Pipeline

💡 **Définition**

Le **pipeline (|)** permet d’envoyer la sortie standard **d’une commande** vers l’entrée **d’une autre commande**.

👉 **Chaque commande reçoit le résultat de la précédente** et peut le filtrer, le modifier ou l’utiliser.



## **🛠️ Utilisation du pipeline**

### 🔹 **Exemple de base** :
- `Get-NetAdapter | Select-Object Name, Status, LinkSpeed`

➡️ Get-NetAdapter retourne la liste des cartes réseau.

➡️ Le **pipeline** (|) envoie ce résultat à Select-Object.

➡️ Select-Object Name, Status, LinkSpeed affiche **uniquement** ces propriétés.



### 🔹 **Enchaîner plusieurs commandes** :
- `Get-Process | Sort-Object CPU -Descending | Select-Object -First 5`

➡️ Liste les processus

➡️ Trie par **utilisation CPU** (du plus gourmand au moins gourmand)

➡️ Affiche **les 5 premiers**



### 🔹 **Filtrer avant d’afficher** :
- `Get-Service | Where-Object Status -eq "Running"`

➡️ Affiche **seulement les services en cours d’exécution**



### 🔥 **Exemple combiné :**

- `Get-Process | Where-Object CPU -gt 10 | Sort-Object CPU -Descending | Format-Table -AutoSize`

➡️ Affiche **les processus consommant plus de 10% CPU**, triés **du plus gourmand au moins gourmand** en **tableau ajusté**.

## **⚡ Pipeline : Les commandes clés**

| **Commande**   | **Fonction**                           |
|----------------|----------------------------------------|
| Select-Object  | Choisir des **propriétés spécifiques** |
| Where-Object   | Filtrer les résultats                  |
| Sort-Object    | Trier les résultats                    |
| Measure-Object | Compter les objets                     |
| Group-Object   | Regrouper les objets similaires        |
| Format-Table   | Affichage tabulaire                    |
| Format-List    | Affichage détaillé                     |

