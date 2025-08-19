# Single-user via GRUB**1️⃣ Pourquoi utiliser le mode Single ?**

- **Dépanner un système** après :
  - Une install ratée / Une mise à jour qui casse tout / Un mot de passe root oublié 🔒
- **GRUB** est souvent **notre seule chance** quand on n’a pas de clé USB ou de CD de secours.



**2️⃣ Accéder au menu GRUB**

➡️ Au démarrage :

- Appuie sur **Shift** ➔ si système **BIOS** 🔵 / Appuie sur **Échap** ➔ si système **UEFI** 🟣

**⚡ Astuce** : Appuie plusieurs fois dès que le PC s'allume pour être sûr de l'attraper !

**3️⃣ Modifier le démarrage depuis GRUB**

➡️ **Sélectionne ton kernel** : Utilise les flèches haut/bas ⬆️⬇️.

➡️ **Passe en mode édition** : Appuie sur **E**.

➡️ **Trouve la bonne ligne** : Cherche la ligne qui commence par linux et finit par quiet.

➡️ **Modifie-la** : Remplace quiet par single.

➡️ **Valide** : Appuie sur **CTRL+X** ou **F10** ➔ pour démarrer avec cette modif ⚡.



**4️⃣ Processus de démarrage en mode Single**

- Le système va démarrer en **mode maintenance** ➔ **Single User Mode**.
- **Demande du mot de passe root** ➔ obligatoire 🔑.



**5️⃣ ⚠️ Attention au clavier QWERTY !**

- En mode Single, le clavier est en **QWERTY (US)**.
- Donc, certaines touches ne sont pas à leur place habituelle !
- **Prépare une petite feuille** avec la correspondance AZERTY ➔ QWERTY pour éviter de galérer 📝.
