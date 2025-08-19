# Gestion des accés/options🚪 **Gestion des accès simples – Directive Require**

Contrôle des permissions d'accès à un site ou une ressource.

| **🧾 Directive** | **🎯 Description** |
|----|----|
| Require all granted | ✅ Autorise tous les accès |
| Require all denied | ❌ Refuse tous les accès |
| Require ip 192.168.10.200 | 🖥️ Autorise uniquement l'IP 192.168.10.200 |
| Require ip 192.168.10 | 🌐 Autorise toutes les IP du réseau 192.168.10.0/24 |
| Require host tssr.lcl | 🔍 Autorise les hôtes dont le FQDN contient tssr.lcl (recherche DNS inversée + directe) |
| Require forward-dns ad.tssr.lcl | 🌐 Autorise uniquement l’hôte avec FQDN ad.tssr.lcl (recherche DNS directe) |
| Require local | 🏠 Autorise uniquement les adresses locales (127.0.0.1, ::1, etc.) |



🧩 **Gestion des accès multiples – Directives combinées**

1️⃣ **<RequireAll>** – Toutes les conditions doivent être vraies

💡 Fonctionne comme un **ET logique**

<RequireAll>  
Require ip 192.168.10  
Require not ip 192.168.10.100  
</RequireAll>

✅ Autorise tout le réseau 192.168.10.0/24 sauf l’IP 192.168.10.100



2️⃣ **<RequireAny>** – Au moins une condition doit être vraie

💡 Fonctionne comme un **OU logique**

<RequireAny>  
Require ip 192.168.10  
Require ip 192.168.30  
Require not ip 192.168.20  
</RequireAny>

✅ Accès autorisé si l’IP appartient à un des deux premiers réseaux ou **n’appartient pas** au 3e



3️⃣ **<RequireNone>** – Aucune condition ne doit être vraie

💡 Fonctionne comme un **NON logique**

<RequireNone>  
Require ip 192.168.10  
Require ip 192.168.20  
</RequireNone>

🚫 Refuse tout accès aux IP issues des réseaux 192.168.10.0/24 et 192.168.20.0/24

⚙️ **Directive Options**

Permet d'ajouter ou supprimer des comportements du serveur Apache.

📍 **Position possible :**

- 🛠️ Fichier de conf global (apache2.conf)
- 🌐 Bloc VirtualHost
- 📁 Bloc <Directory>

📐 **Syntaxe :** Options [+|-]option [[+|-]option]



🗂️ **Exemples d’options :**

| **🧾 Option** | **📌 Fonction** |
|----|----|
| All | Toutes les options sauf MultiViews |
| FollowSymLinks | 🔗 Autorise les liens symboliques |
| Indexes | 📂 Affiche la liste des fichiers si pas de page d'accueil |
| MultiViews | 🌐 Gère la compatibilité selon le navigateur (négociation de contenu) |
