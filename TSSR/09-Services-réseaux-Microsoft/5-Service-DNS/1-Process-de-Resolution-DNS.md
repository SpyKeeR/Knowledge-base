# Process de Resolution DNS

## **🌐 Résolution de noms : principe général**

Quand tu tapes une URL (ex. [www.google.com](http://www.google.com)), le système interroge un **serveur DNS**, souvent celui configuré sur ton pare-feu, box ou routeur.

S’il trouve l’adresse IP associée, la connexion se fait et le service s’affiche.

Même logique pour des noms internes (ex. servfic01) → DNS local.



## **⚙️ Fonctionnement du DNS au quotidien**

DNS = clé d’accès à **Internet** mais aussi aux **ressources internes** (fichiers, imprimantes, partages...).

➡️ On tape un nom → le système cherche son IP → la connexion se fait si l’IP est correcte.

➡️ Sans résolution DNS : pas de ping, pas d’accès, pas de communication.



## **🧠 Mécanismes utilisés pour résoudre les noms**

Lorsqu’on demande la résolution d’un nom, l’OS consulte différentes **sources** dans un ordre précis :

1.  **Cache DNS local** 🧳 → conserve les résolutions récentes (positives et négatives)
2.  **Fichier hosts** 📄 → fichier système local avec des liaisons IP ↔ nom définies à la main
3.  **Service DNS configuré** 🌍 → serveur DNS réseau (interne ou public) qui résout les noms

💡 Un cache "pollué" peut bloquer l'accès à un site ou retourner une fausse réponse.

➡️ On peut le vider avec ipconfig /flushdns.



## **🛰️ Utilisation du service DNS**

Un **client Windows** (ex. Windows 10) **doit avoir une IP de serveur DNS** configurée pour :

- Résoudre les noms Internet
- Rejoindre un domaine Active Directory 🏢
- Communiquer sur le réseau local avec des noms

Sans ça, il ne pourra **ni rejoindre le domaine**, ni accéder aux ressources partagées.



## **📄 Le fichier hosts (héritage old school mais toujours utile)**

Ce fichier situé dans C:\Windows\System32\drivers\etc\hosts permet de :

- Forcer une résolution locale
- Ajouter des IP ↔ noms manuellement
- **Contourner un DNS externe** (utile pour tests, redirections, blocage volontaire)

🧠 Syntaxe : IP nomd'hôte (ex. 192.168.1.10 serveurweb)

➡️ Les entrées du fichier hosts **ont priorité** sur le serveur DNS → très utile, mais aussi risqué si mal configuré !

Ex : écrire 172.17.0.0 google.com va **rediriger Google vers une IP fausse** → piégeage ou blocage.

