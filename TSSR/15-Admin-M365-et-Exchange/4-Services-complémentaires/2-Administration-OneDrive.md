# Administration OneDrive-


## **🛠️ Centre d'administration OneDrive**

- Accès à une **console dédiée** pour gérer :
  - 🔗 *Fonctionnalités de partage*
  - 🔄 *Options de synchronisation*
  - 💾 *Stockage des données*



## **🔗 Paramétrage des liens de partage**

- Type de lien par défaut :  
  → Par défaut : **"Toute personne disposant du lien"** (anonyme, non authentifié)  
  → Risqué pour la confidentialité des données

- Il est recommandé de :
  - 🛑 **Restreindre les liens à des utilisateurs authentifiés**
  - 🧭 Cadrer les comportements des utilisateurs via des valeurs par défaut strictes
- ⚙️ Curseurs de sécurité disponibles pour limiter :
  - Le **type de partage autorisé**
  - Les **entités internes ou externes** autorisées à recevoir des fichiers



## **🔐 Gestion des partages externes**

- Possible de :
  - 🔒 Interdire aux utilisateurs externes de partager des documents qu’ils ne possèdent pas
  - 🧩 Renforcer les règles côté SharePoint et OneDrive de façon unifiée



## **🔄 Administration de la synchronisation**

- 💥 **Première synchronisation** : très gourmande en bande passante
- 🧠 **Synchronisation différentielle** ensuite : seuls les fichiers modifiés sont resynchronisés
- 🎯 Recommandation : **planifier les synchronisations** pendant les périodes creuses



## **🚫 Contrôle des fonctionnalités de synchronisation**

- Options disponibles :
  - Masquer le bouton **"Synchroniser"** dans l'interface Web
  - 🔐 **Restreindre la synchronisation** uniquement aux postes joints au domaine
  - 📁 Bloquer la synchronisation de certains fichiers par extension (ex. : .docx)


## **✅ Objectifs couverts**

- 🎛️ Configurer les options de partage, synchronisation et stockage
- 🛡️ Sécuriser les accès internes et externes
- 📶 Optimiser les performances réseau liées à la synchronisation
- 🌍 Restreindre l’accès par IP et par méthode d’authentification


## **🌐 Restrictions d'accès réseau**
- Configuration des plages d’adresses IP autorisées

→ Limiter l’accès à certaines IP ou sous-réseaux (ex. : IP publique entreprise)
- 🔑 Gestion de l’authentification :
  - Autoriser uniquement l'authentification moderne (plus sécurisée)
  - Ou bien tolérer l’authentification standard (moins recommandée)