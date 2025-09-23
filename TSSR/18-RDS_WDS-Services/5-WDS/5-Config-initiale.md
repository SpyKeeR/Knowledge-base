## 🧭 **Lancement de la configuration**

- 📁 Une fois le rôle **WDS installé**, la configuration initiale s’effectue via l’**assistant** intégré
- 🖱️ Ouvrir la **console de gestion MMC WDS**, clic droit sur le serveur → **Configurer le serveur**



## 🔑 **Étapes clés de la configuration initiale**

### 📌 **a. Contexte d’intégration Active Directory**

- 🏢 Choix entre fonctionnement **en domaine** ou **hors domaine**
- ✅ Recommandé : **intégration à un domaine Active Directory** pour un déploiement centralisé et géré

### 💾 **b. Dossier de partage des données WDS**

- 📂 Spécifier un **emplacement dédié** pour stocker les images et fichiers de configuration
- 🚨 Recommandé : **disque distinct** avec **grande capacité**, les images système peuvent être volumineuses

### 📡 **c. Configuration du démarrage réseau (PXE)**

- 📶 Choix de la **stratégie de réponse PXE** :
  - Autoriser **tous les clients**
  - Autoriser uniquement certains postes (**filtrage**)
- 🛠️ Par défaut, aucun client n’est autorisé, une configuration explicite est donc nécessaire



## 📌 **Résumé**

⚙️ L’assistant de configuration WDS permet de :

1.  Définir le **contexte domaine / hors domaine**
2.  Spécifier un **répertoire de stockage dédié et suffisant**
3.  Configurer le **mode de réponse PXE** (tous / ciblés)  
    🧱 Ces étapes assurent une base solide pour l’exploitation du service WDS
