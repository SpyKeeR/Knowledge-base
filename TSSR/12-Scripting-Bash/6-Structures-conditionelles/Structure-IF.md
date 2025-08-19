# Structure IFL’instruction if permet d’**exécuter des commandes seulement si une condition est remplie**. Elle rend ton script **dynamique** et **réactif au contexte**.



**⚙️ Composants obligatoires**

Une structure if classique contient au minimum :

- if ➜ début de la condition et une **commande/test** qui renvoie 0 (succès) ou non
- then ➜ actions à faire si vrai et fi ➜ fin de la structure



**🧩 Structure complète (formes)**

**🆕 Moderne (compacte et très utilisée)** if condition ; then action ; else autre_action ; fi

**🧾 Classique (plus lisible en multi-lignes)**

if condition  
then  
action  
else  
autre_action  
fi



**📌 Utilisation simple**

Quelques cas concrets : if [[ "$LOGNAME" = root ]] ; then echo "Interdit" ; fi ---- if ls /tmp ; then echo "/tmp existe" ; fi

👉 Le test peut être une **commande système** (ls, grep, etc.) ou un test booléen avec [[ ... ]].



**🔀 Avec else : choix entre deux actions**

Permet de **gérer l’alternative** si la condition échoue : ➡️ Tu peux chaîner plusieurs **actions** dans chaque bloc.

if [[ "$LOGNAME" = root ]] ; then  
echo "Ne pas se connecter en root"  
else  
echo "Bienvenue sur la machine $HOSTNAME"  
fi



**🧮 Plusieurs actions dans le même bloc**

C’est tout simple : tu les empiles dans l’ordre. 📍 Astuce : si tu veux **arrêter le script**, pense à exit.

if [[ "$LOGNAME" = root ]] ; then  
echo "Interdit"  
exit 1  
else  
echo "OK"  
echo "Script continue"  
fi

**🔀 Structure elif**

➡️ **Lisibilité + efficacité**, c’est la méthode recommandée !

if test1 ; then  
action1  
elif test2 ; then  
action2  
else  
action3  
fi
