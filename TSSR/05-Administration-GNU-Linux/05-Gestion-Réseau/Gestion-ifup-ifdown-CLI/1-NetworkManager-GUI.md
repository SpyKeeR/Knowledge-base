# NetworkManager - GUI

Quand tu as **un Linux avec une interface graphique** (comme GNOME), **NetworkManager** est **LE service** qui gère tout ton réseau.

👉 **Attention** : **NetworkManager** ≠ **networking.service**



## **🖥️ Comment on accède aux paramètres réseau ?**

➡️ **Icône réseau** en haut à droite (sur GNOME) → tu cliques sur ta carte réseau.

➡️ Tu peux configurer :

- **Adresse IP / Masque de sous-réseau / Passerelle (Gateway) / DNS**

💡 **Facile et rapide** via l’interface sans taper de commande.



## **⚙️ Et le suffixe DNS alors ?**

🙃 Mauvaise nouvelle : Sur **GNOME par défaut**, **pas possible de configurer graphiquement** le **suffixe DNS**.

👉 Il faudrait passer par une interface semi-graphique : nmtui (Network Manager Text User Interface).



## **🔧 Gérer NetworkManager en ligne de commande**

Parfois, tu devras **redémarrer** NetworkManager, par exemple après une modification manuelle.

**Commandes utiles :** sudo systemctl restart NetworkManager

Ou plus simple → déconnecter / reconnecter ta carte directement dans l'interface graphique.
