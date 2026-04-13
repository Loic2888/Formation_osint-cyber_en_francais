# Formation OSINT : Enquête sur un Numéro de Téléphone (Phone OSINT)

**Objectif du cours :** Apprendre la méthodologie et les outils pour extraire des informations 
à partir d'un simple numéro de téléphone (nom, prénom, comptes de réseaux sociaux associés, 
et présence dans des fuites de données).

**🛑 Avertissement Éthique et Légal :** Ces techniques doivent être utilisées uniquement dans 
le cadre d'enquêtes légitimes (cybersécurité, recherche de personnes disparues, journalisme d'investigation) 
ou sur vous-même. Le harcèlement (Stalking) ou la divulgation de données privées (Doxxing) sont 
des délits pénalement répréhensibles.

---

### L'Entonnoir du Phone OSINT

Une enquête sur un numéro ne se fait pas au hasard. On procède par couches successives, 
de la moins intrusive à la plus révélatrice :
1. **Validation technique :** Le numéro existe-t-il et d'où vient-il ?
2. **Identification (Caller ID) :** À qui appartient-il publiquement ?
3. **Empreinte Sociale :** Sur quelles applications est-il utilisé ?
4. **Fuites de données (Breaches) :** À quelle adresse email est-il lié ?

---

### Étape 1 : Validation et Empreinte Technique (PhoneInfoga)

Avant de chercher un nom, il faut analyser le numéro lui-même (opérateur, type de ligne, zone géographique,
 validité). Pour cela, l'outil de référence en ligne de commande est **PhoneInfoga**.

**1. Installation avec Docker :**
C'est la méthode la plus propre pour ne pas "polluer" votre système Linux.
```bash
sudo apt install docker.io -y 
```

**2. La commande de Scan :**
(Remplacez +33612345678 par le numéro cible au format international).

```Bash
sudo docker run -it --rm sundowndev/phoneinfoga scan -n "+33612345678"
```

*Que va faire l'outil ?*

Il va vérifier si le numéro est valide (via des bases de données télécoms).

Il va vous donner l'opérateur (Orange, SFR, Free, etc.). S'il s'agit d'un opérateur virtuel (comme Lycamobile ou Lebara), 
cela peut indiquer un numéro jetable (Burner phone).

Il va générer des liens (Google Dorks) tout prêts pour chercher ce numéro sur les moteurs de recherche.

### Étape 2 : L'Identification (Les annuaires inversés participatifs)

Si le numéro n'est pas dans les Pages Blanches, il faut utiliser les bases de données issues du Crowdsourcing 
(les applications qui aspirent les contacts de leurs utilisateurs).

Les trois géants de ce domaine sont Truecaller, Sync.me et Eyecon.

La Méthodologie "Propre" :
On n'utilise pas d'outils en ligne de commande ici pour éviter de se faire bloquer.

Allez sur le site web de sync.me ou truecaller.com.

Connectez-vous avec un faux compte Google (créé pour l'OSINT, jamais votre compte personnel).

Entrez le numéro de téléphone.

Le Résultat : Ces outils vous renverront le nom et le prénom sous lesquels cette personne est enregistrée 
dans le répertoire téléphonique de ses amis. C'est redoutable pour trouver l'identité réelle derrière un numéro prépayé.

### Étape 3 : L'Empreinte sur les Messageries (WhatsApp, Telegram)

C'est ici que l'enquête prend une dimension visuelle. Les applications de messagerie lient le numéro 
de téléphone à une photo de profil et à un pseudo (Bio).

La méthode du "Burner Phone" (Téléphone d'investigation) :
La technique la plus fiable ne se passe pas dans un terminal Linux, mais sur un smartphone dédié à 
l'OSINT (sans aucune de vos données personnelles).

Ajoutez le numéro cible dans les contacts de votre téléphone OSINT.

Ouvrez WhatsApp : Regardez si la personne a une photo de profil et lisez sa "Bio" (ex: "Travaille chez XYZ").

Ouvrez Telegram : C'est une mine d'or. Telegram vous donnera souvent le @pseudo de la personne.

Ouvrez Snapchat ou TikTok : Utilisez la fonction "Trouver des amis via les contacts". L'application va scanner 
votre répertoire (qui ne contient que le numéro cible) et vous proposer le profil exact de la personne.

(Astuce de pro : Pensez toujours à masquer votre propre numéro et à configurer votre confidentialité au maximum 
avant de faire cela, pour que la cible ne reçoive pas de notification "Nouveau contact sur Telegram").

### 📧 Étape 4 : Lier le numéro à un Email (Les Fuites de Données)

La dernière étape est de relier ce numéro à une adresse email pour continuer l'enquête. Comment faire ? 
Grâce aux outils d'investigation et aux fuites de données (Data Breaches).

Quand une entreprise (comme Facebook en 2021) se fait pirater, les bases de données contenant 
*[Nom, Email, Téléphone]* se retrouvent sur le web.

**La méthode et les outils :**

On n'utilise pas de données illégales directement. On interroge des moteurs de recherche 
spécialisés dans l'indexation de fuites, comme des sites d'analyse de compromission, ou des outils 
interrogeant des API publiques. Voici l'arsenal concret :

* **L'approche sans fuite (Epieos) :** Avant même de chercher dans les piratages, utilisez **Epieos** (`epieos.com`). 
Cet outil d'OSINT français interroge silencieusement les API de services publics. En entrant le numéro, 
il peut vous dire s'il est lié à un compte Google, vous donner un identifiant Skype (qui contient souvent le nom), 
ou révéler les avis Google Maps laissés par la cible.
* **La vérification légale (Have I Been Pwned) :** C'est le standard mondial (`haveibeenpwned.com`). En entrant le numéro au 
format international (ex: `+336...`), HIBP vous dira exactement dans quelles fuites publiques (ex: WhatsApp, Facebook) 
ce numéro est apparu. Il confirme la compromission sans afficher les données sensibles.
* **Les moteurs de recherche spécialisés (Intelligence X et Dehashed) :** Pour aller plus loin, des outils comme 
**Intelligence X** (`intelx.io`) ou **Dehashed** (`dehashed.com` - standard professionnel) archivent le Dark Web et les fuites. 
En entrant le numéro de téléphone dans ces bases, l'outil va chercher dans des milliards d'enregistrements historiques.

Le résultat de ces outils d'investigation profonde vous donnera la ligne complète de la base de données piratée : 
vous y trouverez **l'adresse email associée au compte lors du piratage**, le nom complet, et parfois les anciens mots 
de passe utilisés (souvent hachés).

Une fois l'email obtenu... l'enquête repart de zéro avec de toutes nouvelles pistes !

### Bilan de l'Enquête

Un numéro de téléphone n'est jamais isolé. En suivant cet entonnoir, vous êtes capables de transformer 
une suite de 10 chiffres en une véritable identité numérique :

Vous savez chez quel opérateur il est (PhoneInfoga).

Vous avez son vrai nom (Truecaller).

Vous avez son visage et son pseudo (WhatsApp/Telegram).

Vous avez son adresse email (Data Breaches).


***
