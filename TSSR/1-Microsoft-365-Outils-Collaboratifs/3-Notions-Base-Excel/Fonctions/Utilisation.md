# Utilisation🔹 **📌 Trouver et insérer une fonction :**

✔ Sélectionner la cellule pour le résultat

✔ Taper = puis les premières lettres → **Autocomplétion des fonctions**

✔ Double-cliquer sur la fonction ou appuyer sur TAB

✔ Utiliser le bouton Fx pour une aide détaillée



🔹 **📌 Recopier une formule :**

✔ **Croix noire** → Recopie automatique

✔ **Clic droit > Options de collage > Formule (Fx)** → Copie sans mise en forme

✔ **Modification automatique des références de cellules selon la direction de copie**



🔹 **📌 Références de cellules :**

✔ **Référence relative (A1)** → Évolue lors de la copie

✔ **Référence absolue ($A$1)** → Ne change pas

✔ **Mixte ($A1 ou A$1)** → Bloque soit la colonne, soit la ligne



**➡ Exemple d'évolution des formules lors de la copie :**



| **Cellule d'origine** | **Formule initiale** | **Formule copie vers le bas** | **Formule copie vers la droite** |
|----|----|----|----|
| E2 | =SOMME(A2:D2) | =SOMME(A3:D3) | =SOMME(B2:E2) |



**➡ Exemple de blocage avec $ :**



| **Formule initiale** | **Effet après recopie**                       |
|----------------------|-----------------------------------------------|
| =$A$1+B1           | $A$1 reste fixe, B1 devient B2 puis B3...   |
| =A$1+B1             | La ligne 1 reste fixe, mais A devient B, C... |
| =$A1+B1             | La colonne A reste fixe, mais la ligne change |
