# Autres cmds

## **🕒 📅 Date et heure**

|     Commande                                |     Description                        |     Exemple                                       |
|---------------------------------------------|----------------------------------------|---------------------------------------------------|
|     date                                    |     Affiche la   date/heure système    |     ven. 26 janv. 2025   14:20:30 UTC+2           |
|     date +"%A, %d   %B %Y%nIl est %H:%M"    |     Format   personnalisé 📌            |     dimanche, 27 avril   2025     Il est 10:15    |

### **⏱️ Options utiles pour le format :**

- %A → Jour complet
- %d → Jour du mois (01-31)
- %B → Mois complet
- %Y → Année 4 chiffres
- %H → Heure (24h)
- %M → Minutes

⚠️ + doit être placé **une seule fois** au début !



## **📆 cal – Affiche un calendrier**

- `cal` → Calendrier du **mois courant**
- `cal 2025` → Année complète
- `cal 04 2025` → Mois d’avril 2025



## **🖥️ tty – Type de terminal**

- `tty` → Donne le **type de session**
  - Ex : /dev/tty1 → Terminal local
  - Ex : /dev/pts/2 → Session SSH ou terminal graphique



## **📂 pwd – Emplacement courant**

- `pwd` → Affiche le **répertoire actuel**
  - Ex : /home/user30

💡 Astuce : souvent visible dans le prompt (user@machine:~/dossier), mais pas toujours. Cette commande te sauvera 🦸
