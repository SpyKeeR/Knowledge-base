# **🧾 Vocabulaire fondamental à connaître**

## **💻 MUA – *Mail User Agent***

- C’est **le client de messagerie utilisé par l'utilisateur final**.
- Exemples : **Outlook**, **Thunderbird**, **Gmail (web)**, **applications mobiles**…
- Il permet de **composer, envoyer et recevoir** des mails, mais **ne transmet pas directement les mails au destinataire**.



## **📤 MSA – *Mail Submission Agent***

- C’est **l’intermédiaire entre le client (MUA) et le serveur de transfert (MTA)**.
- Son rôle est :
  - **D’identifier l’émetteur**.
  - **De contrôler le message** (authentification, vérification des règles, sécurité…).
  - **De transmettre le message au MTA** via le protocole **SMTP**.



## **🚚 MTA – *Mail Transfer Agent***

- C’est **le "transporteur" du mail sur Internet**.
- Il **reçoit le mail du MSA**, puis :
  - Soit **le transfère à un autre MTA** s’il faut traverser plusieurs serveurs.
  - Soit **le remet au MDA** une fois arrivé sur le serveur du destinataire.
- Le MTA **n’interagit pas directement avec les utilisateurs**.



## **📥 MDA – *Mail Delivery Agent***

- C’est **l’agent de distribution final**.
- Il **stocke les messages dans la boîte aux lettres** de l’utilisateur concerné.
- Il agit **à la fin du parcours**, juste avant la récupération du message par le MUA du destinataire.

## **🔄 Résumé du parcours complet d’un mail interne (ex : Jean-Pierre → Micheline)**

1.  🧑‍💻 **Jean-Pierre utilise Outlook (MUA)** pour rédiger un message à Micheline.
2.  📤 **Le MUA passe le message au MSA** de son serveur Exchange.
3.  🚚 **Le MSA remet le mail au MTA**, qui va gérer le transport du mail au sein du domaine (ex : crepes.lcl) ou vers l'extérieur.
4.  📬 **Le MTA transmet le message au MDA** une fois arrivé sur le serveur destinataire.
5.  🗃️ **Le MDA dépose le mail dans la boîte aux lettres** de Micheline.
6.  📱 **Micheline utilise son client Outlook (MUA)** pour récupérer le message via **POP3, IMAP4, ActiveSync, MAPI/HTTPS**, etc.

