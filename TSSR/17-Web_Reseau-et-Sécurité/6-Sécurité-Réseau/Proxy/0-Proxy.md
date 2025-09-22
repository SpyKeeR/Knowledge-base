# 🛡️ **Le Proxy**

Le proxy agit comme **intermédiaire entre les clients et Internet**, filtrant les requêtes web pour **protéger le réseau interne**.

### ✔️ **Fonctions principales** :

- 🔒 **Filtrage des sites web malveillants** via des blacklists (liste noire).
- 👁️ **Surveillance des connexions sortantes**, utile contre les salariés malveillants.
- 🗂️ **Mise en cache** des ressources web fréquemment consultées (gain de bande passante et vitesse de chargement).
- 🕵️ **Transparence pour l’utilisateur final** : configuration en mode transparent dans le réseau local.
- 📈 **Traçabilité** : toutes les connexions sont **journalisées**.

📜 **Aspect légal** (France) : Les logs d'accès doivent être **conservés pendant un an**, selon l’**article L34-1** du Code des postes et communications électroniques, ainsi que l’**article 9 de la LCEN** du 21 juin 2004.



## 🧊 **Squid Proxy**

Squid est un **logiciel proxy open source** très répandu. Il peut fonctionner à la fois en **proxy direct** et **reverse proxy**.

🧰 **Fonctionnalités principales** :

- 🌐 Prise en charge des protocoles **HTTP, HTTPS, FTP, Gopher**.
- ⚙️ Utilise un processus **asynchrone performant** pour gérer un grand nombre de connexions.
- 🗃️ **Mise en cache** efficace des contenus web (réduction du trafic + amélioration des temps de réponse).
- 🔄 Sert d’**intermédiaire entre clients et serveurs** (isolation + sécurité).
- 👨‍🔧 Dispose d’une **grande communauté open source**, avec mises à jour fréquentes.
- 🧩 Supporte de nombreuses **options de configuration avancée** : authentification, filtrage, quotas.



## 🚫 **SquidGuard (filtrage d’URL)**

SquidGuard est un **module complémentaire** de Squid qui permet un **filtrage d’URL basé sur des catégories**.

📚 **Fonctionnalités clés** :

- 📂 Utilise des **blacklists classées par thèmes** (pornographie, réseaux sociaux, jeux, etc.).
- 🧱 **Bloque l'accès à des sites indésirables** en amont.
- 🧮 Intégration **transparente avec Squid** : facile à activer/configurer.
- 🛡️ Recommandé pour les **environnements professionnels ou scolaires**.
