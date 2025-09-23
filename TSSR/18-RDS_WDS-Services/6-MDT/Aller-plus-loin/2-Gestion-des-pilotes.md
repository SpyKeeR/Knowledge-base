# Gestion des pilotes

## 🚀 **Rôle des pilotes dans un déploiement MDT**

- 📡 **Phase de boot (WinPE)**
  ➤ Nécessite surtout les **pilotes réseau** et **stockage** pour :  
  • Accéder au réseau depuis la machine cible  
  • Accéder au disque dur pour y déployer l’OS  
  ⚠️ En général, **les pilotes natifs suffisent**

- 💽 **Phase de déploiement de l’OS**
  ➤ Nécessite des **pilotes matériels spécifiques** (vidéo, audio, chipset, etc.)  
  ➤ Ceux-ci doivent souvent être **importés manuellement** dans MDT



## 📁 **Organisation des pilotes dans MDT**

- Tous les pilotes sont à importer dans la section :  
  ➤ Out-of-Box Drivers

- Recommandation : organiser les drivers par :  
  🔹 **Marque**
  🔹 **Modèle**
  🔹 **Version de l’OS cible**

- Objectif : garantir une structure claire et évolutive  
  ➕ Favoriser un **parc homogène** pour limiter la surcharge de la base de pilotes



## 🧪 **Personnalisation de la phase de démarrage (WinPE)**

- ✅ Par défaut : tous les **drivers réseau** sont inclus dans WinPE
- 🔄 Option : utiliser des **profils personnalisés** pour cibler certains pilotes à injecter au boot
