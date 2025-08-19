# Fonctions🔹 **📌 Définition : Formule prédéfinie** qui effectue une tâche avec des arguments → Retourne un résultat



🔹 **📌 Fonctions de base :**

✔ =SOMME(A1:A10) → Additionner une plage

✔ =MOYENNE(A1:A10) → Moyenne

✔ =MIN(A1:A10) / =MAX(A1:A10) → Valeur min/max

🔹 **📌 Fonctions logiques :**

✔ =SI(A1>10, "OK", "Non") → Condition simple

✔ =ET(A1>5, B1<15) → Toutes conditions vraies

✔ =OU(A1=10, B1=20) → Au moins une condition vraie

🔹 **📌 Fonctions de texte :**

✔ =GAUCHE(A1, 5) → Premiers caractères

✔ =DROITE(B1, 3) → Derniers caractères

✔ =NBCAR(C1) → Nombre de caractères

🔹 **📌 Fonctions de recherche :**

✔ =RECHERCHEV(10, A1:B10, 2, FAUX) → Cherche une valeur verticalement

✔ =RECHERCHEH(5, A1:J1, 3, FAUX) → Cherche horizontalement

🔹 **📌 Fonctions date & heure :**

✔ =AUJOURDHUI() → Date actuelle

✔ =MAINTENANT() → Date & heure

✔ =DATEDIF(A1, B1, "d") → Différence en jours

🔹 **📌 Fonctions imbriquées :**

✔ =SI(A1>10, SOMME(A1:A10), "Trop bas")

✔ =SI(RECHERCHEV(10, A1:B10, 2, FAUX)>100, "Grand", "Petit")
