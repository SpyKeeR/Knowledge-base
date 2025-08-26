# Via Interface simplifiéeL’interface simplifiée est pensée pour : Être claire, intuitive, **Accompagner les utilisateurs dans la création de tickets** sans les noyer dans les détails techniques, Rendre certains champs invisibles ou automatisés (via gabarits)

**🔐 Accès**

- Accessible uniquement **après authentification**
- L’utilisateur doit avoir le **profil “Self-Service”** dans GLPI



**📄 Formulaire à remplir**

| **👤 Utilisateur concerné** | Peut être soi-même ou un autre utilisateur (si délégation activée) |
|----|----|
| **📷 Documents joints** | Possible d’ajouter des captures d’écran, manuels, etc. |
| **✉️ Suivi par mail** | Option d’être informé de l’évolution du ticket si le serveur mail est configuré |
| **📦 CI concernés** | Sélection de l’équipement ou logiciel impacté |
| **📍 Lieu** | Où se trouve le matériel concerné |
| **👁️ Observateurs** | Autres personnes à informer du ticket |
| **📝 Titre** | Court résumé de la demande |
| **💬 Description** | Détail du problème ou de la demande. Ce champ peut être **prérempli** selon le gabarit sélectionné |
| **⚠️ Urgence** | Peut être masquée et gérée automatiquement par le SLA |
| **📂 Catégorie** | Obligatoire. Sert à définir **le gabarit à utiliser**, ce qui peut **changer dynamiquement les champs affichés** |
| **📌 Type** | Choix entre **Incident** ou **Demande** |

**🧠 Fonctionnement du gabarit de ticket**

Le gabarit (template) permet de : Guider l’utilisateur (description préremplie, titres prédéfinis), Cacher les champs inutiles, Préconfigurer certaines options (lieu, urgence, observateurs, etc.) C’est **grâce au gabarit** que l’interface reste simple, mais adaptable à différents types de demandes.



**🏠 Accueil utilisateur**

Après création d’un ticket, l’utilisateur peut : Voir **l’historique de tous ses tickets,** Suivre leur avancement, Recevoir des **notifications par mail** si configuré



**🧭 Étapes de création (récap)**

1.  Connexion à GLPI avec un compte Self-Service
2.  Aller dans **« Créer un ticket »**
3.  Choisir **le type** (Incident ou Demande)
4.  Sélectionner une **catégorie**
5.  Renseigner les champs affichés (guidé par le gabarit)
6.  Joindre des documents si besoin
7.  Valider
