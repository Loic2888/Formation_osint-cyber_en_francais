# Rapport d'Audit OSINT et Reconnaissance Externe
**Cible :** Yahoo! Inc.
**Type d'audit :** Boîte Noire (Black Box) - Reconnaissance passive et active limitées.

---

## 1. Résumé Exécutif (Pour la Direction)

**Contexte et Objectif**
Cet audit de sécurité a été réalisé afin d'évaluer la surface d'attaque externe de l'infrastructure
 Yahoo! telle qu'elle est perçue par un attaquant potentiel sur Internet. L'objectif était d'identifier
  les actifs exposés et d'évaluer le risque d'une intrusion initiale.

**Constats Principaux**
L'analyse a révélé une exposition significative d'actifs dits "Shadow IT" (infrastructures oubliées ou de test)
 ainsi qu'une fuite d'informations sur l'architecture interne des bases de données de la division Finance. 
 Bien qu'aucune faille technique directe (zéro-day) n'ait été exploitée, la combinaison de ces expositions
  avec des données en sources ouvertes (OSINT) sur le personnel crée un vecteur d'attaque critique par 
  ingénierie sociale.

**Impact Métier et Risques (Business Impact)**
Si un attaquant (APT ou groupe cybercriminel) exploitait cette chaîne d'attaque (Hameçonnage ciblé de 
l'équipe Data couplé au contournement des sécurités standard), l'impact serait sévère :
* **Fuite de données (Data Breach) :** Accès non autorisé aux bases de données financières et de production.
* **Atteinte à la réputation :** Perte de confiance des utilisateurs et des partenaires institutionnels.
* **Risque légal et financier :** Amendes réglementaires liées à la non-protection des données sensibles 
et chute potentielle de la valorisation boursière.

---

## 2. Constats Techniques Détaillés (Technical Findings)

**Périmètre de l'analyse :** 36 000 sous-domaines identifiés.
**Vitesse de scan (Rate Limit) :** 50 requêtes/seconde pour garantir la disponibilité des services (SLA).

### Constat n°1 : Exposition critique d'interfaces d'administration
* **Niveau de risque :** Élevé 🔴
* **Description :** Des interfaces de gestion internes, qui devraient être confinées à un réseau privé (VPN), 
                    sont publiquement résolubles sur Internet. Cela expose l'infrastructure à des attaques de type "Brute-Force" 
                    ou "Credential Stuffing".
* **Preuve de concept (PoC) :**
  * Cible identifiée : `https://admin.nevec.yahoo.com`
  * Réponse serveur : Code HTTP `[302 Found]` confirmant la présence d'un portail de redirection actif utilisant `Apache Traffic Server`.

### Constat n°2 : Divulgation d'informations sur l'infrastructure (Information Disclosure)
* **Niveau de risque :** Moyen 🟠
* **Description :** L'énumération DNS a révélé la nomenclature interne des serveurs de la division Finance. 
                    Même si ces serveurs ne renvoient pas de page web, leurs noms exacts permettent à un attaquant de 
                    cartographier la topologie du réseau de production.
* **Preuve de concept (PoC) :**
  * Cible identifiée : `http://mysql-prod-us-east-mtls.finance.yahoo.com`
  * Déduction : Utilisation de bases de données MySQL en production, hébergées sur la région US-East, protégées par Mutual TLS (mTLS).

### Constat n°3 : Surface d'attaque par Ingénierie Sociale (OSINT)
* **Niveau de risque :** Critique (si combiné) 🔴
* **Description :** La combinaison de la fuite d'informations (Constat n°2) avec des recherches en sources ouvertes sur 
                    LinkedIn (via Google Dorks) a permis d'identifier le personnel technique en charge de ces infrastructures. 
                    Les conventions de nommage des adresses emails professionnelles sont facilement déductibles, ouvrant 
                    la voie à des campagnes de Spear-Phishing ultra-ciblées.
* **Preuve de concept (PoC) :**
  * Recherche "X-Ray" : Ingénieurs Data/Cloud pour Yahoo Finance.
  * Convention de nommage email identifiée : `{prenom}.{nom}@yahooinc.com`
  * Cible validée (Censurée pour confidentialité) : `m*******.j****@yahooinc.com` (Administrateur Base de Données).

---

## 3. Recommandations Techniques (Pour l'Équipe IT / Blue Team)

Pour atténuer les risques identifiés lors de la phase de reconnaissance et casser la chaîne d'attaque théorique, 
nous recommandons l'application immédiate des correctifs suivants :


**A. Réduction de la Surface d'Attaque (Shadow IT)**


* **Restreindre l'accès aux environnements critiques :** Les sous-domaines de développement 
(`dev.*`), de test (`stg.*`) et les interfaces d'administration (`admin.*`) ne doivent plus 
être résolus publiquement.

* **Mise en place du ZTNA (Zero Trust Network Access) :** Imposer l'utilisation d'un VPN 
d'entreprise ou d'une solution Zero Trust pour accéder à toute ressource interne (ex: `*.corp.yahoo.com`).

* **Masquage des erreurs :** Configurer les serveurs frontaux pour qu'ils abandonnent les requêtes 
(Drop) ou renvoient une page d'erreur générique plutôt que des redirections `[302]` ou des erreurs `[403]` 
qui confirment l'existence de la page aux attaquants.


**B. Assainissement des configurations DNS**


* **Nettoyage de la zone DNS publique :** Supprimer les entrées DNS publiques pointant vers des ressources 
strictement internes (comme `mysql-prod-us-east-mtls.finance.yahoo.com`). Même si l'accès est bloqué par 
le réseau, le nom lui-même divulgue la topologie de l'infrastructure de production à de potentiels attaquants.


**C. Défense contre l'Ingénierie Sociale et le vol de session (Evilginx2)**


* **Évolution du MFA (Multi-Factor Authentication) :** Le phishing moderne par Reverse Proxy peut contourner 
les codes SMS ou les applications de type Authenticator (TOTP). Il est impératif de migrer les accès des comptes 
à hauts privilèges (Administrateurs, Ingénieurs Data/Cloud) vers des **clés de sécurité physiques compatibles FIDO2/WebAuthn** 
(ex: YubiKey). Ces clés sont cryptographiquement liées au nom de domaine réel et rendent les attaques de 
type "Man-in-the-Middle" (comme Evilginx2) inopérantes.

* **Formation continue ciblée :** Sensibiliser spécifiquement les équipes d'administration système et bases 
de données aux attaques de Spear-Phishing ultra-ciblées basées sur leur profil LinkedIn.
