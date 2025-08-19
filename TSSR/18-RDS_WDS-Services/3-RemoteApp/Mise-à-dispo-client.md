# Mise à dispo client🌐 **Accès via le portail web (RD Web Access)**

• Le service **RD Web Access** permet aux utilisateurs de se connecter via un navigateur web 🌍

• L’URL du portail RDS est du type :

<https://nom_du_serveur/RDWeb>

• Une fois **authentifié**, l’utilisateur voit la **liste des applications publiées** et peut les lancer d’un simple clic ✅

• Ce service repose sur **IIS** et doit être **installé** et correctement **configuré** côté serveur ⚙️



🧭 **Accès via le menu Démarrer (RemoteApp & Bureau à distance)**

• Utilisation du **panneau de configuration** → **Connexions RemoteApp et Bureau à distance**

• Saisir l’URL du flux d'applications :

<https://nom_FQDN_du_serveur/RDWeb/Feed/webfeed.aspx> 🔗

• Une fois connecté, les applications apparaissent automatiquement dans le menu **Démarrer** dans le dossier **Work Resources** 🗂️



🔒 **Sécurité : Certificat serveur requis**

• Pour que les RemoteApp apparaissent correctement dans le menu Démarrer, le **certificat serveur** doit être **installé sur chaque poste client** 🔐

• Ce certificat assure une **connexion sécurisée** entre le client et le serveur RDS 🛡️



📌 **Résumé**

• Deux méthodes principales :

└ 🌐 **Portail web** (facile d'accès)

└ 🧭 **Menu Démarrer** (intégré au quotidien)

• Le **certificat serveur** est **indispensable** pour le bon fonctionnement et la sécurité de l’accès RemoteApp ✅
