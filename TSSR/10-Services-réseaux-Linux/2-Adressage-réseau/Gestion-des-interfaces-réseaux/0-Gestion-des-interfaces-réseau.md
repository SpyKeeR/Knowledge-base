# Gestion des interfaces réseaux

## **🏷️ 1. Nommage des interfaces réseau**

Avant, on avait eth0, eth1… Maintenant, grâce à **systemd**, les noms sont **prédictibles** et liés au matériel détecté :

- ensX → interface attachée à une carte PCI embarquée
- enpXsY → interface basée sur l'emplacement physique PCI
- lo → interface loopback (toujours nommée ainsi)

✅ Ce nommage évite les confusions au boot (plus de changement d’ordre entre deux redémarrages).



## **⚙️ 2. Méthodes de configuration**

### **🔁 a. Dynamique avec ip**

- ⚡ **Immédiate** : prise en compte directe, non persistante après reboot
- 🔨 Sous-commandes : `addr` (adresses), `route`, `link` (activer/désactiver), etc.
- 📋 Affichage : `ip addr show`
- ➕ Ajout IP : `ip addr add 10.11.12.13/24 dev ens33`
- ➖ Suppression IP : `ip addr del 172.16.0.1/16 dev ens37`
- 🧹 Reset config : `ip addr flush ens33`
- ⬇️⬆️ Activation/désactivation : `ip link set ens33 down`, `ip link set ens37 up`

🧠 *Cette méthode est idéale pour tester des configs à chaud.*



### **🗂️ b. Statique via /etc/network/interfaces**

- 🧷 **Persistante**, relue à chaque boot
- 🖊️ Exemple IP fixe :
  ```bash
  auto ens33
  iface ens33 inet static
    address 192.168.66.6
    netmask 255.255.0.0
    gateway 192.168.66.1
  ```
- 🔁 Exemple DHCP :
   ```bash
  auto ens37
  iface ens37 inet dhcp
   ```
- 🔄 Redémarrage nécessaire : `systemctl restart networking`

🔎 *Simple, clair, mais pas compatible avec NetworkManager activé.*



### **🧠 c. Avec NetworkManager**

- 📦 Actif par défaut sur certaines distros (ex. Ubuntu Desktop, CentOS)
- 🔗 Ne pas mélanger avec /etc/network/interfaces → conflits
- 🧪 Vérifier état : `systemctl status NetworkManager`
- 📊 Affichage rapide : `nmcli`
- 🧬 Modifier IP : `nmcli connection modify "Wired connection 1" ipv4.addresses 192.168.1.100/24 ipv4.method manual`
- 🧩 GUI possible (avec `nmtui` ou interface graphique)

🎯 *Utilisé sur les postes utilisateurs ou si tu veux une gestion centralisée plus souple.*

