# Types de RAID📈 **RAID 0 – Striping**

- Répartition des **blocs de données** sur plusieurs disques
- ✅ Très bonnes performances
- ❌ **Aucune redondance** : perte totale si un seul disque tombe en panne



🪞 **RAID 1 – Mirroring**

- **Duplication** des données sur deux disques
- ✅ Haute **tolérance aux pannes**
- ✅ Accès garanti même si un disque échoue
- ❌ Capacité de stockage réduite (50 % utilisée)



🧮 **RAID 5 – Striping avec parité**

- Répartition des **données + parité** sur **3 disques ou plus**
- ✅ Données **reconstituables** en cas de panne d’un seul disque
- ✅ Bon compromis entre **capacité, performance et sécurité**
- ❌ Moins performant en écriture que RAID 0 ou RAID 10

🔁 Exemple de répartition RAID 5 :

RAID5 = [Disque1, Disque2, Parité]

RAID5 = [Disque1, Parité, Disque3]

RAID5 = [Parité, Disque2, Disque3]



💡 **RAID 10 – Striping + Mirroring**

- Combine RAID 0 + RAID 1
- ✅ Performances élevées **et** forte **redondance**
- ✅ Données **répliquées et réparties**
- ❌ Besoin d’un **nombre pair** de disques, capacité divisée par 2
