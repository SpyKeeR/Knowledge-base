# Gestion - Incidents


## 🎯 **1. Objectif de la pratique**

Le but de la gestion des incidents est **de rétablir le service normal le plus rapidement possible** ⚡

Même si ce n’est pas une solution définitive, **le but c’est que ça refonctionne vite**, sans impacter la qualité globale du service. 🧠 "Service normal" = tel qu’il est **défini dans le SLA**.



## ❗ **2. C’est quoi un incident ?**

Un **incident**, c’est une : **Interruption non planifiée** d’un service et/ou une **Réduction de la qualité** d’un service

💡 **Si ça empêche l’utilisateur de travailler comme prévu ➜ c’est un incident.**

👀 Exemples : 
- Le client ne peut plus envoyer d’e-mails 📭 
- L’ERP rame à mort 🚧
- Le wifi ne couvre plus une partie du bâtiment 📶



## 🔄 **3. Changement ≠ Incident : ne mélange pas les deux !**

| **Situation** | **Changement 🛠️** | **Incident 🚨** |
|----|----|----|
| Un salarié veut un écran plus grand | ✅ Oui (changement standard) | ❌ Non |
| La synchronisation des mails est lente | ❌ Non | ✅ Oui |
| Le lien Internet est coupé | ❌ Non | ✅ Oui |
| Le client veut doubler le débit Internet | ✅ Oui | ❌ Non |

🔁 **Règle d’or :** 
→ *Changement = c’est prévu, planifié ou demandé* 

→ *Incident = c’est une panne ou une baisse inattendue de qualité*



## 🔍 **4. Ne pas confondre : Incident / Évènement / Problème**

| **Terme** | **Définition** |
|----|----|
| **Évènement** | Quelque chose qui se passe dans le SI (signalé par un outil ou une alerte). Ex : Un disque RAID clignote 🔴 |
| **Incident** | L’évènement **impacte le service**. Ex : Le serveur web est lent, l’imprimante ne répond plus |
| **Problème** | L’**origine profonde** d’un ou plusieurs incidents. (Ce sera vu dans un autre chapitre) 🕵️ |



### 🎯 Un **évènement ne devient un incident que s’il gêne le fonctionnement du service**.

⚠️ **Cas concret RAID 5 :** 
- Un disque tombe en panne → **évènement détecté par la supervision,**
  - Le RAID continue à fonctionner → **pas d’incident,**
  - Si un **second disque** tombe avant remplacement → là, **incident majeur** 😱



## 🚀 **5. Comment l’incident est-il détecté ?**

Un incident peut être : 
- ⚙️ **Remonté automatiquement** via la supervision 
- 🙋‍♂️ **Signalé par un utilisateur**
- 🧠 **Identifié par les techniciens** lors de la gestion des évènements

