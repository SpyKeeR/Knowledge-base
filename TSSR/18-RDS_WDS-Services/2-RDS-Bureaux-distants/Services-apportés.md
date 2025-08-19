# Services apportés🖥️ **Environnement de bureau complet**

- Interface utilisateur Windows complète (bouton Démarrer, barre des tâches, bureau)
- Hébergé côté serveur
- Permet un accès distant à un poste de travail collaboratif
- Intégré dans les ateliers pratiques



🪟 **Applications déportées (RemoteApp)**

- Affichage uniquement de la **fenêtre de l'application**, pas du bureau complet
- Exécution isolée pour une expérience plus ciblée
- Permet d’éviter la surcharge visuelle d’un bureau complet
- Prévu dans les ateliers pratiques



💻 **Machines virtuelles – VDI (Virtual Desktop Infrastructure)**

- Accès à des **VM individuelles** hébergées sur un hyperviseur
- Chaque utilisateur dispose de sa propre VM distante
- Offre flexibilité et puissance dédiée
- **Non abordé dans ce module** (trop complexe)



🔁 **Caractéristiques générales du service RDS**

👥 **Multi-utilisateurs** : plusieurs connexions simultanées

🪪 **Multi-sessions** : chaque utilisateur a sa propre session isolée

🏷️ **Environnements dédiés** :

- Ex. : service comptabilité avec accès restreint à ses outils
- Environnement RH avec applications spécifiques
- Adaptation des accès selon les besoins métier



🧩 **Protocole RDP – Remote Desktop Protocol**

🔌 Protocole réseau utilisé pour communiquer entre le client et le serveur

📡 Fonctionne via le port **TCP 3389**

📦 Transfère les entrées clavier/souris et l'affichage graphique

🧱 À ne pas confondre avec RDS (le rôle), RDP est **le protocole** utilisé
