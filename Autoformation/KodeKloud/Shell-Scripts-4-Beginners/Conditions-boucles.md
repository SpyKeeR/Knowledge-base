# Conditions/boucles**🧠 *if / elif / else* : Le cœur de la logique conditionnelle**

- **Structure** : if [ condition ]; then ... elif [ condition ]; then ... else ... fi
- **⚠️ Espaces obligatoires** autour des crochets : [ "$status" = "ok" ] ✅ [ "$status"="ok" ] ❌
- **Test conditionnel** basé sur une **valeur retournée** : exemple, rocket_status=$(rocket-status mission), ensuite if vérifie cette valeur.
- **Comparateurs String** : égalité → = / inégalité → != / avec [[ ... ]] → support des wildcards ([[ "$str" == *val* ]])

**🔢 *Comparateurs numériques* dans les conditions**

- -eq (égal), -ne (différent), -gt (sup), -lt (inf)
- Ex : if [ "$A" -gt 5 ] && [ "$A" -lt 10 ]
- Avec [[ ... ]] → tout en un : [[ $A -gt 5 && $A -lt 10 ]] (plus lisible, Bash only)

**📂 *Tests sur les fichiers* : savoir ce qui existe**

- -e (existe), -d (répertoire), -s (non vide), -x (exécutable), -w (écriture)
- Exemple : [ -e /etc/passwd ] && echo "Fichier OK"



**🔁 *Boucle for* : pour parcourir une liste**

- Syntaxe :  
  for mission in item1 item2 item3; do ... done

- 🧠 Remplace bien la valeur fixe par la variable mission
- Pour lire depuis un fichier :  
  for mission in $(cat missions.txt); do ... done  
  (✅ préférer $() aux backticks)

**⏳ *while loop* : répéter jusqu’à condition fausse**

- Exécution tant que condition vraie :  
  while [ "$status" = "launching" ]; do ... done

- Parfait pour **attendre un état**, un process qui se termine, etc.
- 🔁 Ajoute un sleep pour éviter de surcharger le système



**📜 *Exemple : boucle d'attente d'état de fusée***

1.  Lancement des commandes
2.  Boucle while [ "$status" = "launching" ]
3.  Vérifie rocket-status, refait un test après 2s
4.  Quand ce n’est plus "launching", sort de la boucle
5.  Si échec → rocket-debug

**📋 *Menus infinis avec while + if* : interaction utilisateur**

- while true; do ... done → boucle infinie
- read -p pour demander un choix
- Test avec if/elif/else selon le choix (-eq pour comparaison numérique)



**🧩 *Menus plus propres avec case/esac***

- Alternative à if/elif/else pour menus ou multiples cas
- Syntaxe :  
  case $var in val1) cmd ;; val2) cmd ;; *) default ;; esac

- * = valeur par défaut



**🚀 *Menu complet infini (combo while + case)***

- while true + read + case
- Exécute shutdown, reboot ou break selon le choix
- continue relance le menu si input invalide
