# HabilitationsElles lient **un utilisateur à un ou plusieurs profils**, ce qui détermine : **Ce qu’il peut faire (droits) / Où il peut le faire (entité/filiale)**

⚠️ Important : un même utilisateur peut avoir **plusieurs profils** et des droits différents **selon la filiale ou entité** dans laquelle il travaille.



**🧱 Composition d’une habilitation**

Une habilitation est constituée de :

- **Un profil utilisateur** : définit les droits fonctionnels.
- **Une entité** : délimite le périmètre d’action (par exemple une filiale).
- **Une option de récursivité** : indique si les droits s’appliquent aussi aux sous-entités (ex : filiales enfants).

🔁 Exemple : Dans la **filiale A**, tu peux être "utilisateur simple" / Dans la **filiale B**, tu peux être "technicien de maintenance"



**🧍‍♂️ Attribution des habilitations**

| **Statique** | Habilitation ajoutée manuellement pour chaque utilisateur. | Gestion directe sans système externe |
|----|----|----|
| **Dynamique** | Attribution automatique basée sur des règles (ex : groupe AD). | Quand l’authentification passe par Active Directory |
|  |  |  |

**🛠️ Attribution statique : à la main**

1.  Aller dans **l’édition du compte utilisateur**
2.  Ouvrir l’onglet **"Habilitations"**
3.  Sélectionner : L’entité concernée, Le profil à attribuer, Si l’habilitation est récursive ou non
4.  Cliquer sur **"Ajouter"**

Tu peux répéter l’opération pour ajouter plusieurs habilitations sur différentes entités ou avec d'autres profils.



**⚙️ Attribution dynamique : via un moteur de règles**

Le moteur de règles permet d’attribuer automatiquement des habilitations, selon :

- **Des critères** : ex. appartenance à un groupe AD / **Des actions** : ex. assigner tel profil à telle entité

💡 Exemple : Tous les utilisateurs membres du groupe "Techniciens" dans Active Directory reçoivent automatiquement le profil "Technicien" dans GPI.

**🔄 Fonctionnement :** Les règles sont **lues à chaque connexion utilisateur /** Chaque règle est indépendante (évite les règles trop complexes) / Une règle = **un objectif clair**



**🧠 Créer une bonne règle**

Une règle doit contenir : **Un nom clair** : utile pour la maintenance / **Des critères** (conditions d’application) / **Une ou plusieurs actions**

**🎯 Exemples de critères :** Appartenance à un groupe AD (memberOf) / Valeur d’un attribut dans l’annuaire / Contenu d’un champ (avec opérateurs logiques : **ET**, **OU**)

🔍 ⚠️ Attention : L’opérateur **"contient"** est plus souple mais moins strict que **"est égal à" /** Il peut y avoir des erreurs de casse ou de syntaxe → faire des tests



**✅ Exemples d’actions possibles**

Les actions les plus fréquentes sont : Affecter un **profil /** Affecter une **entité /** Définir la **récursivité /** Bloquer un utilisateur s’il ne remplit pas les critères
