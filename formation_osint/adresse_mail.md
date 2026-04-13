# Formation OSINT : Enquête sur une Adresse Email (Email OSINT)

**Objectif du cours :** Apprendre la méthodologie et utiliser les outils en ligne de commande pour extraire
 l'empreinte numérique complète d'une adresse email : comptes associés, identité réelle, 
 habitudes géographiques et mots de passe.

**🛑 Avertissement Éthique et Légal :** Ces techniques sont documentées à des fins d'enquête 
légitime (cybersécurité, recherche de menaces). L'utilisation de ces méthodes pour intimider, harceler 
ou usurper l'identité d'une personne est strictement interdite et punie par la loi.

---

### L'Entonnoir de l'Email OSINT
Une adresse email n'est pas qu'un moyen de communication, c'est un identifiant de connexion universel. 
Notre enquête va suivre 4 étapes :
1. **L'Empreinte Sociale :** Sur quels sites cette adresse est-elle inscrite ?
2. **L'Écosystème Google :** Que cache spécifiquement une adresse `@gmail.com` ?
3. **Le Pivot du Pseudo :** Comment utiliser la première partie de l'email ?
4. **Les Fuites de Données :** Comment récupérer le numéro de téléphone ou l'adresse physique liés à cet email ?

---

### Étape 1 : Cartographier les comptes inscrits (Holehe)
Avant de chercher le nom de la personne, nous voulons savoir quels services elle utilise. 
S'est-elle inscrite sur Twitter, Instagram, Tinder, ou des forums de hackers ?
L'outil de référence absolu pour cela est **Holehe**.

**1. Installation :**
Holehe est un outil Python. On l'installe via le gestionnaire de paquets de Python (`pip`).
```bash
sudo apt install python3-pip -y
pip3 install holehe
```

**2. La commande de Scan :**
(Remplacez cible@email.com par l'email visé).

```Bash
holehe cible@email.com
```
*Comment ça marche ? (La technique secrète)*

Holehe est brillant car il est furtif. Il n'envoie aucun email à la cible. Il utilise la fonction 
"Mot de passe oublié" ou "Créer un compte" des sites web (plus de 120 sites supportés). Il analyse la 
réponse du serveur du site pour savoir si l'email existe déjà dans leur base de données.

Le Résultat : L'outil affichera en vert tous les sites (Spotify, Twitter, Github, etc.) 
où la cible possède un compte. Cela vous dresse le profil psychologique et numérique de la personne.

### Étape 2 : L'Investigation profonde sur Gmail (GHunt)

Si votre cible utilise une adresse se terminant par @gmail.com (ou un domaine d'entreprise géré 
par Google Workspace), vous avez touché le jackpot. L'outil GHunt permet d'extraire les données 
publiques liées au compte Google.

**1. Installation :**

```Bash
pipx install ghunt
```
*(Note : GHunt nécessitera une petite configuration initiale pour lui fournir les cookies d'un faux compte Google d'investigation).*

**2. La commande de Scan :**

```Bash
ghunt email cible@gmail.com
```
Le Résultat : GHunt va interroger les API de Google et peut vous retourner :

Le nom et prénom enregistrés sur le compte.

La photo de profil (que vous pourrez utiliser pour une recherche d'image inversée).

Les avis Google Maps : C'est la faille de sécurité n°1 ! Si la cible a laissé des avis sur des restaurants
 ou des hôtels, vous pouvez littéralement tracer ses habitudes géographiques, son lieu 
 de vacances ou son adresse professionnelle.

Le Google ID : Un numéro unique à 21 chiffres qui permet de suivre la cible même si elle change d'adresse email.

### Étape 3 : Le "Pivot" du Pseudonyme (Sherlock)

En OSINT, on utilise le concept du Pivot. Une donnée en amène une autre.
Regardez une adresse email : darkhacker99@yahoo.com.
La partie avant le @ (le local-part) est souvent le pseudonyme favori de la cible.

Nous allons utiliser l'outil Sherlock pour chercher ce pseudonyme exact sur plus de 300 réseaux sociaux.

**1. Installation :**

```Bash
git clone [https://github.com/sherlock-project/sherlock.git](https://github.com/sherlock-project/sherlock.git)
cd sherlock
pip3 install -r requirements.txt
```

**2. La commande de Scan :**

```Bash
python3 sherlock darkhacker99
```

Le Résultat : Sherlock va vous générer une liste de liens directs vers les profils Reddit, 
TikTok, Pinterest ou Twitch qui utilisent exactement ce pseudonyme. Vous venez d'étendre massivement 
votre surface d'investigation.

### Étape 4 : Atteindre l'identité réelle via les Data Breaches

C'est l'étape ultime. Vous avez les réseaux sociaux, mais vous voulez l'adresse physique, 
la date de naissance ou le numéro de téléphone de la cible.

Comme pour la formation "Phone OSINT", nous allons utiliser les bases de données issues 
de piratages d'entreprises (Fuites de données).

*La Méthode (Intelligence X / Dehashed) :*
Lorsque vous entrez l'adresse email dans des moteurs comme Dehashed.com ou Intelx.io, 
ces outils parcourent les fuites historiques (LinkedIn 2012, Canva 2019, etc.).

Dans une base de données piratée, l'email est la clé primaire. La ligne de la base de données vous révélera :

Le mot de passe (souvent haché, parfois en clair).

L'adresse IP de connexion (qui permet de géolocaliser la ville de la cible).

Le numéro de téléphone associé au compte.

Parfois l'adresse postale si la fuite provient d'un site de e-commerce.

#### Bilan de l'Enquête

Grâce à ce module, vous avez appris à faire "parler" une simple adresse email. 
Vous avez découvert la boucle infinie de l'OSINT :

L'email vous donne le pseudo (Sherlock) et les habitudes (GHunt).

L'email, via les fuites de données (Dehashed), vous donne un Numéro de téléphone.

Et que faites-vous avec ce numéro de téléphone ? Vous utilisez les compétences apprises 
dans le module Phone OSINT pour creuser encore plus loin !

Une bonne enquête n'a pas de fin, elle rebondit d'une donnée à l'autre.


***
