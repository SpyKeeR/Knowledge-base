# Protocole SSL/TLS| Années actives | 1994 → 1996 (puis déprécié) | 1999 → aujourd’hui |
| Sécurité | ❌ Obsolète, vulnérable | ✅ Moderne, sécurisé |
| Support navigateur | ❌ Plus reconnu | ✅ Supporté par défaut |
| Usage recommandé | ❌ À éviter | ✅ À **prioriser absolument** |
| Cas d’usage typique | Ancienne sécurité web | Transactions bancaires, intranet |

vendredi 25 juillet 2025

09:08

📜 **📅 Historique des protocoles**

**🔁 SSL – *Secure Socket Layer***

- 🕰️ **1994** : SSL 1.0 *(jamais publié)*
- 🔁 **1995** : SSL 2.0
- 🔁 **1996** : SSL 3.0
- 📡 **Transfert de responsabilité** : géré ensuite par l’**IETF**

**🔐 TLS – *Transport Layer Security***

- ✅ **1999** : TLS 1.0 *(très proche de SSL 3.0)*
- ⚙️ **2006** : TLS 1.1
- 🚀 **2008** : TLS 1.2 *(encore utilisé aujourd’hui)*
- 🛡️ **2018** : TLS 1.3 *(version la plus récente)*



⚙️ **🎯 Rôles du protocole TLS**

TLS assure **trois fonctions essentielles** à la sécurité d’une communication :

🧾 **Authentification** → Vérifie l'identité du serveur (via un **certificat numérique**), et dans certains cas le client.

🕶️ **Confidentialité** → Chiffre les données échangées (ex : numéro de carte bancaire)

🧬 **Intégrité** → Garantit que les données **n'ont pas été modifiées** pendant le transport (via un **algorithme de hachage**)



📌 **🛑 SSL : un protocole dépassé**

- ⚠️ **Déprécié depuis 20 ans**
- ❗️ Vulnérable aux attaques modernes
- 🔓 Peut être **facilement exploité** par des acteurs malveillants
- 🚫 Ne pas utiliser pour des **transactions sensibles**



✅ **🔐 TLS : la norme actuelle**

- 🔍 **Corrige les failles** des anciennes versions SSL
- 🔄 Évolue avec les besoins des entreprises et utilisateurs
- 🧱 Assure un niveau de **sécurité robuste et fiable**
- 🎯 À privilégier pour tous les usages web sécurisés (HTTPS, VPN, intranet…)

**Évolutions Récentes et Bonnes Pratiques**

**SSL** est considéré comme **obsolète et non sécurisé**. L'utilisation de TLS est fortement recommandée.

**TLS 1.3 apporte des améliorations significatives :**

- [x] Réduction du temps de handshake (poignée de main), améliorant la performance.
- [x] Simplification du processus de négociation, réduisant la surface d'attaque.
- [x] Renforcement de la confidentialité avec le chiffrement précoce des données.



**SSL/TLS utilise une combinaison sophistiquée de techniques cryptographiques :**

- **✓ Cryptographie Asymétrique(ou à clé publique)** : Utilisée principalement pour l'échange sécurisé de clés et l'authentification.
- **✓ Cryptographie Symétrique :** Employée pour le chiffrement rapide des données de la session.



**Importance et Applications**

- **✓ HTTPS :** SSL/TLS est la base du protocole HTTPS (443), essentiel pour la sécurisation des sites web.
- **✓ Sécurisation des emails et des annuaires** : Utilisé dans les protocoles comme SMTPS (465), IMAPS (993) et LDAPS (port 636).
- **✓ VPN :** Certaines solutions VPN utilisent SSL/TLS pour sécuriser les connexions.
