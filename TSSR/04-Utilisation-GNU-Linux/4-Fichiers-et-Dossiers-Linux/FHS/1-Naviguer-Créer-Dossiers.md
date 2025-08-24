# Naviguer/Créer Dossiers

## **🔄 Se déplacer avec cd**

|     Commande                                |     Effet                                                           |
|---------------------------------------------|---------------------------------------------------------------------|
|     cd / cd ~ / cd   $HOME     ~username    |     Va au dossier   perso     Appel le dossier   perso d'un user    |
|     cd dossier                              |     Chemin relatif                                                  |
|     cd /chemin/complet                      |     Chemin absolu                                                   |
|     cd ..                                   |     Remonte d’un   niveau                                           |
|     cd -                                    |     Revient au dossier   précédent                                  |



## **🛠 Créer un dossier avec mkdir**

| **Commande**    | **Effet**                          |
|-----------------|------------------------------------|
| mkdir nom       | Crée un dossier simple             |
| mkdir -v nom    | Idem avec message                  |
| mkdir -p -v A/B | Crée plusieurs niveaux de dossiers |



## **🧹 Supprimer un dossier vide avec rmdir**

| **Commande**    | **Effet**                          |
|-----------------|------------------------------------|
| rmdir -v nom    | Supprime un dossier vide           |
| rmdir -p -v A/B | Supprime B puis A s’ils sont vides |

⚠️ rmdir ne supprime **que des dossiers vides**.

