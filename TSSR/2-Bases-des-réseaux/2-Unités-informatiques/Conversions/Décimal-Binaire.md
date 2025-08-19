# Décimal > Binaire📌 **Principe : Méthode des divisions successives**

🔹 Diviser le nombre par **2** et noter le **reste** (0 ou 1).

🔹 Répéter jusqu’à obtenir un quotient **0**.

🔹 **Lire les restes de bas en haut** pour obtenir le résultat binaire.



**🏗 Exemple : Conversion de 25 en binaire**

| **Division par 2** | **Quotient** | **Reste** |
|--------------------|--------------|-----------|
| 25 ÷ 2             | **12**       | **1**     |
| 12 ÷ 2             | **6**        | **0**     |
| 6 ÷ 2              | **3**        | **0**     |
| 3 ÷ 2              | **1**        | **1**     |
| 1 ÷ 2              | **0**        | **1**     |

📌 **Lecture des restes de bas en haut : 11001₂ =** ✔ **25 (décimal) = 11001 (binaire)**



**📝 Méthode alternative : Tableau Binaire par Soustraction Itérative**

🔹 Identifier les puissances de **2** inférieures ou égales au nombre à convertir.

🔹 Soustraire successivement ces puissances et noter **1** ou **0** selon leur présence.

📊 **Exemple : Conversion de 25 en binaire avec le tableau**

| **Puissance de 2** | **16**   | **8**    | **4**    | **2**    | **1**    |
|--------------------|----------|----------|----------|----------|----------|
| Valeur             | ✅ **1** | ✅ **1** | ❌ **0** | ❌ **0** | ✅ **1** |

💡 **Calcul détaillé :**

✔ 25 - **16** = 9 (**1**)

✔ 9 - **8** = 1 (**1**)

✔ 1 - **4** = impossible (**0**)

✔ 1 - **2** = impossible (**0**)

✔ 1 - **1** = 0 (**1**)

📌 **Résultat : 11001₂**
