# Règles**📌 1️⃣️Règles explicites et refus implicite**

Les **permissions dans les ACL** sont **basées sur des règles explicites**. Cela signifie que :

- Les **groupes** doivent être explicitement mentionnés dans la **DACL** (Liste de contrôle d'accès discrétionnaire).
- Les **permissions** doivent être définies explicitement dans les **ACE** (Entrées de contrôle d'accès).  
  ❌ Si un groupe n'est **pas mentionné dans la DACL**, l'accès sera **refusé automatiquement** (c’est ce qu’on appelle un *refus implicite*).



**⚖️ 2️⃣ Privilège : Autorisation vs Refus**

Lorsque plusieurs **règles d’accès** s’appliquent à un **utilisateur**, voici comment ça fonctionne :

- Un utilisateur peut appartenir à plusieurs **groupes**.
- Si **deux groupes** différents ont des permissions opposées (par exemple, **l'un autorise la lecture**, l'autre la refuse), le **refus explicite** aura toujours **la priorité sur l'autorisation**.

Ce mécanisme est **fondamental pour la sécurité**. Le refus explicite **l’emporte toujours** sur une autorisation.



**🔄 3️⃣️Mécanisme d’héritage**

Les ACL sous **NTFS** bénéficient d’un **mécanisme d’héritage** très important pour la gestion des permissions.

- **Par défaut**, un répertoire **hérite des permissions de son dossier parent**.
- Si un fichier ou un sous-dossier reçoit des **permissions héritées**, celles-ci apparaîtront **grisées** et **non modifiables** dans les propriétés de l’objet.
- Les règles héritées ne sont pas prioritaires face aux règles explicites des objets enfants.



📑4️⃣**Ordre d'Évaluation**

Les ACL sont évaluées dans un ordre spécifique :

- Les refus explicites sont appliqués en premier et ont la priorité.
- Les permissions explicites sont ensuite évaluées.
- Les permissions héritées sont appliquées en dernier.



**⚙️ 5️⃣ Modifier l’héritage des ACL**

Si vous souhaitez **modifier les permissions** d’un objet tout en maintenant l’héritage des autorisations :

1.  **Modifiez les autorisations** sur le **dossier parent**.

&nbsp;

2.  Si cette modification impacte trop d’objets enfants et n’est pas souhaitée, vous pouvez **désactiver l’héritage** pour casser la relation et rendre les **permissions modifiables**.



**🏷 5️⃣️Déplacement et copie de fichiers**

Les **actions de déplacement et de copie** de fichiers ou dossiers peuvent influencer les autorisations :

- **Déplacement à l’intérieur du même volume** : Les **permissions d’origine** sont **conservées**, car le système de fichiers reste le même.
- **Déplacement entre différents volumes** ou **copie** : Le fichier ou dossier sera traité comme un **nouvel objet**, et il héritera des **permissions** du dossier cible.
