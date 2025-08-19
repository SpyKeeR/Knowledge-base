# Monitoring👀 **Rôle du monitoring**

- Permet de **suivre en temps réel** les déploiements depuis la console MDT
- Utile surtout quand l’accès direct aux machines est difficile (pas d’écran branché, unités centrales éloignées)



⚙️ **Activation et configuration**

- 🔧 Se configure dans les **propriétés du partage de déploiement**, onglet **Monitoring**
- ⚠️ Désactivé par défaut, il faut l’activer manuellement
- 📝 Ajouter dans le fichier **CustomSettings.ini** la ligne : eventService=http://<nom_du_serveur_MDT>:9800
- 🔌 Le port **9800** doit être celui configuré dans MDT pour le service de monitoring



📈 **Suivi des déploiements**

- Via le menu **Monitoring** dans la console MDT, à gauche
- Visualisation de l’état d’avancement des déploiements des postes
- Indication claire quand le déploiement est **"completed"** (terminé avec succès)
- Affiche :
  - Temps total du déploiement
  - Nombre de tâches réalisées dans la séquence



🔑 **Points clés**

- Le monitoring offre une **visibilité précise** sur l’état des déploiements
- Essentiel pour les environnements où les machines ne sont pas facilement accessibles physiquement
- Permet une **meilleure gestion et contrôle** des opérations de déploiement
