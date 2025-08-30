# **🛠️ Format-Table – Fonctionnement général**

🔹 Affiche les **propriétés** comme en-têtes de **colonnes**, avec les **valeurs** réparties en lignes

🔹 Représentation idéale pour **comparer des objets** ou **trier visuellement**



## **📋 Utilisation basique de Format-Table**

🔹 Exemple avec un AD User :

`Get-ADUser -Filter * | Format-Table -Property Enabled, Name, SamAccountName`

➡️ Affiche les utilisateurs avec 3 colonnes personnalisées

## **🧱 Regrouper les objets par valeur commune**

🔹 Ajoute `-GroupBy` pour **organiser visuellement** par groupe

➡️ Ex : Groupement par Enabled ou City dans un annuaire

`Get-ADUser -Filter * | Format-Table -GroupBy Enabled`

➡️ Utilisateurs classés par comptes actifs/inactifs



## **🫥 Masquer les noms de colonnes**

🔹 Option `-HideTableHeaders`

➡️ Supprime l’en-tête si l’**export est prévu** ou pour un affichage minimaliste



## **🔁 Éviter les coupures de texte**

🔹 Option `-Wrap`

➡️ Force le retour à la ligne pour afficher toutes les données longues dans une cellule (ex : Description de services ou logs système)

Exemple :

`Get-Service | Format-Table -Property Description -Wrap`

`Get-EventLog -LogName System | Format-Table -Wrap`



## **🧠 À retenir**

🔸 `Format-Table` est utilisé **à la fin du pipeline**, une fois que les objets ont été filtrés, triés et sélectionnés

🔸 Le `Select-Object` n’est **pas obligatoire** avant un Format-Table, mais peut être utile

🔸 Commande **essentielle** pour **structurer une sortie claire**, avant un affichage ou un export

