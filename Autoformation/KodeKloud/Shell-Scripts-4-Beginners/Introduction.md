# Introduction**🧮 Variables Bash : affectation et utilisation**

- **Déclaration** : nom_variable=valeur (⚠️ pas d’espace autour de =)
- **Utilisation** : $nom_variable
- **Bonnes pratiques** : minuscules + underscores (mission_name ✅, Mission-Name ❌)
- Changer une seule ligne suffit pour réutiliser la même valeur dans tout le script



**🔄 Command Substitution : stocker le résultat d’une commande**

- Syntaxe : var=$(commande)
- Exemple : rocket_status=$(rocket-status "$mission_name")
- Affichage : echo "Status: $rocket_status"



**🧳 Arguments de script**

- $0 = nom du script, $1 = 1er argument, $2, $3, etc. pour les suivants
- Recommandé ➜ stocker $1 dans une variable (mission_name=$1) pour clarté



**💬 read : lire une entrée utilisateur**

- Simple : read var
- Avec prompt : read -p "Texte : " var
- 📌 Astuce : le -p améliore l’UX en indiquant ce qu’on attend



**➕ expr : opérations arithmétiques simples (entiers uniquement)**

- Addition : expr 6 + 3
- Soustraction : expr 6 - 3
- Multiplication : expr 6 \ 3 (⚠️ le * doit être **échappé**)
- Division : expr 6 / 3
- Variables : expr $A + $B, expr $A \ $B, etc.
- ⚠️ espaces **obligatoires** entre opérateurs et opérandes



**🔢 Double parenthèses (( )) : alternative moderne à expr**

- Plus simple, sans $ et sans escape du *
- Exemple : ((A + B)), ((A * B)), etc.
- Compatible avec ++/-- (pré/post incrément & décrément) → (( ++A )), (( A++ ))
- 🛑 Toujours utiliser avec echo ou affectation : echo $((A + B)), sinon erreur



**🔬 bc : calculs en virgule flottante**

- Integer only ❌ avec expr / (( ))
- Float ✅ avec bc -l
- Exemple : echo "$A / $B" | bc -l → 3.333333…
- -l = charge la lib math pour une précision décimale
