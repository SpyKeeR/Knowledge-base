# Espace disque**🖥️ df – Analyser l’espace disque utilisé**

**Objectif** : Afficher l’espace utilisé et disponible sur les systèmes de fichiers montés.



| **Commande** | **Description** |
|----|----|
| df | Affiche l’espace occupé sur tous les systèmes de fichiers montés. |
| df -h | Version "human-readable" ➔ tailles en K, M, G, T pour plus de lisibilité. |
| df -i | Affiche l’**utilisation des inodes** (utile si un disque est plein malgré peu de Go utilisés). |
| df -h /chemin/ | Cible spécifiquement un chemin ou un périphérique pour afficher son usage disque. |



**À savoir** :

- df prend en compte **seulement les systèmes montés**.
- -h est **quasiment indispensable** pour lire facilement les tailles.
- Les inodes (-i) sont critiques sur les serveurs ou systèmes avec **des millions de petits fichiers**.



**📂 du – Analyser la taille des dossiers**

**Objectif** : Calculer la place utilisée par un dossier et ses sous-dossiers.



| **Commande** | **Description** |
|----|----|
| du | Affiche la taille de chaque dossier et sous-dossier (très verbeux). |
| du -h | Affiche les tailles en format lisible (K, M, G, T). |
| du -hs | Résume seulement la taille totale du dossier, sans détails sur les sous-dossiers. |



**À savoir** :

- du est **très utile** pour trouver **quel dossier consomme** le plus d’espace.
- L’option -s (summary) permet de ne pas noyer l’affichage si on veut juste savoir combien pèse un dossier globalement.
