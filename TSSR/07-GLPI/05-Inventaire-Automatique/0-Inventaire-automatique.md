### 🏁 **Où ça se passe ?** ↳ **Accueil \> Administration \> Inventaire**

Ici, on entre dans le **cœur du suivi automatique des postes**. Une fois bien configuré, GLPI peut **remonter tout seul** les infos de tes machines via un agent, sans que tu aies à intervenir manuellement. Tu vas pouvoir voir les composants, les logiciels, les utilisateurs, etc.

 

## 🛠️ **Configuration de l'inventaire**

### ✔️ **Activer l’inventaire**

Coche cette case pour que le serveur soit prêt à **recevoir les données envoyées par les agents** installés sur les postes clients.

### 📦 **Éléments à remonter**

Tu peux choisir **ce que tu veux voir apparaître** dans ton inventaire. Exemples : Composants matériels (CPU, RAM, disque…), Systèmes d’exploitation, Logiciels installés, Imprimantes, Cartes réseaux…

### 🟢 **Statut par défaut**

Tu définis ici le **statut des machines nouvellement inventoriées**, par exemple :

- *En service* (si tu veux les considérer directement comme actifs)

- *À vérifier* (si tu veux faire une validation manuelle avant qu'elles soient considérées comme officielles)

### ⏰ **Fréquence de l’inventaire**

Tu peux ajuster la fréquence à laquelle l’agent **renvoie les infos au serveur** GLPI.

Ex : 1 fois par jour, par semaine, etc. → À adapter selon la **taille du parc** et la **sensibilité des changements**.

 

## 🏷️ **Paramétrage des entités et des TAGs**

🎯 Si tu gères plusieurs entités ou sites dans GLPI (ex : agences, départements, filiales…), tu peux leur **attribuer un TAG**.

Ce tag sera utilisé par l’agent pour **classer automatiquement** les postes remontés dans la bonne entité.

👉 **Exemple concret** : Tu installes un agent sur les machines d’un bureau à Nantes, et tu leur donnes le tag NANTES. Quand elles s'inventorient, GLPI les classe directement dans l'entité "Agence de Nantes". Pas besoin de tri manuel.

 

## 🖥️ **Installer l’agent d’inventaire GLPI sur les postes**

Tu dois installer **l’agent GLPI** (appelé aujourd'hui "GLPI Agent") sur chaque poste client pour qu’il : Fasse un **scan complet** du système (matériel + logiciel) et Envoie ces infos au serveur GLPI via l’API REST

 

## 🔁 **Cycle typique d’inventaire automatique :**

1.  L’agent est installé et configuré sur le poste

2.  Il fait un scan à intervalle régulier

3.  Il envoie les données à GLPI (URL du serveur + clé API)

4.  GLPI reçoit les données et les intègre dans la base

5.  Tu peux consulter les postes, voir ce qui a changé, et générer des rapports


