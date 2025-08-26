# 5 - Les pratiques

Une **pratique**, c’est un **ensemble de ressources, de compétences, d’outils et de méthodes** qu’on utilise pour **faire tourner et améliorer les services IT**. 🔍 En gros : c’est **"comment on s’y prend"** pour gérer les services IT au quotidien, en suivant des méthodes reconnues.

## **📦 Répartition des 34 pratiques ITIL® 4**

| 🛎️ **Gestion de services** | 17  | Gestion des incidents, des changements… ✅ |
|----------------------------|-----|--------------------------------------------|
| 🧑‍💼 **Gestion générale**    | 14  | Gestion RH, stratégie, finances ❌         |
| 🔧 **Gestion technique**   | 3   | DevOps, Infra, déploiement logiciel ❌     |

## **🎯 Les 8 pratiques clés à connaître pour l'examen et la pratique terrain**

### **🛑 1. Gestion des incidents** 
➡️ Gérer **les interruptions de service** ou les **dysfonctionnements**.

📌 Objectif : **Réduire au maximum l’impact** d’un incident, **Rétablir le service rapidement**. 

👀 Concret : Un utilisateur n’arrive plus à se connecter à son PC, tu ouvres un ticket d’incident, tu fais un premier diagnostic rapide, tu résous ou escalades.



### **🧩 2. Gestion des problèmes** 
➡️ Identifier et **supprimer les causes racines** des incidents récurrents.

📌 Objectif : **Éviter que les incidents se reproduisent**, Mettre en place des solutions **durables**.

👀 Concret : Une imprimante réseau plante toutes les semaines. La gestion des incidents remet juste en route. La **gestion des problèmes** analyse pourquoi ça arrive et **corrige la cause** (driver incompatible, câble défaillant…).



### **🔁 3. Habilitation des changements** 
➡️ Autoriser, planifier et **contrôler les changements** apportés à l’infrastructure.

📌 Objectif : **Minimiser les risques**, Éviter les changements faits à l’arrache. 

👀 Concret : Un collègue veut mettre à jour une appli métier. Tu vérifies les risques, tu valides ou refuses, et tu **planifies le changement** pour un moment stratégique.



### **🎯 4. Centre de services (Service Desk)** 
➡️ C’est le **point de contact unique** entre l’IT et les utilisateurs.

📌 Objectif : **Accueillir les demandes**, incidents, questions, etc. **Orienter vers la bonne solution ou la bonne équipe**. 

👀 Concret : Tu es au téléphone, tu reçois une demande : “Je ne peux plus imprimer”. Tu crées le ticket, tu aides ou tu rediriges vers le technicien en charge des impressions.



### **📡 5. Surveillance et gestion des événements** 
➡️ Observer les **signaux du système** (logs, alertes, pings) et réagir.

📌 Objectif : **Anticiper les pannes**, détecter les anomalies, **Automatiser les réponses** si possible. 

👀 Concret : Tu reçois une alerte : “Serveur CPU à 95 %”. Tu agis **avant que ça plante**. Tu peux configurer Zabbix ou Centreon pour que les alertes soient automatiques.



### **📃 6. Gestion des niveaux de service (SLA)** 
➡️ Suivre les engagements de qualité pris avec les utilisateurs (ex : délai de résolution).

📌 Objectif : Assurer que le **niveau de service promis est respecté**, Évaluer la **performance des services IT**. 

👀 Concret : Un SLA indique que les tickets urgents doivent être traités sous 2h. Si tu mets 4h, tu dois **justifier** pourquoi, et t’adapter pour que ça ne se reproduise pas.



### **🧾 7. Gestion des actifs IT** 
➡️ Gérer tout le **matériel et logiciel** de l’organisation (parc info).

📌 Objectif : **Savoir ce que l’on a**, où c’est, et à qui ça appartient, Suivre le **cycle de vie complet** (achat, usage, recyclage). 

👀 Concret : Tu reçois 10 nouveaux PC portables. Tu les enregistres dans l’inventaire GLPI, tu les affectes aux utilisateurs, et tu suis leur maintenance.



### **🧪 8. Gestion de la configuration de service** 
➡️ Identifier et documenter **les composants IT et leurs relations**.

📌 Objectif : Construire une **CMDB** (base de données de config), **Comprendre les dépendances** (ce qui est lié à quoi). 

👀 Concret : Tu dois redémarrer un serveur. Tu vérifies dans la CMDB si ce serveur héberge des applis critiques, avant de faire une bêtise qui coupe tout un service métier.

