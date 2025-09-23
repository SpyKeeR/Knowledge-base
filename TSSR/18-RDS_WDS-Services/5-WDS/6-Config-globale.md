## 📋 **Accès aux paramètres avancés**

- 🖱️ Clic droit sur le **serveur WDS** > **Propriétés**
- ⚙️ Plusieurs **onglets** disponibles pour affiner le fonctionnement du service
- 💡 Objectif : personnaliser, automatiser et optimiser les déploiements



## 📡 **Paramétrage DHCP**

- 🎯 Si le serveur **DHCP est installé sur le même serveur que WDS** :
  - ❌ WDS **ne doit pas écouter sur les ports DHCP**
  - ✅ Le serveur DHCP doit **fournir les options PXE**
  - ☑️ **Cocher les options spécifiques** dans l’onglet DHCP de WDS
- 🔕 Si DHCP est hébergé ailleurs :
  - 🚫 **Ne pas cocher** ces cases



## 🤖 **Automatisation du démarrage PXE**

- 🖥️ Par défaut, WDS demande une **intervention utilisateur (appui sur une touche)** pour initier le boot PXE
- ⚡ Dans l’onglet de **démarrage (Boot)**, activer l’option pour :
  - 🔄 **Automatiser le processus** de démarrage réseau
  - 💻 Permettre aux clients PXE de **booter sans interaction**



## 📌 **Résumé**

La **configuration globale** permet de :

1.  Adapter le comportement WDS en fonction de la **présence du service DHCP**
2.  Automatiser le **lancement PXE** pour améliorer l’expérience utilisateur  
    🎯 Objectif : **fluidifier le déploiement** et **réduire les interventions manuelles**
