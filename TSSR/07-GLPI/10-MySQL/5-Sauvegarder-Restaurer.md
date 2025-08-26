# Sauvegarder / Restaurer

mysqldump est un outil puissant pour **sauvegarder une base de données** MySQL, en générant un fichier .sql que tu peux rejouer plus tard pour **restaurer** la base.



## **🛡️ Sauvegarde classique**

Commande de base : 
```bash
mysqldump -u utilisateur -p nom_base > sauvegarde.sql`
```

La sortie (stdout) est redirigée vers un fichier .sql.



## **🎯 Définir le périmètre de la sauvegarde**

**✅ Une seule base** 
```bash
mysqldump -u root -p ma_base > ma_base.sql
```



**🗃️ Plusieurs bases** 
```bash
mysqldump -u root -p --databases base1 base2 base3 > bases.sql
```
⚠️ Avec --databases, le fichier .sql contient aussi les instructions `CREATE DATABASE` et `USE`.



## **🌍 Toutes les bases**
```bash
mysqldump -u root -p --all-databases > full_backup.sql
```


### **🧼 Ajouter l’option --add-drop-database** 
```bash
mysqldump -u root -p --databases ma_base --add-drop-database > ma_base.sql
```
Permet d’ajouter une commande DROP DATABASE IF EXISTS avant chaque CREATE DATABASE. Très utile pour **écraser proprement** une base lors de la restauration.



## **🧰 Options importantes**

### **🔧 --opt *(activée par défaut)***

C’est un raccourci pour plusieurs options d’optimisation : `--add-drop-table`, `--add-locks`, `--create-options`, `--disable-keys`, `--extended-insert`, `--lock-tables`, `--quick`, `--set-charset`

Pas besoin de l'ajouter sauf si tu modifies certaines de ses options.

### **🧩 -c ou --complete-insert** 
Inclut les noms de colonnes dans les INSERT, ce qui rend le script **plus lisible** et **plus robuste** en cas de changement de structure.

### **⚡ -e ou --extended-insert** 
Permet d’insérer plusieurs lignes d’un coup avec une seule requête INSERT, ce qui **accélère** la restauration. Actif par défaut via `--opt`



## **🧱 Exclure des tables spécifiques**

Si tu veux **exclure certaines tables**, utilise l’option `--ignore-table`.

Exemples :

- Exclure une seule table : `--ignore-table=ma_base.nom_table`
- Exclure plusieurs tables (tu dois répéter l’option à chaque fois) : `--ignore-table=ma_base.table1 --ignore-table=ma_base.table2`

⚠️ Tu dois **préciser le nom de la base** pour chaque table exclue.



## **🔁 Restauration d’une base**

```bash
mysql -u root -p nom_base < sauvegarde.sql
```

⚠️ Si le fichier .sql contient un `CREATE DATABASE`, la base n’a pas besoin d’exister avant. 

Sinon, pense à la créer manuellement avant de restaurer : `CREATE DATABASE nom_base;`

