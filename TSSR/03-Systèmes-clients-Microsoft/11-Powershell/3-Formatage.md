# Formatage

## **📌 Les principales commandes de formatage**

| **Commande** | **Affichage** | **Usage** |
|----|----|----|
| Format-List (fl) | 📜 **Liste verticale** | Pour voir **toutes** les propriétés d'un objet |
| Format-Table (ft) | 📊 **Tableau structuré** | Organiser les données en colonnes |
| Format-Wide (fw) | 📏 **Affichage large** | Afficher une seule propriété sur plusieurs colonnes |

## **🛠️ Exemples d'utilisation**



### 🔹 **Affichage en liste** *(chaque propriété sur une ligne)*

- `Get-Service | Format-List Name, Status`

📌 **Avec toutes les propriétés**

- `Get-Service | Format-List`

➡️ Équivalent à Select-Object *



### 🔹 **Affichage en tableau** *(colonnes ajustées automatiquement)*

- `Get-Service | Format-Table Name, Status -AutoSize`

➡️ -AutoSize ajuste la largeur des colonnes dynamiquement

📌 **Raccourci plus rapide** :

- `Get-Service | FT Name, Status -AutoSize`



### 🔹 **Affichage large** *(sur plusieurs colonnes, idéal pour une seule propriété)*

- `Get-Process | Format-Wide ProcessName -Column 3`

➡️ Affiche les noms de processus sur **3 colonnes**

📌 **Mise en page automatique**

- `Get-Process | Format-Wide ProcessName -AutoSize`



## ⚠️ *Attention : Format- doit être utilisé en dernier !**

❌ **Une fois un objet formaté, il ne peut plus être manipulé avec un autre pipeline !**

Exemple à ne pas faire :

- `Get-Service | Format-Table Name, Status | Sort-Object Name` # ❌ Ne fonctionnera pas

✅ **Correct** :

- `Get-Service | Sort-Object Name | Format-Table Name, Status`

