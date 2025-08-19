# Boucle FOR**🔁 Boucle for : principe général**

La boucle for permet d’itérer :

➡️ sur **une liste de valeurs** à traiter **ou**

➡️ un **nombre fixe** de fois (via une séquence ou une commande seq par ex.)

**🛠️ Syntaxe de base**

for variable in liste_de_valeurs ; do  
commandes  
done

👆 À chaque tour :

- variable prend une **valeur de la liste**
- la **liste** est lue **de gauche à droite**
- le **bloc do ... done** s’exécute à chaque fois

**🧩 Exécution et comportement**

- La **1ʳᵉ valeur** est stockée dans $variable lors du **1er passage**
- Puis, chaque valeur suivante est affectée aux **itérations suivantes**
- La **liste** peut être construite avec : "val1" "val2" ou $(commande) ou encore {1..10}

**⚙️ IFS : Internal Field Separator**

IFS = variable spéciale qui détermine **les séparateurs** dans les listes (par défaut : espace, tab, retour à la ligne)

🧪 Tu peux le modifier temporairement pour changer la manière dont la liste est découpée



**📌 Exemple simple**

for valeurs in "petit exemple" "dede" "boucle for" ; do  
echo "contenu de la variable valeurs : $valeurs"  
done

🔎 Résultat → 3 itérations, une par bloc entre guillemets (donc espaces internes respectés)



**🧠 À retenir (essentiel)**

- La **liste** est **interprétée par le shell** selon IFS
- ⚠️ **Ne pas oublier les guillemets** si les valeurs contiennent des espaces
- **for ≠ while** : ici, tout est **déjà connu d’avance**, pas de lecture interactive ligne à ligne
