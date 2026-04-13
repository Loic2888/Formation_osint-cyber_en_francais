# Formation OSINT : SOCMINT Profond (Social Media Intelligence)

**Objectif du cours :** Aller au-delà de la simple recherche d'un profil. Apprendre à traquer 
un pseudonyme de manière agressive, à contourner les restrictions des réseaux sociaux, à aspirer 
(scraper) les données avant qu'elles ne soient supprimées, et à cartographier le cercle intime d'une cible.

**🛑 Avertissement Éthique et Légal :** L'analyse des réseaux sociaux révèle la vie privée, 
les opinions politiques, et les relations d'une personne. Le respect du RGPD et du droit à la vie privée 
est fondamental. L'aspiration massive de données à des fins commerciales ou de harcèlement est illégale. 
L'utilisation de "faux comptes" (Sockpuppets) peut violer les Conditions Générales d'Utilisation (CGU) des plateformes.

---

### L'Entonnoir du SOCMINT

Analyser un réseau social ne consiste pas à "scroller" passivement sur un fil d'actualité. L'enquête suit 4 étapes tactiques :
1. **L'OPSEC (Sockpuppets) :** Comment enquêter sans se faire repérer.
2. **La Traque de Pseudo Avancée :** Passer de Sherlock à Maigret.
3. **Le Contournement (Google Dorks & Filtres) :** Fouiller sans avoir besoin d'être connecté.
4. **L'Extraction et l'Archivage :** Sauvegarder les preuves hors-ligne.

---

### Étape 1 : La Sécurité Opérationnelle (Les Sockpuppets)

Règle absolue du SOCMINT : **N'utilisez jamais votre vrai compte (Facebook, Insta, LinkedIn) pour enquêter.**
Les plateformes enregistrent votre adresse IP et suggèrent souvent "Connaissez-vous cette personne ?" 
à votre cible si vous visitez son profil.

Vous devez créer un **Sockpuppet** (un faux compte d'investigation) :
* Utilisez une adresse email dédiée (ProtonMail) et un "Burner Phone" (numéro prépayé) pour l'inscription.
* Générez une photo de profil via Intelligence Artificielle (`thispersondoesnotexist.com`) 
pour éviter la recherche d'image inversée.
* "Chauffez" le compte : abonnez-vous à des pages publiques, ajoutez quelques faux amis, pour que l'algorithme 
ne vous bannisse pas comme un robot dès votre première recherche.

---

### Étape 2 : Traque de Pseudonyme Nouvelle Génération (Maigret)

Dans le module Email, nous avons utilisé *Sherlock*. Mais en SOCMINT profond, les professionnels utilisent **Maigret**. 
*Pourquoi ?* 
Sherlock vérifie juste si le compte existe (Code HTTP 200). Maigret, lui, télécharge la page HTML, 
extrait le nom complet affiché, la biographie, et parfois même d'autres liens vers d'autres réseaux sociaux !

**1. Installation :**
```bash
pip3 install maigret
```

**2. La commande de Scan (Analyse Profonde) :**
*(Remplacez cible_pseudo par le pseudo trouvé)*
```Bash
maigret cible_pseudo -a
```

Le Résultat : L'option -a lance une analyse agressive. Maigret va vous générer un rapport PDF ou HTML complet, 
avec les vrais noms trouvés sur les sites, vous permettant de découvrir la véritable identité cachée derrière le pseudonyme.

### Étape 3 : Le Contournement (Dorks & Opérateurs Natifs)

Facebook et LinkedIn vous demandent de vous connecter pour voir des choses ? 
Utilisez Google pour passer par la porte de derrière.

**1. X-Raying LinkedIn & Facebook :**
Vous voulez trouver les employés d'une entreprise dans une ville précise sans alerter LinkedIn ?

site:[linkedin.com/in/](https://linkedin.com/in/) "Nom de l'entreprise" "Paris"
Vous cherchez un profil Facebook sans avoir de compte ?

site:facebook.com "Prénom Nom" AND "Ville"

**2. Les Opérateurs avancés de X (anciennement Twitter) :**
La barre de recherche de Twitter est un outil d'OSINT redoutable si on connaît les codes. 
Tapez ceci directement dans Twitter :

from:pseudo cible (Affiche uniquement ses propres tweets).

to:pseudo cible (Affiche tous les messages que les autres lui ont envoyés, parfait pour trouver ses amis).

from:pseudo since:2018-01-01 until:2018-12-31 (Isole exactement ce qu'il a dit cette année-là).

### Étape 4 : L'Extraction et l'Archivage (Instaloader)

En OSINT, une règle prévaut : "Si c'est en ligne aujourd'hui, ça peut disparaître demain".
Dès que vous trouvez un profil intéressant, vous devez l'archiver avant que la cible ne le passe en privé.

Exemple avec Instagram via l'outil Instaloader.

**1. Installation :**

```Bash
pip3 install instaloader
```

**2. La commande d'Aspiration :**

```Bash
instaloader profile pseudo_cible
```
Le Résultat : L'outil va créer un dossier sur votre ordinateur. Il va télécharger absolument toutes les photos, 
toutes les vidéos, et toutes les descriptions (captions) avec leur date exacte. Vous pourrez alors examiner les 
photos à la loupe hors-ligne (pour y appliquer les techniques de géolocalisation GEOINT) sans risquer de "liker" une 
photo par erreur avec votre téléphone !

### Étape 5 : L'Analyse du Graphe Social (Identifier le cercle intime)

Quelqu'un avec 5 000 followers n'a pas 5 000 amis. Comment trouver le "premier cercle" de la cible 
(conjoint, famille, collègues proches) ?

*La technique du Croisement de Plateformes :*

Regardez qui interagit (Like/Commente) systématiquement sur les posts les plus anciens 
(ceux qui ont le moins de visibilité).

Prenez ces 3 ou 4 personnes actives et cherchez-les sur d'autres plateformes (Spotify, GitHub, Strava, Pinterest).

La Règle d'Or : Si Personne A et Personne B sont amies sur Facebook ET qu'elles se suivent sur une plateforme de 
niche comme Spotify ou Strava (l'application de course à pied), il y a 99% de chances qu'elles soient 
intimement liées dans la vraie vie.

### Bilan de l'Enquête Sociale

Vous ne naviguerez plus jamais sur les réseaux sociaux comme avant. Vous avez appris à :

Protéger votre identité avec des Sockpuppets.

Extraire des données riches à partir d'un simple pseudo avec Maigret.

Utiliser les moteurs de recherche pour contourner les murs de connexion.

Aspirer des comptes entiers pour une analyse hors-ligne sécurisée.

Cartographier le véritable entourage d'une cible numérique.


---
