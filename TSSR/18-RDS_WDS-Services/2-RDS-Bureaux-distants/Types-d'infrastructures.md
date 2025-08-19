# Types d'infrastructures💻 **Modèle Client Lourd**

- Applications installées **directement sur les postes utilisateurs**
- Les **performances** dépendent de la **puissance de chaque machine**
- Infrastructure **décentralisée** : chaque poste doit être **configuré, mis à jour et maintenu individuellement**
- Gestion des applications complexe pour les administrateurs

🔧 *Ressources et traitements* : localisés sur chaque poste

🧩 *Gestion des applications* : individuelle

🏗️ *Type d'infrastructure* : décentralisée



🪶 **Modèle Client Léger**

- Les utilisateurs travaillent depuis des **terminaux simples**, sans disque dur
- Les **applications** sont installées et exécutées **sur les serveurs**
- La **puissance de calcul** est assurée **côté serveur**, pas sur le poste client
- Maintenance **centralisée** des applications

⚙️ *Ressources et traitements* : centralisés sur les serveurs

🛠️ *Gestion des applications* : centralisée

🏢 *Type d'infrastructure* : centralisée



🌐 **Support de transmission**

- Les **communications** entre clients légers et serveurs passent par le **réseau**
- Le protocole de transmission dépend de la **solution RDS choisie** (ex : RDP, ICA, PCoIP…)
- Les actions utilisateur (clavier, souris) sont transmises au serveur
- Le **serveur traite**, puis **renvoie l’affichage** au terminal client
- 

🔁 *Interaction client-serveur* : 🖱️ Actions → 🛰️ Réseau → 🖥️ Serveur → 📡 Réseau → 📺 Affichage
