# Décimale > Héxadécimale📌 **Principe : Utilisation des divisions successives par 16**

🔹 Divisez le nombre décimal par **16** et notez le reste.

🔹 Si le reste est compris entre **10 et 15**, remplacez-le par les lettres hexadécimales correspondantes (A à F).

🔹 Répétez cette opération avec le quotient obtenu jusqu’à ce qu'il soit égal à 0.

🔹 Le nombre hexadécimal final est obtenu en lisant les restes de **bas en haut**.



**🏗 Exemple : Conversion du Nombre Décimal 254 en Hexadécimal**

1.  **Divisez le nombre décimal par 16 :**

    - **254÷16=15 (quotient), reste 14 (14 correspond à la lettre E).**

2.  **Divisez le quotient par 16 :**

    - **15÷16=0 (quotient), reste 15 (15 correspond à la lettre F).**

3.  **Lire les restes de bas en haut :**

    - **Reste : 14→E**
    - **Reste : 15→F**

**📌 Résultat :**

🔹 **254₁₀ = FE₁₆**



**📝 Méthode alternative via Binaire :**

Pour la conversion **Décimal → Hexadécimal** en **2 étapes** :

1.  **Convertir le nombre décimal en binaire**.
2.  **Regrouper les bits binaires en blocs de 4**, de droite à gauche.
3.  **Convertir chaque groupe de 4 bits en son équivalent hexadécimal**.
