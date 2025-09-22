# Certificats autosignés

## **🛠️ Création d’un certificat autosigné**

- 🌐 Depuis la **configuration globale** d’IIS.
- 🔧 Accéder à la section **Certificats de serveur**.
- ➕ Cliquer sur **Créer un certificat autosigné**.
- 🏷️ **Nom du site** à saisir (ex : [www.tssr.lcl](http://www.tssr.lcl)).
- 📁 Choisir le **magasin de certificats** : *Hébergement Web*.



## **📄 Résultat de la création**

- ✅ Le certificat autosigné est **créé localement** et prêt à l’emploi.

### **🌍 Paramétrage HTTPS sur un site IIS**

1.  🌐 Aller dans le **Gestionnaire des services Internet**.
2.  🔗 Cliquer sur **Liaisons** du site concerné.
3.  ➕ Ajouter une liaison :

    - **Type** : `https`
    - **Port** : `443`
    - **Nom de l’hôte** : ex. `glpi.tssr.lcl`
    - **Certificat SSL** : choisir le certificat autosigné créé précédemment



## **🌐 Accès au site en HTTPS**

- 🌍 Le site devient accessible en **HTTP** et **HTTPS**.



## **🧰 Gestion de l’erreur de certificat côté client**

- 🚫 Le navigateur affiche une **erreur de certificat** (non reconnu par une autorité de certification/incohérence CommonName-FQDN).
- 👁️ Afficher les détails du certificat dans le navigateur.
- 🖱️ **Installer le certificat manuellement** :
  - 🧭 Lancer l'**assistant d'importation de certificat**.
  - 👤 Pour l’**utilisateur actuel**.
  - 🏛️ Importer dans le **Magasin de certification racine de confiance**.

## **🛡️ Qu’est-ce qu’un certificat ?**

Un certificat numérique est un document électronique qui joue un rôle crucial dans la sécurité des communications en ligne. Il est utilisé pour établir l'identité d'une entité (comme un site web, une organisation ou une personne) et pour sécuriser les échanges de données via des protocoles de chiffrement tels que SSL/TLS.

Un certificat contient généralement les informations suivantes :

- Un numéro de série unique
- L'autorité de certification émettrice
- La période de validité
- Le nom du titulaire de la clé publique
- La clé publique
- L'algorithme de chiffrement utilisé
- L'algorithme de signature



Ce protocole assure la vérification de l'identité des parties communicantes et confirme l'authenticité du serveur auquel on se connecte.
