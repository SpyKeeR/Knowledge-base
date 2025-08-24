# Compression avec tar

## **🧠 Archiver ≠ Compresser**

- **Archiver** = regrouper plusieurs fichiers dans **un seul** (ex : .tar, .iso)
- **Compresser** = réduire la **taille** d’un ou plusieurs fichiers (gzip, bzip2, xz)
- 💡 Un .zip fait les **deux**. Un .tar **archive** seulement, sauf si on ajoute compression.



## **🛠️ Créer une archive .tar**
```bash
tar cf archive.tar fichier1 fichier2
```
- c → create (créer)
- f → filename (nom du fichier archive)
- ⚠️ f doit **toujours** être suivi du nom du fichier



## **📂 Extraire une archive**
```bash
tar xf archive.tar
```
- x → extract
- -C → Destination d'extraction



## **🔍 Lister le contenu**
```bash
tar tf archive.tar # Vue simple  
tar tvf archive.tar # Vue détaillée
```
- t → table of contents
- v → verbose (affichage détaillé)



## **➕ Ajouter à une archive existante**
```bash
tar uvf archive.tar chemin/fichier
```
- u → update (ajoute uniquement si fichier plus récent) / (Chemin relatif, du a un problème historique de / et tar)



## **💨 Compression avec tar**

| **Format** | **Commande**                       | **Option** |
|------------|------------------------------------|------------|
| gzip       | *tar czf archive.tar.gz fichiers*  | z          |
| bzip2      | *tar cjf archive.tar.bz2 fichiers* | j          |
| xz         | *tar cJf archive.tar.xz fichiers*  | J          |

