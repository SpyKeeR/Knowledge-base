# 📌 **Principe : Utilisation des puissances de 8**

🔹 Chaque chiffre d’un nombre **octal** (base 8) est multiplié par **8** élevé à la puissance correspondant à sa position.

🔹 On commence par la **droite** (position 0) et on applique les puissances successivement :

🔹 **8^0** pour le premier chiffre à droite, **8^1** pour le suivant, etc.


## **🏗 Exemple : Conversion du Nombre Octal 234 en Décimal**

1.  **Identifiez les positions des chiffres dans le nombre octal :**

    - **Le chiffre tout à droite (4) est à la position 0**
    - **Le chiffre suivant (3) est à la position 1**
    - **Le chiffre le plus à gauche (2) est à la position 2**

2.  **Appliquez les puissances de 8 :**

    - **(2×8^2)+(3×8^1)+(4×8^0)**

Simplification des calculs :

- (2×64)+(3×8)+(4×1)
- 128+24+4=156

📌 Résultat :

🔹 **234₈ = 156₁₀** 🎯


## **📝 Résumé de la méthode :**

1.  **Identifiez les positions des chiffres** (de droite à gauche).
2.  **Multipliez chaque chiffre** par 8^position
3.  **Additionnez les produits** obtenus pour obtenir le nombre en base 10.
