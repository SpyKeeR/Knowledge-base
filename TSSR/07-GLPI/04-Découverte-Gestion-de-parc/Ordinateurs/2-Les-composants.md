# Les composants

Dans GLPI, un **composant** c’est un **élément matériel que tu peux retrouver dans plusieurs objets** (souvent des ordinateurs). Exemple : un processeur Intel i7 11700K ou une barrette de RAM DDR4 16 Go 3200 MHz.

Pourquoi c’est utile ? Parce que ça t'évite de **ressaisir la même info** 15 fois, et surtout, tu peux savoir **dans quels équipements se trouve chaque composant**, faire des statistiques, prévoir du remplacement ou du dépannage plus facilement. 

📦 Un composant ≠ un seul objet → un composant peut être partagé entre plusieurs objets (c'est une sorte de *fiche modèle* de pièce détachée).



## 📦 **📚 Liste des composants disponibles dans GLPI**

🔧 Voici les types que tu peux gérer : 
- **Alimentations**,
- **Batteries**,
- **Boîtiers, Caméras**,
- **Capteurs, Cartes SIM**,
- **Cartes mères**, **Cartes graphiques**,
- **Cartes réseau**,
- **Cartes son,**
- **Composants génériques** = utile si t’as un composant non répertorié ailleurs (genre carte spécifique ou accessoire rare). 🧪
- **Contrôleurs, Disques durs**,
- **Firmware**,
- **Lecteurs, Mémoires**,
- **Processeurs**,
- **Périphériques PCI**



## 🧱 **🆕 Création d’un nouveau composant** 

➡️ Menu : **Accueil > Configuration > Composants > + Ajouter**

Tu dois choisir le **type** du composant à la création. Une fois le type choisi, GLPI te propose des champs spécifiques en plus des champs communs. 
👀 Par exemple :
- Si tu choisis “**Processeur**” → tu verras “Nombre de cœurs”, “Threads”, “Fréquence par défaut”, etc, 
- Si tu choisis “**Disque dur**” → tu verras “Capacité par défaut”, “Cache”, “Vitesse de rotation”, etc.



## 📋 **🖊️ Contenu d’une fiche composant**

Tous les composants partagent un tronc commun d’infos 📑 : 
- **Nom** : C’est le nom du composant (ex. “Intel Core i5-12600K”), 
- **Fabricant** : Intel, AMD, Samsung, Crucial…, 
- **Type** : (selon le composant, ex. "DDR4", "SATA", "NVMe", etc.), 
- **Modèle** : Rattaché aux **modèles prédéfinis** dans *Configuration > Intitulés > Modèles,* 
- **Commentaires** : zone libre pour infos utiles. 

Ensuite, selon le **type**, tu as des champs spécifiques, voici quelques exemples pour que ce soit plus parlant :

- 📦 **Processeur** : Fréquence par défaut (MHz), Nombre de cœurs / Threads, Chipset, Date de publication
- 📦 **Disque dur** : Capacité par défaut, Cache, Vitesse de rotation (RPM), Interface (SATA, NVMe…)
- 📦 **Alimentation** : Puissance (Watt), ATX : Oui/Non
- 📦 **Carte graphique** : Mémoire par défaut, Interface
- 📦 **Carte réseau** : Adresse MAC par défaut, Débit
- 📦 **Caméra** : Flash (Oui/Non), Distance focale, Orientation, Taille du capteur

🎯 **Objectif** : Tu crées une fiche complète et normalisée pour que les techniciens puissent l’attribuer à un objet sans avoir à ressaisir les caractéristiques.


## 🔍 **🔗 Éléments liés à un composant**

Une fois ta fiche créée, elle peut contenir : 
- **📂 Éléments** : liste des objets (PC, serveurs…) qui utilisent ce composant → *Très utile pour retrouver tous les postes avec une même RAM ou un CPU donné*
- **📄 Documents** : tu peux lier manuels, fiches techniques, notices PDF…


