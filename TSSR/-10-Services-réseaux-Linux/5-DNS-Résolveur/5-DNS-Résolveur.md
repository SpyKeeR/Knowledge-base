# 5 - DNS Résolveur**🧠 Objectifs du module DNS Résolveur**

🧩 **Comprendre le fonctionnement du service DNS**  
➤ Savoir comment un nom de domaine (ex. google.fr) est résolu en adresse IP  
➤ Suivre les étapes de résolution : récursif, itératif, autorité, cache, etc.



🛠️ **Mettre en place un serveur DNS résolveur**  
➤ Installer et configurer un service type Bind ou Unbound  
➤ Permettre la résolution pour le réseau local via ce serveur  
➤ Gérer le cache, la sécurité (ex : forwarding, DNSSEC, restriction IP), et les logs



**🧭 À quoi sert un résolveur DNS ?**

Un **serveur DNS résolveur** agit comme un **intermédiaire entre les machines et Internet** :

💬 Quand tu tapes un nom de domaine, il s’occupe d’aller chercher l’IP, en allant questionner d’autres serveurs (racine, TLD, autoritaire) **et en mettant les réponses en cache**.

🧱 C’est un **élément critique** du réseau interne :

→ Si ton DNS tombe, tu n’as plus de navigation, même si l’accès Internet est OK !



**🔧 Pourquoi monter son propre DNS Résolveur ?**

- ⚡ Vitesse : les réponses sont en cache local
- 🔒 Sécurité : filtrage, logs, DNSSEC
- 📊 Contrôle : surveillance et redirection possible
- 🧰 Indispensable en entreprise, labo ou même chez soi pour comprendre comment marche le réseau
