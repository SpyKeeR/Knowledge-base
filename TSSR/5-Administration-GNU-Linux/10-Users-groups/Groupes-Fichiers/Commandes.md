# Commandes**➕ Ajouter un groupe : groupadd**

La commande de base pour créer un nouveau groupe.

**Syntaxe :** groupadd nom_du_groupe

✅ **Options utiles** :

- -g GID : permet de **spécifier manuellement** un identifiant de groupe (GID). À utiliser si tu veux un numéro précis, par exemple pour respecter une convention ou un système existant. Attention à ne pas utiliser un GID déjà pris !
- -r : crée un **groupe système** (généralement utilisé pour les services ou daemons). Cela force l’usage d’un GID dans une plage plus basse (souvent < 1000).

💡 Si tu ne précises pas d’option, le système attribuera automatiquement le **premier GID disponible** à partir de 1000.



**✏️ Modifier un groupe : groupmod**

Permet de **modifier un groupe existant**.

**Options principales** :

- -g nouveau_GID : change l’identifiant du groupe
- -n nouveau_nom : renomme le groupe

👀 Utile si tu dois restructurer une organisation ou corriger une erreur.



**❌ Supprimer un groupe : groupdel**

Utilisée pour **supprimer un groupe** du système.

⚠️ Attention : cette commande **ne supprime pas les utilisateurs** du système, **mais le groupe doit être vide** (aucun utilisateur ne doit l’avoir comme secondaire).



**👥 Gérer les membres : gpasswd**

Cette commande sert à **ajouter ou retirer des utilisateurs dans un groupe secondaire**.

**Options courantes** :

- -a utilisateur groupe : ajoute un utilisateur au groupe
- -d utilisateur groupe : retire un utilisateur du groupe

Exemples :

- gpasswd -a maxime sudo → ajoute Maxime au groupe sudo
- gpasswd -d maxime audio → retire Maxime du groupe audio
