# Extensions.conf / IVR

## 📂📞 **À quoi sert le fichier extensions.conf ?**

Ce fichier est **le cœur du routage des appels dans Asterisk/XiVO**. Il décrit *quoi faire quand un appel arrive*, en fonction du **contexte** de l’appel (ex: interne, externe, standard, IVR…). 

➡️ Chaque appel passe dans un **contexte**, et chaque contexte contient des **instructions** à exécuter étape par étape (priorités).

## 🔧🗂️ **Structure du fichier extensions.conf**

📌 Organisation :

• `[contexte]` → nom du bloc (ex: `[local]`, `[ivr-menu]`, `[default]`)

• `exten =>` → règle de traitement (numéro, priorité, action)

• `same => n`, → suite d’étapes sans répéter l’extension

📍 Exemple de traitement : • 
```console
[local]
exten => 200,1,Wait(10)
same => n,Dial(SIP/John)
```



## 📞🎛️ **IVR (Interactive Voice Response)**

C’est un **standard vocal interactif**, type : "Tapez 1 pour le support, 2 pour la compta…"

➡️ Programmé directement dans extensions.conf en créant un contexte avec des actions `Background()`, `Goto()` et écoute d'entrées DTMF.

🔥 L'IVR dirige ensuite les appels vers d'autres contextes, files ou utilisateurs selon le choix de l'appelant.



## ⚙️🎚️ **Actions standards (commandes fréquentes)**

• `Answer()` : décroche l’appel

• `Hangup()` : raccroche

• `Playback(son)` : joue un son sans interaction

• `Background(son)` : joue un son + attend une touche

• `Goto(contexte,extension,priorité)` : redirige l’appel

• `Queue(nom|options)` : envoie vers une file d’attente (définie dans queues.conf)

• `Voicemail(ext)` / `VoicemailMain()` : boîte vocale / consultation

• `Directory()` : annuaire basé sur voicemail.conf

• `MusicOnHold()` : musique d’attente

• `Echo()` : test d’écho audio



## 📐📈 **Les modèles d’extensions (patterns dynamiques)**

Permettent d’agir selon des **plages de numéros**.

• `_X.` : n’importe quel chiffre, 1 ou plusieurs

• `_Z.` : chiffre ≠ 0

• `_N.` : 2 à 9

• `_1[5-7].` : commence par 1, suivi de 5 à 7

• `.` : wildcard (fin) → 1+ caractères

• `!` : wildcard total → 0+ caractères

### 🔁🧠 **Variables utiles dans les traitements**

• `${EXTEN}` : numéro composé

• `${CALLERID}` : ID de l’appelant

• `${CONTEXT}` : contexte actuel

• `${PRIORITY}` : étape en cours

• `${CALLEDID(NUM)}` : numéro du destinataire



## ❗🔀 **Cas spéciaux de gestion d’appels**

• `exten => s,1,Answer()` : entrée directe (sans DTMF)

• `exten => i,1,Playback(sounerror)` : entrée invalide

• `exten => h,1,Hangup()` : raccroche auto (fin d’appel)

• `t`, `T` : gestion des timeout → `t` pour délai utilisateur, `T` pour durée max d’appel


