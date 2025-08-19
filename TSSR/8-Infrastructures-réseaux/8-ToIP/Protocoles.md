# Protocoles**🎬📶 Le déroulement d’un appel TOIP : 2 phases clés**

1.  **Signalisation** → protocole **SIP** : en charge de l’établissement, routage, vérif du numéro, état de la ligne
2.  **Transport de la voix** → protocoles **RTP/RTCP** : en charge de l'audio après mise en relation

🛑 SIP reprend la main à la fin pour clôturer la session et **arrêter la taxation**.



**🧠⚙️ SIP : le cerveau de la signalisation**

- Objectifs : **établir / maintenir / libérer** une session multimédia
- Basé sur **ASCII**, proche de HTTP
- Ports : **UDP 5060** (standard), **TLS 5061** (SIPS sécurisé)
- Fonctionne avec **User Agents**, **Proxy SIP**, **Registrar**
- Gestion : **localisation**, **disponibilité**, **type de média**, **choix des codecs**

📍Dans le modèle OSI :

- **Applicatif** : services de téléphonie (ex. IPBX)
- **Session** : gestion SIP/SIPS
- **Présentation** : encodage (G711, G729)
- **Transport** : SCTP, TLS, RTP, RTCP, SRTP



**📤📥 SIP : actions et messages**

- **Requêtes SIP** : Register, Invite, Reinvite, Ack, Cancel, Bye
- **Réponses SIP** : info (1xx), succès (2xx), redirection (3xx), erreur client (4xx), erreur serveur (5xx), échec global (6xx)
- **SDP** : utilisé dans SIP pour **négocier les flux multimédias**

👁️ Exemple : SIP envoie une **Invite**, reçoit un 180 Ringing (l’appel sonne), puis 200 OK si l’appel est décroché.



**🗣️🎧 RTP/RTCP : transport du flux vocal**

- **RTP** : transporte la voix/vidéo
- **RTCP** : contrôle, synchronisation, feedback
- **SRTP** : version chiffrée (sécurité du flux)

🧩 Hors de la phase SIP, **RTP entre en jeu dès que la conversation démarre**.



**🎛️🧪 Les codecs : qualité audio & MOS**

- **Codecs principaux** : G711 (qualité élevée, 64 kbps), G729 (compressé, 8 kbps)
- Fonctionnement : encode/décode la voix via **DSP**
- **MOS (Mean Opinion Score)** : de 1 (mauvais) à 5 (excellent), permet d’évaluer la qualité vocale d’un codec

🎯 Le choix du codec dépend de la **bande passante** dispo et du **niveau de qualité** attendu.
