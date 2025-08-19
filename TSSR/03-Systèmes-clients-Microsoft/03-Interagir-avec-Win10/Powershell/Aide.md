# Aide**🔍 Aperçu de l'aide PowerShell**

- PowerShell dispose d'une **aide intégrée** qui permet d'apprendre et d'utiliser efficacement ses commandes et concepts.

&nbsp;

- Cette aide fonctionne comme un **manuel structuré**, accessible à tout moment pour répondre à nos questions.

**⚙️ Évolution de l'aide à partir de PowerShell 3**

- **PowerShell 1 & 2** : L'aide était intégrée nativement.
- **PowerShell 3 et versions ultérieures** :
  - L'aide n’est plus incluse **par défaut**.
  - Il faut la **télécharger** manuellement.
  - **Seule la version anglaise est régulièrement mise à jour**.



**📌 Mettre à jour l'aide**

- Il est recommandé de **mettre à jour l’aide dès la première utilisation**, surtout sur une machine professionnelle.
- **Commande pour mettre à jour l’aide** *(exécutable en mode administrateur)* : ***Update-Help***
  - Télécharge l’aide officielle depuis les serveurs Microsoft.
  - Peut être redirigé vers un dépôt local (utile en entreprise).



**❓ Consulter l'aide**

- **Afficher l’aide générale** : ***Get-Help***
- **Aide affichée page par page** : ***Help***

&nbsp;

- **Alias UNIX pour afficher l'aide** : ***man***
- 

**🔎 Obtenir de l’aide sur une commande spécifique**

- **Exemple avec la commande Disable-LocalUser** : ***Get-Help Disable-LocalUser***
- **Consulter les concepts PowerShell** : ***man about_Variable***
  - Toutes les sections commençant par about_ expliquent des notions spécifiques.



**🚀 Astuces pour optimiser l’utilisation de l’aide**

- **Voir des exemples concrets d’une commande** : ***Get-Help Disable-LocalUser -Examples***
- **Afficher l’aide complète dans une fenêtre séparée** *(avec barre de recherche)* : ***Get-Help Disable-LocalUser -ShowWindow***
- **Consulter l’aide en ligne (toujours à jour)** : ***Get-Help Disable-LocalUser -Online***
