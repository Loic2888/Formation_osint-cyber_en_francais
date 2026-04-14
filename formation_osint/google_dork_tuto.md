# Formation OSINT : Maîtriser les Google Dorks (L'art de fouiller le Web)

**Objectif du cours :** Apprendre à utiliser les opérateurs de recherche avancés de Google
 (les "Dorks") pour forcer le moteur de recherche à révéler des informations cachées, des profils précis, 
 ou des documents confidentiels non sécurisés.

**Avertissement Éthique :** Trouver des documents exposés par erreur (comme des listes de mots de 
passe ou des bases de données) n'est pas illégal en soi car c'est le serveur cible qui est mal configuré 
et Google qui le rend public. En revanche, *télécharger et exploiter* ces données privées l'est totalement. 
Regardez, mais ne touchez pas.

---

### L'Entonnoir du Google Dorking

Google indexe des dizaines de milliards de pages. Si vous tapez une simple phrase, l'algorithme vous 
donne ce qu'il *pense* que vous voulez voir. Un Dork permet de reprendre le contrôle et de **filtrer le bruit**.

Voici les "Mots de Pouvoir" (Opérateurs) à retenir et à combiner :
* `site:` : Restreint la recherche à un nom de domaine précis.
* `filetype:` ou `ext:` : Cherche un format de fichier exact (PDF, DOCX, TXT).
* `intitle:` : Le mot-clé doit obligatoirement se trouver dans le titre de l'onglet de la page.
* `intext:` : Le mot-clé doit se trouver dans le corps du texte.
* `""` (Guillemets) : Recherche l'expression *exacte*, sans synonyme ni correction orthographique.
* `-` (Tiret) : Exclut un mot des résultats (ex: `-www` pour enlever le site principal).

---

### Cas Pratique 1 : Cibler une personne et ses profils

Ne tapez jamais juste un nom et prénom. Forcez Google à chercher dans les annuaires des réseaux sociaux.

**1. Trouver le profil LinkedIn exact :**
```bash
site:[linkedin.com/in/](https://linkedin.com/in/) "Prénom Nom" "Ville"
```

*Pourquoi ça marche ?* 
Le /in/ dans l'URL garantit que vous cherchez un profil d'utilisateur et non une offre d'emploi ou une page entreprise.

**2. Trouver les autres réseaux sociaux liés à un pseudo :**

```bash
"pseudo_cible" site:twitter.com OR site:instagram.com OR site:tiktok.com
```
L'astuce : L'opérateur OR (toujours en majuscules) permet de demander à Google de chercher 
sur Twitter OU Insta OU TikTok en une seule requête.

### Cas Pratique 2 : Débusquer des Emails et Numéros de Téléphone

Les cibles laissent souvent traîner leurs coordonnées sur des documents professionnels 
qui ont été indexés par erreur.

**1. Trouver le CV d'une cible (La mine d'or) :**

```bash
"Prénom Nom" filetype:pdf (CV OR Curriculum)
```
L'astuce : Un CV au format PDF non protégé contient presque toujours le numéro de mobile, 
l'email personnel, la date de naissance et l'adresse physique de la cible !

**2. Chercher des emails professionnels spécifiques (Reconnaissance) :**

```bash
intext:"@yahooinc.com" site:yahoo.com -www
```
*Pourquoi ça marche ?* 
On cherche le format de l'adresse email sur le domaine de l'entreprise (site:yahoo.com), 
mais on exclut (-www) le site vitrine principal. Google va alors fouiller les sous-domaines obscurs 
où les employés laissent parfois leurs coordonnées.

### Cas Pratique 3 : Retrouver un post ou un message précis

Vous savez qu'une personne a dit quelque chose de compromettant, mais vous ne le 
trouvez plus sur son profil ? Google le garde souvent en mémoire !

**1. Fouiller uniquement les posts LinkedIn :**

```bash
site:[linkedin.com/posts/](https://linkedin.com/posts/) "Prénom Nom" "mot clé du message"
```
L'astuce : Les posts LinkedIn sont indexés dans un sous-dossier /posts/ (ou /pulse/ pour les articles). 
C'est parfait pour retrouver une publication que la cible a récemment effacée, en utilisant la 
version "En cache" de Google.

**2. Chercher dans des forums spécifiques (ex: Reddit) :**

```bash
site:[reddit.com/r/cybersecurity](https://reddit.com/r/cybersecurity) "OSINT" "Outils"
```
L'astuce : Le /r/ cible un sous-forum (Subreddit) précis, éliminant tout le reste du site.

### Cas Pratique 4 : Trouver l'Introuvable (Serveurs mal configurés)

C'est la technique préférée des Hackers et des professionnels en Bug Bounty pour trouver 
des données fuiteés sans aucun outil spécialisé.

**1. Trouver des répertoires ouverts (Directory Listing) :**

```bash
intitle:"index of" "contacts" (xls OR csv OR txt)
```
Le Résultat : Google vous affichera des serveurs qui n'ont pas de page d'accueil web 
et qui listent leurs fichiers nus publiquement. Ce dork cible spécifiquement les répertoires 
contenant des mots comme "contacts" sous forme de tableur ou texte.

**2. Chercher des fichiers de mots de passe de serveurs :**

```bash
filetype:env "DB_PASSWORD"
```
L'explication : Les fichiers .env sont utilisés par les développeurs pour stocker les clés secrètes et 
les mots de passe des bases de données de leurs applications. S'ils les mettent en ligne par erreur sans 
les protéger, Google les lit et les indexe !
