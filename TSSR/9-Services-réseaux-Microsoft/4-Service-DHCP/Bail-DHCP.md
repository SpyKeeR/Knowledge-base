# Bail DHCP**📄 Qu’est-ce qu’un bail DHCP ?**

Un **bail DHCP** (ou *DHCP Lease*) est un contrat temporaire entre le client et le serveur DHCP.

⏳ Il contient : **adresse IP**, **masque de sous-réseau**, **passerelle**, **DNS**, **suffixe DNS**, **WDS (si besoin)**, etc.

📍 Ces paramètres sont définis dans une **étendue** (scope), qu’on verra plus en détail plus tard.



**🔄 Processus d’obtention du bail : DORA**

Le processus suit les 4 étapes **DORA** ⬇️

1.  **D**iscover : le client envoie une requête pour détecter les serveurs DHCP disponibles
2.  **O**ffer : un ou plusieurs serveurs répondent avec une **offre de bail**
3.  **R**equest : le client choisit une offre et demande les paramètres proposés
4.  **A**cknowledgement : le serveur confirme → le bail est actif, les infos réseau sont attribuées

💡 Ce processus est automatique, rapide et essentiel au démarrage d’un poste en IP dynamique.



**🔁 Renouvellement automatique du bail**

Un bail a une **durée de vie limitée**, ex : 6 jours

📆 Renouvellement automatique à deux moments clés :

- ⏱️ **50 %(1/2)** de durée (ex : jour 3) → le client tente un renouvellement discret
- ⏱️ **87,5 %(1/8)** (ex : jour 5,25) → nouvelle tentative si le 1er essai a échoué

✅ Si le serveur accepte, le bail est **prolongé**

❌ Si pas de réponse du serveur, le bail **expire**, et le client **relance le processus DORA**



**🗑️ Résiliation du bail**

Un bail peut être **libéré manuellement** :

🧑‍💻 L’administrateur ou le poste client envoie une requête **DHCP Release**

➡️ Cela informe le serveur que l’adresse IP peut être **réattribuée** à un autre appareil

🎯 Utile lors de : reconfigurations réseau, remplacement de postes, nettoyage de baux expirés…
