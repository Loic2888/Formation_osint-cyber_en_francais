# Module 2 : La Progression de l’Attaquant – De l’Infiltration Initiale au Contrôle Total du Domaine 

Bienvenue dans ce second chapitre. Si le premier module vous a présenté la structure théorique de l’Active Directory, 
ce module-ci est celui de la réalité du terrain. Nous allons suivre le parcours d'un attaquant au sein d'une entreprise 
fictive mais représentative : TechSolution.

À travers cette histoire, nous allons décortiquer chaque concept technique, chaque protocole et chaque outil. Ce cours n'est 
pas une simple liste d'étapes ; c'est une étude sur la manière dont la confiance est exploitée dans un réseau Windows.

### Chapitre 1 : L'Investigation Préliminaire – L'Art de l'OSINT

L'attaque ne commence pas par une ligne de code, mais par une recherche. Notre attaquant, appelons-le Léo, ne connaît rien 
de TechSolution au départ. Son premier objectif est de transformer une entreprise anonyme en une liste de cibles potentielles. 
C'est ce qu'on appelle l'OSINT (Open Source Intelligence).

**1.1 Le "Scraping" des Identités**

Léo se rend sur LinkedIn. Grâce aux filtres de recherche, il extrait la liste des employés de TechSolution. 
Il repère trois profils clés :

Jean-Michel, le nouveau stagiaire IT (vulnérable par manque d'expérience).

Sophie Martin, l'assistante de direction (point de pivot vers les décideurs).

Marc Durand, l'administrateur système (la cible ultime).

**1.2 L'Identification Technique de la Convention de Nommage**

Léo ne peut pas "deviner" l'identifiant de connexion. Il doit le confirmer. Pour cela, il utilise des outils comme Hunter.io 
ou Phonebook.cz. Ces plateformes agrègent toutes les adresses emails d'une entreprise ayant fuité sur le web.

En analysant les résultats, il remarque que les adresses sont formatées ainsi : m.durand@techsolution.com. Il en déduit 
techniquement la Convention de Nommage : l'identifiant AD (le sAMAccountName) est très probablement l'initiale du prénom 
suivie du nom (m.durand).

Pour être certain, il télécharge un livre blanc au format PDF sur le site officiel de TechSolution. Il utilise ensuite 
l'outil exiftool en ligne de commande :
```bash
exiftool rapport_annuel.pdf
```

Dans les métadonnées du document, le champ "Créateur" indique s.martin. La preuve est faite : le format est confirmé. 
L'attaquant possède maintenant sa liste de cibles : j.michel, s.martin, m.durand.

### Chapitre 2 : La Brèche Initiale – Le "Password Spraying"

Léo sait que s'il tente 50 mots de passe sur le compte de Marc Durand, le système de sécurité va détecter l'attaque et verrouiller 
le compte (c'est la Account Lockout Policy). Pour rester invisible, il va utiliser la technique de l'Aspersion de mots de 
passe (Password Spraying).

**2.1 La Psychologie du Mot de Passe par Défaut**

Dans une boîte comme TechSolution, la création des comptes stagiaires est automatisée. Pour que Jean-Michel puisse se connecter 
facilement le premier jour, l'IT lui attribue un mot de passe temporaire. Léo parie sur la paresse humaine et teste un 
standard : Bienvenue2026!.

**2.2 L'Exécution Technique**

Léo utilise un outil nommé NetExec. Cet outil est un "couteau suisse" qui permet de tester des identifiants sur tout un réseau 
ou sur un portail web (comme le VPN de l'entreprise).
```bash
nxc smb 10.10.20.50 -u utilisateurs.txt -p 'Bienvenue2026!'
```

L'outil teste le mot de passe une seule fois pour chaque utilisateur de la liste. Le serveur ne voit aucune anomalie : 
juste une erreur de frappe isolée par employé. Mais soudain, la ligne devient verte :
```
[+] techsolution.local\j.michel:Bienvenue2026! (Pwn3d!)
```

Léo vient d'obtenir son accès initial. Il est maintenant dans le réseau.

### Chapitre 3 : L'Énumération Interne – Cartographier l'Invisible

Léo est connecté sur le PC de Jean-Michel. Il n'a aucun droit d'administration, mais il a un avantage majeur : il est Authentifié. 
Dans un domaine Active Directory, un utilisateur authentifié peut interroger l'annuaire via le protocole LDAP 
(Lightweight Directory Access Protocol).

**3.1 La Découverte des Privilèges**

Léo veut savoir qui commande. Il tape une commande Windows native :
```bash
net group "Domain Admins" /domain
```

Le système lui répond que seul m.durand est Administrateur du Domaine. Léo sait maintenant qui il doit abattre.

**3.2 Le GPS du Hacker : BloodHound**

Pour visualiser le chemin le plus court vers Marc Durand, Léo utilise BloodHound. Cet outil aspire toutes les relations de 
confiance de l'AD (qui a le droit de faire quoi, qui est connecté où).

BloodHound révèle un chemin critique :

Le stagiaire j.michel a le droit de se connecter au PC de la secrétaire s.martin.

L'admin m.durand s'est connecté récemment sur ce même PC.

Léo a son plan de route : il doit "pivoter" vers le poste de la secrétaire.

### Chapitre 4 : Le Pivot Horizontal – La Prise de Contrôle de Sophie Martin

L'attaquant ne peut pas sauter directement du stagiaire à l'administrateur. Il doit effectuer un Mouvement Horizontal. 
Il va utiliser la confiance de Sophie Martin envers ses collègues.

**4.1 Le Phishing Interne**

Léo envoie un message sur la messagerie interne (Teams ou Outlook) à Sophie : "Bonjour Sophie, c'est Jean-Michel de l'IT. 
On a un problème avec votre profil, pouvez-vous cliquer sur ce lien pour synchroniser votre session ?".

Le lien pointe vers un fichier .LNK (un raccourci Windows modifié). Dès que Sophie clique, un script invisible s'exécute en arrière-plan. 
Ce script ouvre une porte dérobée (Reverse Shell) sur l'ordinateur de Sophie. Léo est maintenant "maître" de la machine de la secrétaire.

### Chapitre 5 : L'Élévation de Privilèges – La Mémoire des Secrets

Nous arrivons à l'étape la plus technique et la plus cruciale de ce cours. Léo est sur le PC de Sophie. Il sait que Marc Durand (l'admin) 
s'est connecté ici ce matin pour réparer une imprimante.

**5.1 Le Processus LSASS : Le Gardien des Clés**

Dans chaque système Windows, il existe un processus nommé LSASS (Local Security Authority Subsystem Service). C'est le cœur de la sécurité locale. 
Lorsqu'un utilisateur se connecte, LSASS garde une copie de ses identifiants en mémoire vive (RAM). Pourquoi ? Pour éviter que Windows ne vous 
redemande votre mot de passe à chaque fois que vous accédez à un fichier partagé ou à une boîte mail.

**5.2 L'Extraction avec Mimikatz**

Léo déploie Mimikatz, un outil qui permet de lire directement dans la mémoire de LSASS. Pour cela, il doit d'abord obtenir les privilèges 
d'administrateur local sur le PC de Sophie (ce qu'il fait en exploitant une faille de mise à jour non patchée).

Il tape alors les commandes sacrées :
```
privilege::debug (Donne à Mimikatz le droit de lire la mémoire du système)
sekurlsa::logonpasswords (Demande à Mimikatz de lister tous les secrets en mémoire)
```

L'écran défile et affiche une pépite d'or :
```
User : m.durand
NTLM Hash : 2B108F3FA6CB6DE52CAC67419A9A224A
```

Léo n'a pas le mot de passe de Marc Durand, mais il possède son Hash NTLM.

### Chapitre 6 : Le Coup de Grâce – Le Pass-the-Hash (PtH)

C'est ici que beaucoup de débutants font une erreur de compréhension. Ils pensent qu'il faut absolument "cracker" le hash pour 
retrouver le mot de passe original. C'est faux.

**6.1 Pourquoi le Hash suffit-il ?**

Le protocole d'authentification de Windows (NTLM) est conçu de telle sorte que pour prouver votre identité, vous envoyez le Hash, pas 
le mot de passe. Posséder le hash, c'est posséder l'identité. C'est comme si vous aviez volé le badge magnétique d'un directeur : peu 
importe que vous connaissiez son code secret, le badge ouvre la porte.

**6.2 L'Action Finale**

Léo utilise Mimikatz pour injecter le hash de Marc Durand dans une nouvelle session de commande :
```bash
sekurlsa::pth /user:m.durand /domain:techsolution.local /ntlm:2B108F3FA6CB6DE52CAC67419A9A224A
```

Une nouvelle fenêtre d'invite de commande s'ouvre. Pour le réseau de TechSolution, cette fenêtre est gérée par l'Administrateur 
du Domaine. Léo tape :
```bash
net user hacker Password123! /add /domain
net group "Domain Admins" hacker /add /domain
```

L'attaque est terminée. Léo a créé son propre compte administrateur. Il possède désormais les clés de toute l'entreprise. Il peut lire les 
emails du PDG, copier la base de données client, ou tout effacer.

### Conclusion du Module 2 – Ce qu'il faut retenir

Pour maîtriser l'Active Directory, vous devez intégrer ces trois concepts fondamentaux :

L'Énumération est la clé : Un simple compte de stagiaire permet de cartographier tout le réseau.

La RAM est un cimetière de secrets : Toute connexion d'un administrateur sur un poste non sécurisé laisse une trace 
exploitable dans le processus LSASS.

Le Hash est l'identité : Avec le Pass-the-Hash, l'attaquant n'a jamais besoin de connaître votre vrai mot de passe.

### Exercice de réflexion pour la suite :

Dans ce scénario, Léo a réussi car il a pu lire la mémoire du processus LSASS. Microsoft a récemment introduit une protection 
appelée Credential Guard qui utilise la virtualisation pour isoler LSASS et empêcher Mimikatz d'y accéder.

À votre avis, si Credential Guard avait été activé sur le poste de Sophie, comment Léo aurait-il pu essayer de récupérer l'identité 
de l'administrateur autrement ? (Indice : cherchez du côté des "Tickets" dans le protocole Kerberos).

*Je sais pas?*

Si la porte NTLM est fermée par une protection comme Credential Guard, un attaquant comme Léo ne s'avoue pas vaincu : 
il change de protocole. Il passe de NTLM à Kerberos.
Nous touchons ici à la frontière entre le pentest "classique" et le pentest "avancé". Si tu ne sais pas, c'est que nous avons 
trouvé le sujet parfait pour notre Module 3.
