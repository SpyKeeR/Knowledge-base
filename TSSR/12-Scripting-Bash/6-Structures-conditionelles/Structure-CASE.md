# Structure CASE📌 **🧠 Objectif**

La structure case permet de comparer une variable à plusieurs **valeurs possibles** (chaînes, chiffres, motifs) pour déclencher **des actions spécifiques selon le cas**.



🔎 **🧩 Syntaxe de base**

- case $var in → ouverture du bloc
- valeur1) → test d’un motif/valeur
- action ;; → instructions exécutées si correspondance
- valeur2) etc. → autre cas
- *) → valeur par défaut (fallback, « sinon »)
- esac → fin du bloc case



🛠️ **💬 Exemples types d’utilisation**

- p) → mot-clé exact : ici, si $var = p
- [0-9]) → motif : n'importe quel chiffre
- a|b) → OU logique entre plusieurs valeurs
- *) → n'importe quelle autre valeur (joker)

💡 Seul **le premier cas correspondant** est exécuté (pas de "cascade" comme dans certains langages).



📐 **🔧 Utilisation typique**

- 🔁 Menus en ligne de commande (choix utilisateur)
- 🧪 Traitement de tests multiples (lettres, chiffres, motifs)
- 🎭 Filtres avec métacaractères : [a-z], *, ?, etc.



🚧 **⚠️ À retenir**

- ;; est **obligatoire** après chaque bloc d’action pour clôturer le cas
- Le test est fait **dans l’ordre** : le premier qui correspond s’exécute
- Par défaut, terminer par *) pour couvrir les cas non gérés
