# Evolutions 6.0

## **💻 Passage à l’administration web**

- Jusqu’à **vSphere 5.x**, l'administration se faisait via le **client lourd (vSphere Client)** installé localement.
- Depuis **vSphere 6.0**, une **interface web** devient disponible pour gérer vSphere :
  - Hébergée directement sur **l’hyperviseur** ou sur **le serveur vCenter**.
  - Permet une **gestion centralisée** et plus accessible de l’infrastructure.



## **🧱 vCenter en mode Appliance**

- **Nouvelle possibilité** introduite avec vSphere 6.0 :  
  vCenter peut désormais être déployé en **mode Appliance virtuelle** (VCSA – vCenter Server Appliance).

- 🟢 **Avantages** :
  - Moins gourmand en ressources.
  - Peut être déployé comme une **VM au sein d’un hyperviseur**.
- 🔴 **Limites** :
  - Ne supporte pas un nombre **illimité de VM ou d’hôtes**, contrairement à la version installée sur OS.



## **🌐 Évolution du Web Client**

- 🔁 **vSphere 6.0** : le client web nécessite **Adobe Flash Player** ❌ (technologie en fin de vie).
- ✅ **vSphere 6.5** : passage à une interface en **HTML5**, beaucoup plus moderne, fluide et compatible avec tous les navigateurs actuels.



## **⚠️ Changements majeurs apportés à la plateforme**

- ❌ **Suppression de l’ESX classique** et de son **Service Console**.
- ❌ **Fin du vSphere Client lourd** (client Windows).
- 🆕 **Introduction du Web Client vSphere**.
- 🧩 **vCenter disponible uniquement sous forme d’Appliance virtuelle (VCSA)**.

