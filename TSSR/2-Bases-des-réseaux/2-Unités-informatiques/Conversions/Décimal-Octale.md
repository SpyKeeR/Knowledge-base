# Décimal > Octale> 📌 **Principe : Utilisation des puissances de 8**

🔹 **Divisions successives par 8**, en notant le reste à chaque étape.

🔹 Continuer jusqu'à obtenir un quotient de **0**.

🔹 Le nombre octal est obtenu en **lisant les restes de bas en haut**.



**🏗 Exemple : Conversion de 156 en octal**

1.  **156 ÷ 8 = 19**, reste 4
2.  **19 ÷ 8 = 2**, reste 3
3.  **2 ÷ 8 = 0**, reste 2

📌 **Lecture des restes de bas en haut :**

🔹 **234** en base **8**

✔ **156₁₀ = 234₈** 🎯



**🔄 Méthode Alternative : Conversion en deux temps (Décimal → Binaire → Octale)**

Une autre méthode pour convertir un nombre décimal en octale est de passer par la conversion en **binaire**, puis de **regrouper les bits en paquets de 3** pour obtenir les valeurs octales correspondantes.

**1. Décimal → Binaire**

Prenons l'exemple de 156 et convertissons-le d'abord en binaire :

🔹 **156₁₀ = 10011100₂**

**2. Binaire → Octale**

Ensuite, on regroupe les bits par **groupes de 3** (en partant de la droite) :

🔹 **10011100₂ → 100 111 00**

On complète éventuellement avec des zéros à gauche pour avoir des groupes de 3 bits :

🔹 **100 111 000**

Maintenant, on remplace chaque groupe par sa valeur octale :

🔹 **100 = 4**, **111 = 7**, **000 = 0**

📌 **Résultat en octal :**

🔹 **156₁₀ = 470₈** 🎯
