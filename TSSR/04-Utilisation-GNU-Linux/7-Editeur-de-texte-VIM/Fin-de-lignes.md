# Fin de lignes**🧠 Contexte**

Les fins de ligne diffèrent selon les systèmes d’exploitation :

- **Linux** : utilise le caractère LF (\n)
- **Windows** : utilise les caractères CR + LF (\r\n)

Cette différence peut entraîner des bugs ou des affichages bizarres lors des transferts ou éditions de fichiers entre systèmes.



**🔍 Identifier le format de fin de ligne**

Utiliser la commande file pour savoir si un fichier utilise les fins de ligne Unix ou DOS/Windows.



**🔄 Convertir les fins de lignes (outils externes)**

- dos2unix : convertit un fichier Windows en format Linux
- unix2dos : convertit un fichier Linux en format Windows



**🧰 Gérer les fins de lignes dans Vim**

- :set fileformat  
  Affiche le format de fin de ligne actuellement utilisé (unix, dos ou mac)

- :set fileformat=unix  
  Force les fins de ligne au format Linux (LF)

- :set fileformat=dos  
  Force les fins de ligne au format Windows (CRLF)

Une fois modifié, il faut enregistrer le fichier pour appliquer les changements.



**🧼 Nettoyer les fins de ligne avec sed**

- Supprimer les caractères \r (passer de Windows à Linux) > *sed 's/\r$//'*
- Ajouter des \r en fin de ligne (passer de Linux à Windows) > *sed 's/$/\r/'*
