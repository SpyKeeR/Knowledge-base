# Le RAID

Le **RAID** est une **technique de partition** des données sur plusieurs disques durs, visant à : 
- ⚙️ Améliorer la **tolérance aux pannes** 
- 🔐 Renforcer la **sécurité des données** 
- 📊 Optimiser la **répartition** et la **cohérence** des informations



## 🧰 **Types de systèmes RAID**

1.  **RAID matériel** 🧩  
     • Requiert un **contrôleur RAID dédié**
     • 💸 Coût plus élevé  
     • 🚀 Performances généralement supérieures, Fiabilité et tolérance aux pannes accrues, CPU déchargé

2.  **RAID logiciel** 💻  
     • Géré **intégralement par le système d’exploitation**
     • ✅ Aucun matériel spécifique requis  
     • 🔄 Souplesse d'administration  
     • 🤝 Compatibilité entre toutes les machines équipées du même contrôleur RAID logiciel



## 📈 **Techniques de RAID principales**

- RAID 0 🚀  
   • Striping (répartition des données)  
   • 💨 Haute performance  
   • ❌ Aucune redondance (perte totale en cas de panne d’un disque)

- RAID 1 🔁  
   • Mirroring (copie identique sur plusieurs disques)  
   • 🔐 Données protégées  
   • 📉 Moins d’espace de stockage utilisable

- RAID 5 ⚖️  
   • Striping avec **parité répartie**
   • 🔄 Bon équilibre entre performance et sécurité  
   • ❗ Minimum 3 disques



## 🎯 **Conclusion**

Le choix d’un niveau de RAID dépend de :

- 🛡️ La **sécurité** attendue
- 🚀 La **performance** requise
- 💰 Le **budget** disponible  
  Une bonne compréhension des différentes options permet de sélectionner la solution la plus adaptée à l’infrastructure cible.
