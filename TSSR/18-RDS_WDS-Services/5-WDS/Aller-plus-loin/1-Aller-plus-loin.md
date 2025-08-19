# Aller plus loin🧱 **Cadre d’un déploiement minimaliste**

WDS permet :

- 🌐 Le **démarrage réseau (PXE)** des postes clients
- ⚙️ L’accès à un **environnement de déploiement interactif**
- 🧪 Une **mise en place simplifiée** adaptée aux premiers ateliers  
  🚫 Ce cadre reste **insuffisant** pour une entreprise, car **non automatisé ni centralisé**



⚠️ **Limites de WDS en entreprise**

- 🔒 Absence de gestion avancée
- 🧩 Nécessité d’intégrer un outil complémentaire : **Microsoft Deployment Toolkit (MDT)**
- 🔁 MDT + WDS forment un service de **déploiement complet et fiable**



🧰 **Éléments complémentaires de l’infrastructure WDS**

🔧 **Images de capture**

- 🖼️ Permettent de créer des **images personnalisées** depuis un poste de référence
- ⚙️ Nécessitent une **image de capture spécifique** dans WDS
- 🛠️ Utilisation d’outils comme Sysprep et l’assistant d’image de capture

📄 **Fichiers de réponse (Unattend.xml)**

- 🤖 Automatisent les installations
- 🎯 Contiennent des **réglages préconfigurés** (nom du PC, clé produit, compte admin, etc.)
- 📥 S’intègrent à l’environnement WDS ou MDT pour un déploiement sans intervention

🧬 **Gestion des pilotes**

- 💾 Indispensable pour garantir la compatibilité matérielle post-déploiement
- 🗂️ Ajout manuel des pilotes dans WDS ou automatisation via MDT
- 🧠 Organisation possible par modèle ou type de matériel



🤝 **Vers une infrastructure MDT**

- 🧩 Les notions vues ici (images de capture, fichiers de réponse, pilotes) sont **fondamentales**
- 🛠️ Elles seront approfondies via **Microsoft Deployment Toolkit (MDT)**
- 🚀 MDT permet une **gestion centralisée, automatisée et évolutive** des déploiements
