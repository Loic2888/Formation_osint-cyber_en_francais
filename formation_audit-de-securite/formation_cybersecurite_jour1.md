# Formation Cybersécurité — Jour 1

## Module 1 : La méthode de l'Entonnoir (Cartographie et Filtrage)

> **Objectif du cours :** Apprendre à partir d'un simple nom d'entreprise (Yahoo!), extraire des dizaines de milliers de sous-domaines, puis utiliser des filtres successifs pour isoler les failles critiques. C'est ce qu'on appelle la **méthode de l'entonnoir**.

---

## Étape 1 : Préparation du Laboratoire (L'Environnement)

Un bon ingénieur ne travaille qu'avec des outils parfaitement à jour. Sur votre environnement Linux (ou WSL), nous devons préparer notre arsenal.

### 1. Mise à jour des fondations

Avant d'installer quoi que ce soit, on s'assure que notre système connaît les dernières versions des logiciels.

```bash
sudo apt update
```

### 2. Installation du moteur (Le langage Go)

Pour traiter des dizaines de milliers de données sans faire planter l'ordinateur, nous installons le langage Go, conçu pour le multi-threading.

```bash
sudo apt install golang-go -y
```

### 3. Installation des sondes de reconnaissance

Nous installons les deux outils de ProjectDiscovery qui vont faire le travail.

```bash
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
```

---

## Étape 2 : Le haut de l'entonnoir (Trouver les 36 000 cibles)

La toute première action de notre attaque simulée consiste à lister tous les serveurs liés à Yahoo. Nous utilisons **subfinder**.

> **Note technique :** Selon la configuration de votre Linux, la commande directe `subfinder` fonctionnera très bien. Si le terminal renvoie `command not found`, il suffira d'utiliser le chemin complet `~/go/bin/subfinder`.

### La commande d'énumération brute

```bash
subfinder -d yahoo.com -o subdomains.txt
```

**Explication :** L'outil cherche partout sur le web des traces du domaine (`-d yahoo.com`) et enregistre tout dans un fichier de sortie (`-o subdomains.txt`).

**Résultat :** Lors de notre exercice, vous avez obtenu un fichier colossal contenant environ **36 000 sous-domaines**. À ce stade, c'est juste une liste de noms de texte ; on ne sait pas si ces ordinateurs sont allumés, éteints, ou sécurisés.

---

## Étape 3 : Le milieu de l'entonnoir (Trier l'actif de l'inactif avec httpx)

Tester 36 000 serveurs à la main dans un navigateur prendrait des années. Nous devons automatiser le tri pour savoir lesquels de ces serveurs sont "vivants" et ce qu'ils font. C'est ici qu'intervient **httpx**.

### La commande de tri massif

```bash
cat subdomains.txt | ~/go/bin/httpx -title -tech-detect -status-code -rl 50 -o active_subs.txt
```

### Décryptage de la commande

| Fragment | Rôle |
|---|---|
| `cat subdomains.txt` | Ouvre le fichier de nos 36 000 cibles |
| `\|` | Le *pipe* — tuyau qui envoie les 36 000 lignes dans l'outil suivant |
| `httpx` | L'outil qui va toquer à la porte de chaque serveur |
| `-title` | Demande le titre de la page web |
| `-tech-detect` | Devine les technologies utilisées (PHP, Node.js, Apache…) |
| `-status-code` | Demande l'état du serveur (`200` OK, `403` Interdit…) |
| `-rl 50` | *Rate Limit* — se limite à 50 requêtes/seconde pour ne pas alerter Yahoo |
| `-o active_subs.txt` | Enregistre uniquement les serveurs qui ont répondu |

> **Note :** On utilise `~/go/bin/httpx` car l'installation directe via `apt` pose souvent des conflits de version sur WSL.

**Résultat :** L'entonnoir se resserre. Vous passez de **36 000 lignes** à une liste plus courte de serveurs certifiés actifs avec leur fiche d'identité technique.

---

## Étape 4 : Le bas de l'entonnoir (Trouver les pépites avec Grep)

Maintenant que vous avez votre fichier `active_subs.txt`, il est hors de question de tout lire. Nous allons utiliser `grep` pour chercher des mots-clés spécifiques à l'intérieur du fichier.

C'est **l'étape de l'analyste** : on cherche ce qui ne devrait pas être là — les interfaces d'administration (`admin`), les serveurs de test (`dev`), ou les bases de données (`sql`).

---

### 1. Chercher les portes dérobées d'administration

```bash
cat active_subs.txt | grep -i "admin"
```

> L'option `-i` signifie *ignore-case* : il trouvera `Admin`, `ADMIN` ou `admin`.

**Résultat obtenu :**
```
https://admin.nevec.yahoo.com [302]
```

⚠️ **Pourquoi c'est grave ?** Une interface admin ne devrait pas être visible publiquement. C'est une cible parfaite pour des attaques par **force brute** ou du **credential stuffing** (bourrage d'identifiants).

---

### 2. Chercher les serveurs de test oubliés

```bash
cat active_subs.txt | grep -i "dev"
```

**Résultat obtenu :**
```
https://developer.yahoo.com [200] [Node.js, Express, jQuery]
```

⚠️ **Pourquoi c'est grave ?** Les environnements de développement sont souvent moins mis à jour et moins sécurisés. Un serveur `[200 OK]` avec de multiples technologies est une surface d'attaque rêvée.

---

### 3. Chercher l'infrastructure de données

```bash
cat active_subs.txt | grep -i "sql"
```

**Résultat obtenu :**
```
http://mysql-prod-us-east-mtls.finance.yahoo.com [404]
```

⚠️ **Pourquoi c'est grave ?** Même si la page affiche une erreur `404`, le nom de domaine révèle l'architecture interne : on sait qu'ils utilisent **MySQL en production**, sur la **côte Est des USA**, pour la division **Finance**.

---

## Bilan de l'opération : De la donnée brute à la faille stratégique

La méthode de l'entonnoir a permis de transformer un nom de domaine public (`yahoo.com`) en une cartographie précise des faiblesses de l'entreprise.

### Faille n°1 — Exposition des interfaces d'administration

**Trouvaille :** `https://admin.nevec.yahoo.com [302]`

Une interface d'administration est la salle des commandes d'une application. Elle ne devrait **jamais** être accessible depuis Internet — uniquement depuis un VPN ou un réseau privé. Sa visibilité publique ouvre la porte aux attaques par **force brute** et au **credential stuffing**.

---

### Faille n°2 — Shadow IT et environnements de développement oubliés

**Trouvaille :** `https://developer.yahoo.com [200]` et sous-domaines `.corp` (ex: `dev.gsdapps.corp.yahoo.com`)

Les serveurs `dev` et `test` sont le **talon d'Achille** des grandes entreprises. Souvent oubliés lors des audits, ils n'ont pas les mises à jour critiques. Un attaquant les cible pour **rebondir vers le réseau principal**.

---

### Faille n°3 — Fuite d'informations sur l'infrastructure interne

**Trouvaille :** `http://mysql-prod-us-east-mtls.finance.yahoo.com [404]`

C'est ce qu'on appelle la **divulgation d'informations** (*Information Disclosure*). Le nom de domaine seul révèle : technologie (`MySQL`), environnement (`prod`), localisation (`us-east`), et division métier (`finance`). Une mine d'or pour préparer une attaque ciblée.

---

## Transition vers le Jour 2 : L'OSINT Humain

Aujourd'hui, nous avons cartographié l'**infrastructure technique** : adresses, portes dérobées et technologies.

Mais une machine ne se configure pas toute seule. Derrière `admin.nevec.yahoo.com` ou la base de données `mysql-prod`, il y a des **ingénieurs**, des **administrateurs système** et des **développeurs**.

Si aucune faille technique exploitable (zéro-day) n'est trouvée, la voie la plus efficace est de **pirater l'humain** qui possède les clés. C'est l'**Ingénierie Sociale**.

> **Jour 2 :** Nous abandonnerons le terminal Linux pour les réseaux sociaux (notamment LinkedIn). L'objectif sera de relier les machines découvertes aux employés qui les gèrent, afin de préparer un scénario de **Spear-Phishing**.
