# Informations LVM

## **🔎 Deux types de commandes**

| **Famille** | **Commandes** | **Détail** |
|----|----|----|
| Résumées | pvs, vgs, lvs | Résumé rapide des infos (S = "summarize") |
| Détaillées | pvdisplay, vgdisplay, lvdisplay | Infos complètes et précises |



## **⚡ Commandes Résumées**

| **Commande** | **Utilité** | **Exemple de résultat** |
|----|----|----|
| vgs | Résumé de tous les Groupes de Volumes | Noms, PVs attribués, LV présents, Taille, Attributs |
| lvs | Résumé de tous les Volumes Logiques | Noms, groupes d’appartenance, tailles |
| pvs | Résumé des Volumes Physiques | Infos de base sur chaque PV |



## **📜 Commandes Détaillées**

| **Commande** | **Utilité** | **Exemple d'infos visibles** |
|----|----|----|
| vgdisplay [nom_VG] | Détails sur un VG | Format (ex: LVM2), nb de LV/PV actifs |
| lvdisplay [nom_LV] | Détails sur un LV | Chemin complet, UID, droits d'accès, taille |
| pvdisplay [nom_PV] | Détails sur un PV | UUID, état, taille, espace libre |



**💻 Accès via le LVM Shell**

- Lancer `lvm` ➔ accès à un mini-shell dédié à toutes les commandes LVM.

