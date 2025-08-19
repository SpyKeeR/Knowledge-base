# Définitions🔐 **Sauvegarde (Backup)**

Créer une **copie de sécurité** des données afin de pouvoir les **restaurer** en cas de perte, suppression ou corruption.

➡️ Objectif : garantir la **continuité d’activité** et la **protection des données** en cas d’incident.

✅ Doit être effectuée **régulièrement** et **stockée sur un média différent** de la source.



📥 **Enregistrement (Record)**

Capturer et stocker une **information dans un format durable** pour pouvoir la **réutiliser ou la consulter plus tard**.

🔎 Concerne tout type de données : audio, vidéo, scientifique, médical, etc. 🎯 But : conserver une **trace fidèle** d’un événement ou d’un contenu.



📦 **Archivage de données (Data Archiving)**

Stockage **à long terme** de données **peu ou pas utilisées**, mais à conserver pour des raisons : ⚖️ Légales 🧾 Historiques ✅ Conformité réglementaire  
🆚 Contrairement à la sauvegarde, l’archivage **n’est pas prévu pour une récupération rapide**, mais pour une **conservation sécurisée dans le temps**.



🔁 **Réplication**

Fonctionnalité utilisée dans les environnements **virtualisés** (ex : Veeam), qui permet de :

- Créer une **copie en temps réel** d'une machine virtuelle (VM)
- Garder cette copie **synchronisée avec la VM d’origine  **
  🎯 Objectifs :

  - RTO (Recovery Time Objective) : Temps acceptable pour restaurer un service
  - RPO (Recovery Point Objective) : Durée de perte de données tolérable

⚠️ La réplication assure une **reprise rapide d’activité** en cas d’incident (failover).



📤 **Duplication (Duplication)**

Création d’une **copie identique et figée** de données, d’un fichier ou d’une machine. 🆚 Contrairement à la réplication, la duplication **ne maintient pas les données à jour**.

Utilité : Redondance, Distribution, Sauvegarde ponctuelle



🗃️ **Pérennisation des données (Data Preservation)**

Ensemble de **pratiques et stratégies** pour :

- Conserver les données **lisibles**
- Assurer leur **intégrité** et leur **accessibilité** malgré :  
  • L’obsolescence des formats  
  • Le vieillissement des supports  
  • L’évolution des systèmes

🎯 Vise à garantir une **conservation fiable et durable** dans le temps (archives, patrimoine numérique, etc.).
