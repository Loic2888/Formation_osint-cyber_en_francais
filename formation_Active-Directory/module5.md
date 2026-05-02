# Module 5 : La Coercition d'Authentification – Forcer la Main du Roi

Dans les modules précédents, Léo devait attendre qu'un administrateur fasse une erreur ou se connecte sur une machine piégée. 
Mais les bons administrateurs sont rares et prudents. Léo décide donc d'utiliser des protocoles de "rappel" pour forcer le serveur 
le plus puissant du réseau à lui envoyer ses preuves d'identité.

### Chapitre 1 : Le Concept de Coercition

Dans Windows, de nombreux services sont conçus pour communiquer entre eux. Certains protocoles permettent à un ordinateur A 
de dire à un ordinateur B : "Hé, connecte-toi à moi pour que je te donne une information".

*La Faille :*
Lorsque l'ordinateur B (le Contrôleur de Domaine) tente de se connecter à l'ordinateur A (l'attaquant), il doit prouver qui il est. 
Pour ce faire, il envoie automatiquement son Hash NTLM de compte machine (ex: DC01$).

L'attaquant ne cherche plus à voler le mot de passe d'un humain, mais l'identité de la machine elle-même.

### Chapitre 2 : Le "PrinterBug" (MS-RPRN)

C'est l'une des méthodes les plus anciennes et les plus célèbres. Elle utilise le protocole de gestion des imprimantes à distance.

**2.1 Le mécanisme technique**

Même si aucune imprimante n'est branchée sur le serveur, le service de "Spouleur d'impression" est souvent activé par défaut 
sur les Contrôleurs de Domaine.

Léo utilise un outil (comme dementor.py ou SpoolSample) pour envoyer une requête au Contrôleur de Domaine :

"Hé, cher Contrôleur de Domaine, je suis une imprimante et j'ai une mise à jour pour toi. S'il te plaît, connecte-toi à mon adresse 
IP [10.10.10.LÉO] pour vérifier cela."

**2.2 La réaction du serveur**

Le Contrôleur de Domaine, programmé pour être coopératif, s'exécute. Il tente de se connecter à la machine de Léo via le protocole SMB. 
Pour ce faire, il présente ses identifiants : le hash NTLM de son propre compte machine (TECHSOLUTION\DC01$).

### Chapitre 3 : PetitPotam (MS-EFSRPC)

Si l'administrateur de TechSolution a eu la bonne idée de désactiver le service d'impression, Léo n'est pas bloqué. Il utilise une 
faille plus récente et plus puissante : PetitPotam.

**3.1 Pourquoi "PetitPotam" ?**

Cette attaque exploite le protocole MS-EFSRPC (utilisé pour le chiffrement de fichiers à distance). Contrairement au PrinterBug, 
il est beaucoup plus difficile à désactiver car il est lié à des fonctions de base du système de fichiers Windows.

Léo envoie une commande spécifique qui force le serveur à essayer d'ouvrir un fichier sur sa propre machine d'attaquant. 
Le résultat est le même : le serveur "mord à l'hameçon" et envoie son authentification.

### Chapitre 4 : Pourquoi est-ce utile ? (Le NTLM Relay)

C'est ici que l'étudiant doit bien s'accrocher. Récupérer le hash DC01$ est inutile si on essaie de le cracker (le mot de passe d'un 
compte machine fait 128 caractères aléatoires, c'est incrackable).

L'astuce de Léo est le Relais NTLM.

**4.1 Le scénario du relais**

Au lieu de garder le hash pour lui, Léo va le "rediffuser" instantanément vers une autre cible sensible du réseau, comme le 
serveur AD CS (Active Directory Certificate Services).

Léo force le DC(Domain Controller) à se connecter à lui (via PetitPotam).

Léo reçoit la connexion et, au lieu de répondre, il la transmet immédiatement au serveur de certificats.

Le serveur de certificats croit que c'est le DC(Domain Controller) qui demande un certificat d'administrateur.

Le serveur de certificats génère un certificat et l'envoie à Léo.

**4.2 La victoire finale**

Avec ce certificat, Léo peut se faire passer pour le Contrôleur de Domaine lui-même. Il peut alors effectuer une attaque 
appelée DCShadow ou DCSync pour vider la base de données de tous les secrets de l'entreprise.

### Chapitre 5 : Résumé pour wooami.net

|Terme|Ce qu'il faut comprendre|
|Coercition|Forcer un serveur à initier une connexion vers l'attaquant.|
|PrinterBug|Utiliser le service d'impression pour forcer le rappel.|
|PetitPotam|Utiliser le service de fichiers chiffrés pour forcer le rappel.|
|NTLM Relay|Intercepter une authentification et la renvoyer vers une autre cible pour usurper l'identité.|
|Compte Machine ($)|L'identité unique d'un serveur (ex: DC01$). Sa compromission est fatale.|

### La Conclusion du Professeur

La coercition d'authentification montre que dans l'Active Directory, personne n'est à l'abri. Même si tous les employés ont des 
mots de passe complexes et utilisent la double authentification, les serveurs eux-mêmes peuvent être manipulés pour trahir leurs propres secrets.

C'est pour cela qu'un bon pentester ne regarde pas seulement les utilisateurs, mais aussi les protocoles de communication entre serveurs.

### Vers le Module 6 : La Persistance

Léo est désormais le maître du domaine. Il a réussi. Mais il sait qu'un jour ou l'autre, l'équipe de sécurité va se rendre compte de quelque 
chose et changer les mots de passe. Léo veut s'assurer que même si on réinitialise tout le réseau, il pourra revenir quand il veut.

*C'est ce qu'on appelle la Persistance.*

À ton avis, quel est l'objet le plus précieux dans l'Active Directory que Léo pourrait créer ou modifier pour s'assurer un accès éternel, 
même si on supprime son compte de stagiaire ? (Indice : c'est lié au "Golden Ticket").

C'est le secret le mieux gardé de l'Active Directory. Quand on parle de "contrôle total et éternel", on parle d'un compte bien précis 
que personne n'utilise jamais pour se connecter, mais qui est le pilier de toute la confiance du réseau : le compte krbtgt.

Bienvenue dans le Module 6. Nous allons voir comment Léo, après avoir bousculé les serveurs de TechSolution, va maintenant s'assurer 
que personne ne pourra jamais le mettre dehors.
