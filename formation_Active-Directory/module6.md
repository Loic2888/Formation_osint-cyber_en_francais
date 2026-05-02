# Module 6 : La Persistance Ultime – Le Golden Ticket

Dans les chapitres précédents, Léo a utilisé des failles pour obtenir des accès. Mais un accès peut être révoqué : on change 
un mot de passe, on supprime un compte, et le hacker perd son accès. Pour éviter cela, Léo va s'attaquer à la racine de la 
confiance Kerberos : le compte krbtgt.

### Chapitre 1 : Le Compte krbtgt – Le "Maître des Clés"

Dans chaque domaine Active Directory, il existe un compte créé automatiquement nommé krbtgt (pour Kerberos Ticket Granting Ticket).

**1.1 Son rôle crucial**

Souvenez-vous de l'analogie du festival (Module 3). Le compte krbtgt est celui qui tient le tampon officiel pour valider les bracelets (TGT).

Quand le Contrôleur de Domaine vous donne un TGT, il le chiffre et le signe avec le mot de passe (le Hash NTLM) du compte krbtgt.

Quand vous présentez votre TGT plus tard, le serveur vérifie la signature avec ce même secret.

*La faille logique :*
Si un attaquant possède le Hash NTLM du compte krbtgt, il devient lui-même le "guichetier". Il peut fabriquer ses propres bracelets (TGT) 
sans même demander l'avis du Contrôleur de Domaine.

### Chapitre 2 : L'Attaque DCSync – Voler sans laisser de traces

Léo est désormais Administrateur du Domaine (grâce au Module 5). Mais il ne veut pas aller fouiller dans la RAM du Contrôleur de 
Domaine (trop risqué, trop surveillé). Il va utiliser une technique beaucoup plus élégante : DCSync.

**2.1 Le mécanisme de réplication**

Dans un grand réseau, il y a souvent plusieurs Contrôleurs de Domaine. Ils ont besoin de se parler pour synchroniser leurs bases de données 
(si je change mon mot de passe sur le DC1(Domain Controller1), le DC2(Domain Controller2) doit le savoir). Pour cela, 
ils utilisent le protocole DRSUAPI.

Léo va faire croire au DC principal qu'il est lui-même un autre Contrôleur de Domaine qui a besoin d'une mise à jour de la base de données.

**2.2 L'action technique (Mimikatz)**

Depuis n'importe quel poste de travail, avec ses droits d'admin, Léo lance Mimikatz et demande poliment la réplication du secret de krbtgt :
```bash
lsadump::dcsync /domain:techsolution.local /user:krbtgt
```

Le résultat : Le Contrôleur de Domaine envoie gentiment le Hash NTLM du compte krbtgt à Léo. Léo possède maintenant la clé de 
voûte de tout l'édifice.

### Chapitre 3 : Forger le "Golden Ticket"

Maintenant que Léo a le secret de krbtgt, il peut créer un ticket parfait.

**3.1 Les caractéristiques du Golden Ticket**

Léo utilise Mimikatz pour générer un fichier .kirbi (un ticket Kerberos) avec les paramètres suivants :

Utilisateur : Il peut s'appeler "Administrateur", "Dieu", ou même un nom qui n'existe pas.

Groupes : Il s'ajoute dans le groupe "Domain Admins" (ID 512).

Durée de validité : Par défaut, un ticket dure 10 heures. Léo, lui, peut décider que son ticket est valable 10 ans.

La commande de forge :
```bash
kerberos::golden /user:Hacker /domain:techsolution.local /sid:S-1-5-21-1234... /krbtgt:HASH_VOLÉ /ticket:gold.kirbi
```

**3.2 L'utilisation**

Même si l'administrateur de TechSolution change son propre mot de passe, même s'il supprime le compte du stagiaire ou de la secrétaire, 
le ticket de Léo reste valide. Pourquoi ? Parce que le ticket est signé avec la clé de krbtgt. Tant que le mot de passe du compte krbtgt 
n'est pas changé, le ticket est considéré comme authentique par tous les serveurs du réseau.

### Chapitre 4 : L'Impact sur l'Entreprise

Pour TechSolution, c'est le cauchemar absolu.

Invisibilité : Léo n'utilise plus de compte existant. Il arrive sur le réseau avec son ticket forgé, accède à ce qu'il veut, et repart. 
Les logs indiqueront qu'un utilisateur nommé "Hacker" s'est connecté, mais ce compte n'existe nulle part dans l'annuaire.

Résistance : La seule façon de bannir Léo est de réinitialiser le mot de passe du compte krbtgt.

Attention : Microsoft recommande de le changer deux fois de suite (car Windows garde en mémoire l'ancien et le nouveau mot de passe 
pour ne pas casser les tickets en cours). Si on ne le change qu'une fois, le Golden Ticket de Léo fonctionne encore !

### Chapitre 5 : Résumé pour wooami.net

| Terme                  | Ce qu'il faut retenir pour l'examen                                                                                                                              |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| krbtgt                 | Le compte le plus critique de l'AD. Son mot de passe signe tous les tickets Kerberos.                                                                            |
| DCSync                 | Technique pour simuler une réplication entre serveurs et voler des secrets sans toucher à la RAM.                                                                |
| Golden Ticket          | Un ticket TGT forgé de toutes pièces qui donne des droits illimités et éternels.                                                                                |
| SID (Security Identifier) | L'identifiant unique du domaine nécessaire pour forger le ticket.                                                                                             |

### La Conclusion du Professeur

Le Golden Ticket est la consécration du pentester. C'est la preuve d'une compromission totale. Une fois cette étape atteinte, 
l'attaquant ne "hache" plus le système, il en fait partie intégrante.

C'est pour cette raison que la surveillance des droits de réplication (qui peut faire un DCSync ?) est l'une des alertes les 
plus critiques dans un centre de cybersécurité (SOC).

### Vers le Module 7 : La Détection et la Remédiation

Léo a gagné. Mais en tant que futurs experts, vous ne devez pas seulement savoir attaquer, vous devez savoir protéger. Maintenant que 
nous avons vu toutes les étapes de l'attaque, nous allons passer du côté des "Gentils" (les Blue Teams).

Nous allons voir comment détecter Léo, comment nettoyer le réseau après son passage, et comment configurer l'Active Directory pour que 
son aventure s'arrête dès le premier module.

*À votre avis, qu'est-ce qui est le plus dur pour une entreprise : détecter que Léo est entré, ou réussir à le mettre dehors* 
*complètement sans tout casser ?*

En réalité, réussir à mettre un attaquant dehors complètement sans tout casser est bien plus difficile que de simplement le détecter.

Imaginez TechSolution comme un château où un espion a non seulement volé les clés, mais a aussi creusé des tunnels secrets, 
corrompu certains gardes et changé les serrures de secours. Si vous vous contentez de changer la serrure de la porte d'entrée, 
l'espion est toujours à l'intérieur.

Bienvenue dans le Module 7. Nous passons de l'autre côté du miroir : vous n'êtes plus l'attaquant Léo, vous êtes le responsable de 
la sécurité qui doit sauver TechSolution.
