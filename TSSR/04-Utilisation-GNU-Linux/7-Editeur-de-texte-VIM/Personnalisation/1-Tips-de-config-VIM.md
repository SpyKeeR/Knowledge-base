# Tips de config VIM

## **📝 Comportement de Vim sous Debian (>=9)**

Depuis Debian 9, **Vim agit bizarrement** par défaut…

**🔧 Problèmes observés :**

- 🖱️ **Sélection souris** ➜ active automatiquement le **mode Visuel** (Visual Mode)
- 🧷 **Copie d’un bloc** avec des commentaires ➜ **multicommente tout à partir de la 1re ligne commentée**
  ➜ Résultat foireux du style :
```text
  #echo coucou
  devient
  #echo coucou #fi
```


### **🗂️ Problème de fichier .vimrc perso**

🛠️ Si tu crées un fichier **~/.vimrc**, Vim **ignore complètement** les configs de /etc/vim/vimrc (donc tout comportement "système" saute).

🔁 Résultat : tu perds les réglages globaux utiles, et Vim redevient pénible.



## **🛠️ Solution 🧘 : fichier /etc/vim/vimrc.local**

Pour retrouver un comportement *normal et stable*, crée le fichier :

/etc/vim/vimrc.local

Et mets-y les paramètres suivants :

**⚙️ Contenu recommandé :**

- source /usr/share/vim/vim81/defaults.vim ➜ récupère les réglages par défaut système
- let skip_defaults_vim = 1 ➜ empêche le rechargement en double
- if has('mouse') set mouse=r endif ➜ évite que la souris bascule en Visual Mode
- set paste ➜ désactive l’auto-indent lors du collage (nickel pour les blocs)



### **🚨 Attention à la version de Vim**

Le chemin /usr/share/vim/vim81/ dépend de ta version de Vim.

🔍 Vérifie avec `vim --version` et **remplace vim81 par vimXX** si besoin.





