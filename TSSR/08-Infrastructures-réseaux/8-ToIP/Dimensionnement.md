# Dimensionnement**🧮📐 Dimensionnement ToIP : pourquoi c’est crucial ?**

Une fois l’audit terminé, on passe à la **phase technique** : il faut estimer les **ressources réseau** nécessaires pour supporter la téléphonie IP. Ce dimensionnement repose sur deux points clés :

- 🎧 **Codec utilisé** (débit consommé par appel)
- 📶 **Nombre de canaux simultanés** (appels en parallèle à supporter)

Ces éléments permettent de calculer **la bande passante minimale** requise.



**🚦🔢 Calcul de la bande passante**

➡️ Une règle simple pour démarrer :

**100 kbps x nombre de canaux simultanés** = bande passante nécessaire (avec une marge de sécurité)

🧪 Exemple :

- Besoin : 20 canaux simultanés
- Bande passante mini = **100 x 20 = 2000 kbps** (soit 2 Mbps)

👉 Il faut aussi prévoir une **ligne de secours (SDSL)**, une **ligne principale (FTTO)** et une **QoS bien configurée** pour prioriser la voix 📊.



**🎙️📊 Consommation des codecs**

Chaque codec a une **empreinte réseau** différente. Le plus courant :

- **G.711** : 64 kbps par appel (qualité haute, pas compressé)
- **G.729** : 8 kbps par appel (qualité réduite, très compressé)

📌 Ex : Avec un lien SDSL à 2 Mbps symétrique, et le codec G.711 →

**2000 / 64 = ~31 appels simultanés possibles**

⚠️ Attention à ajouter un **petit buffer** pour la signalisation (SIP, RTP) et pour éviter la saturation réseau.



**🎯✅ En résumé : ce qu’il faut garantir**

Pour un dimensionnement efficace :

- 🧠 Bien connaître son **nombre d’utilisateurs**
- 🔍 Choisir un codec adapté (qualité/débit)
- 🧮 Calculer la **bande passante totale** nécessaire
- 🧰 Mettre en place **FTTO + SDSL + QoS**

💡 Un bon dimensionnement = moins de latence, moins de gigue, meilleure satisfaction utilisateur 📞✨
