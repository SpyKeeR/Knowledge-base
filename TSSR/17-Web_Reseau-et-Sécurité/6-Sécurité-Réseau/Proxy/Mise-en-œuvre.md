# Mise en œuvre🧩 **Mise en œuvre du proxy avec pfSense, Squid & SquidGuard**

pfSense permet une **intégration simple et efficace** de Squid et SquidGuard pour le filtrage du surf dans un pare-feu.

🔧 **Paramètres Squid dans pfSense** :

• 💽 Hard Disk Cache Size : par défaut 100 Mo, peut être augmenté (ex. : 1024 Mo = 1 Go).

• ✅ Enable Squid Proxy : active le service Squid.

• 🌐 Listen IP Version : écoute en **IPv4, IPv6 ou les deux**.

• 🖧 Proxy interface(s) : interface(s) sur lesquelles activer le proxy (ex. : LANCLIENT).

• 🔢 Port du mandataire : par défaut **3128**.

• 👥 Allow Users on interface : autorise les clients du réseau LAN à utiliser le proxy.

• 🌐 Resolve DNS IPv4 First : aide à accéder aux sites HTTPS.



🔐 **Support HTTPS/SSL** :

• 🔒 HTTPS/SSL Interception : active l'interception SSL.

• 🧭 SSL Intercept Interface(s) : définit l'interface de filtrage HTTPS.

• 📍 SSL Proxy Port : **3129** par défaut.

• 🛡️ AC : sélection du **certificat SSL/TLS** à utiliser pour intercepter les flux HTTPS.



📊 **Journaux d’accès** :

• 📝 Enable Access Logging : active la **journalisation complète des connexions** web.

• 🧾 Visible Hostname & Administrator's Email : affichés aux utilisateurs en cas de blocage (ex : page d’erreur).



🛠️ **Autorisations et filtrage** :

• 🧩 Allowed Subnets : réseaux autorisés à passer par le proxy.

• 🚫 Blacklist : domaines ou mots-clés à bloquer.

• 🔘 Activation ou désactivation de **SquidGuard**.



📌 **Paramètres SquidGuard** :

• 📋 Enable Log : active la journalisation des événements de SquidGuard.

• 🌐 Blacklist URL : définit la **source des blacklists** (ex : <https://dsi.ut-capitole.fr/blacklists/download/>).

• 📥 Download : télécharge les listes.

• 📂 Target Rules List : ajout de règles (Allow, Deny selon les catégories).

• ⚙️ Default access [all] : règle par défaut (souvent allow).

• 🛑 Do not allow IP addresses in URL : bloque les URL contenant des IP (pratique anti contournement).

• 🧽 Use SafeSearch engine : force le **filtrage SafeSearch** sur les moteurs de recherche.

• 🏷️ Target categories : permet de **créer ses propres listes personnalisées**.
