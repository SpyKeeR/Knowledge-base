# 📌 **Principe : Utilisation des puissances de 16**

🔹 Chaque chiffre dans un nombre hexadécimal est multiplié par **16** élevé à la puissance de sa position, en commençant de la droite (position 0).

🔹 Additionnez les produits pour obtenir le nombre en base 10.


## **🏗 Exemple : Conversion du Nombre Hexadécimal 3F en Décimal**

1.  **Identifiez les positions des chiffres hexadécimaux :**

    - **Le chiffre F (qui vaut 15 en décimal) est à la position 0.**
    - **Le chiffre 3 est à la position 1.**

2.  **Multipliez chaque chiffre par 16 élevé à la puissance de sa position :**

    - **(3 × 16^1) + (15 × 16^0)**

3.  **Simplification des calculs :**

    - **(3 × 16) + (15 × 1) = 48 + 15 = 63**

**📌 Résultat :**

🔹 **3F₁₆ = 63₁₀**



## **📝 Méthode alternative via Binaire :**

Pour la conversion **Hexadécimal → Décimal** en **2 étapes** :

1.  **Convertir le nombre hexadécimal en binaire** : Chaque chiffre hexadécimal correspond à 4 bits binaires.
2.  **Convertir ensuite le nombre binaire en décimal** : Utilisez les puissances de 2 pour chaque bit, de droite à gauche.

🔹 **Exemple : Conversion de 3F₁₆ → Binaire → Décimal**

- **3** en hexadécimal = **0011** en binaire
- **F** en hexadécimal = **1111** en binaire
- **3F₁₆ = 00111111₂**

Ensuite, on convertit **00111111₂** en décimal :

- (0 × 2^5) + (0 × 2^4) + (1 × 2^3) + (1 × 2^2) + (1 × 2^1) + (1 × 2^0)
- 0 + 0 + 8 + 4 + 2 + 1 = 63

🔹 **Cela donne le même résultat** :

- **3F₁₆ = 63₁₀**
