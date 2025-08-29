## **🛠️ 1. Ajout du Poller (collecteur) dans l’interface**

- Menu **Configuration > Pollers**
- Bouton ➕ > **Add a Centreon Poller**
- Renseigner les infos dans l’assistant (nom, IP, OS, version…)
- Le poller apparaît ensuite dans la liste avec son statut ⬇️



## **🔌 2. Activation de la communication Central ↔️ Poller**

Cette étape est cruciale pour la **synchronisation des configs via Gorgone** (protocole ZMQ sur port 5556).

### **🧩 2.1 Côté Central**

- Aller dans **Configuration > Pollers**
- Dans la colonne *Actions*, cliquer sur l’icône 🧠 Gorgone (📋)
- Une fenêtre s’ouvre → cliquer sur **Copy to clipboard**
- Le contenu YAML de config Gorgone est copié pour être envoyé au Poller



### **📥 2.2 Côté Poller**

- Ouvrir un terminal SSH sur le poller
- Coller directement le contenu du presse-papiers dans le shell → génère le fichier /etc/centreon-gorgone/config.d/40-gorgoned.yaml
- Redémarrer le service : `systemctl stop gorgoned && systemctl start gorgoned`
- Vérifier son statut avec : `systemctl status gorgoned`
- L’activer au démarrage : `systemctl enable gorgoned`

📌 Le process gorgoned doit tourner avec les modules : gorgone-dbcleaner, gorgone-engine, gorgone-action



## **🔁 3. Synchronisation & dépannage**

Une fois Gorgone actif, il faut **exporter la configuration** depuis le Central vers le poller :

- Depuis l’interface Centreon-Central : Export des fichiers + Restart des moteurs
- En cas d’échec, tenter :

  1.  Redémarrer services centengine, centreon, gorgoned sur le poller
  2.  Réexporter les configs depuis le Central
  3.  Ultime recours : redémarrage complet des machines Central + Poller

🔍 Une fois OK, le poller devrait apparaître avec un état actif (vérifiable dans la colonne *Engine status* ou par des logs).

