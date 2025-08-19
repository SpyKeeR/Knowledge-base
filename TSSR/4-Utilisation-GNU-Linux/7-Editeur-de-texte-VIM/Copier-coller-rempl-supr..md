# Copier/coller/rempl/supr.Toutes ces actions se font en **mode normal (commande)**. Important : **Vim utilise son propre presse-papier interne**, pas celui de ton OS.

**❌ Supprimer**

- **x** : supprime un caractère sous le curseur
- **dw** : supprime un mot (à partir du curseur)
- **dd** : supprime la ligne entière

Astuce : tu peux les multiplier !

Par exemple : **3dd** : supprime 3 lignes d’un coup / **5x** : supprime 5 caractères

Chaque suppression est aussi une **opération de "coupé"**, donc tu peux recoller ce que tu viens de supprimer (tant que tu ne fais pas une autre suppression entre-temps).



**📋 Copier**

- **yy** : copie la ligne actuelle / **yw** : copie un mot (à partir du curseur)

Tu peux aussi copier plusieurs lignes : **5yy** → copie 5 lignes à partir de la ligne actuelle



**📥 Coller**

- **p** : colle *après* le curseur / **P** : colle *avant* le curseur

Exemple : tu fais dd pour couper une ligne, tu vas ailleurs, puis p pour la coller.

**🧠 Astuce sur les tampons (registre)**

Vim peut stocker les contenus copiés/coupés dans des **tampons nommés** (registres).

Utilise une lettre entre **a et z** pour désigner un registre :

- "ayy : copie la ligne dans le tampon a
- "ap : colle le contenu du tampon a

Mais attention : **ces tampons sont volatiles** → dès que tu quittes Vim, ils sont perdus.

Astuce : Copier plusieurs lignes dans un tampon : 5"ayy



**🔁 Remplacer du texte**

Remplacer dans Vim, c’est soit lettre par lettre, soit mot/ligne par mot/ligne :

| **Commande** | **Action** | **Passe en insertion ?** |
|----|----|----|
| **r** | Remplace 1 caractère | ❌ (juste une lettre tapée) |
| **R** | Mode remplacement (texte tapé écrase) | ✅ |
| **cl** | Supprime 1 caractère et insertion | ✅ |
| **cw** | Supprime 1 mot et insertion | ✅ |
| **cc** | Supprime la ligne et insertion | ✅ |
