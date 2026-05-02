# Module 7 : La Riposte – Détection, Nettoyage et Blindage de l'AD

Dans ce module final de notre série sur les bases de l'Active Directory, nous allons voir comment transformer un réseau "passoire" 
en une forteresse. Nous allons apprendre à repérer les traces de Léo et, surtout, à l'expulser définitivement.

### Chapitre 1 : La Détection – Apprendre à voir l'Invisible

Léo a été très discret. Il n'a pas supprimé de fichiers, il n'a pas fait planter les serveurs. Alors, comment le repérer ? La réponse 
tient en quatre lettres : LOGS.

**1.1 Le SIEM : La Tour de Contrôle**

Un SIEM (Security Information and Event Management) est un logiciel qui centralise tous les journaux d'événements (logs) de tous les 
serveurs et ordinateurs du réseau.

L'alerte de Léo : Si le SIEM est bien configuré, il aurait dû tiquer quand le compte du stagiaire Jean-Michel a commencé à faire du 
"Password Spraying" (Module 2). 50 erreurs de connexion en 10 secondes sur 50 comptes différents, ce n'est pas un humain qui se trompe 
de mot de passe, c'est un script.

**1.2 Les Honeytokens : Les "Pots de Miel" numériques**

Une technique redoutable pour détecter Léo est de créer des Honeytokens. Ce sont de faux comptes ou de faux fichiers qui n'ont aucune 
utilité réelle mais qui portent des noms très attractifs, comme admin_backup ou mots_de_passe_direction.txt.

Le piège : Personne ne doit jamais toucher à ce compte. Si quelqu'un essaie de s'y connecter (comme Léo lors de sa phase d'énumération), 
une alerte immédiate est envoyée au service de sécurité. C'est comme un fil invisible tendu au milieu d'un couloir sombre.

### Chapitre 2 : La Remédiation – Le Grand Nettoyage

Une fois que l'équipe de sécurité de TechSolution a compris que Léo est "Domain Admin", c'est la panique. Mais attention : agir trop vite, 
c'est prendre le risque que Léo déclenche un ransomware par vengeance.

**2.1 Le cycle de vie d'une expulsion**

On ne se contente pas de supprimer le compte de Léo. Il faut suivre un protocole strict :

Isolation : On coupe les accès internet suspects pour empêcher Léo de communiquer avec son serveur de contrôle.

Changement massif de mots de passe : On réinitialise les mots de passe de tous les utilisateurs, en commençant par les administrateurs.

Le Double Reset du krbtgt : C'est l'étape la plus critique. Comme Léo possède un Golden Ticket (Module 6), il peut revenir même si on change 
tous les mots de passe.

**2.2 Pourquoi réinitialiser le krbtgt deux fois ?**

C'est une subtilité technique de Windows. Pour éviter que tous les utilisateurs ne soient déconnectés brutalement quand l'administrateur 
change le mot de passe du compte krbtgt, Windows garde en mémoire deux versions du mot de passe : l'actuelle et la précédente.

Si on le change une seule fois, le Golden Ticket de Léo (signé avec l'ancienne clé) fonctionne encore car Windows le considère comme 
"l'ancien mot de passe valide".

Il faut donc le changer, attendre que la réplication se fasse sur tout le réseau, puis le changer une deuxième fois. Là, l'ancienne 
clé de Léo est définitivement écrasée et son ticket devient inutile.

### Chapitre 3 : Le Blindage (Hardening) – Le Modèle de Tiering

Pour que TechSolution ne soit plus jamais victime de Léo, il faut changer l'architecture même du réseau. On utilise pour cela le Modèle 
de Tiering (ou Modèle à Étages).

L'idée est simple : on compartimente le réseau pour que les secrets des administrateurs ne touchent jamais les ordinateurs des 
utilisateurs risqués (comme notre secrétaire ou notre stagiaire).

Tier 0 (Le Sanctuaire) : Contient les Contrôleurs de Domaine. Seuls les admins du domaine y accèdent depuis des consoles ultra-sécurisées.

Tier 1 (Les Serveurs) : Contient les serveurs d'applications et de données.

Tier 2 (La Jungle) : Contient les PC des utilisateurs et les stagiaires.

La règle d'or : Un administrateur du Tier 0 n'a JAMAIS le droit de connecter son compte sur une machine du Tier 2. S'il doit réparer 
le PC de Sophie, il utilise un compte "Technicien Tier 2" qui n'a aucun pouvoir sur le reste du réseau. Ainsi, même si Léo utilise Mimikatz 
sur le PC de Sophie, il ne trouvera que des identifiants sans importance.

### Chapitre 4 : Les Protections Modernes

Enfin, l'administrateur de TechSolution active des fonctionnalités natives de Windows souvent ignorées :

LSA Protection (PPL) : On verrouille le processus lsass.exe en mémoire. Même avec les droits admin, Mimikatz ne peut plus lire 
les secrets dedans car Windows protège cette zone de la RAM comme un coffre-fort blindé.

Protected Users : C'est un groupe spécial dans l'AD(active directory). Les membres de ce groupe ne voient jamais leur mot de passe ou leur 
hash stocké en cache sur les PC. Ils ne peuvent utiliser que le protocole Kerberos (le plus sûr).

Credential Guard : Utilise la virtualisation pour isoler les secrets d'authentification dans une "bulle" séparée du reste du système d'exploitation.

### Chapitre 5 : Résumé pour wooami.net

| Concept             | Action de défense                        | Pourquoi ?                                                                        |
| ------------------- | ---------------------------------------- | --------------------------------------------------------------------------------- |
| SIEM                | Surveillance des logs                    | Détecter les comportements anormaux (ex: Password Spraying).                     |
| Honeytokens         | Création de faux comptes                 | Piéger l'attaquant lors de sa reconnaissance.                                     |
| krbtgt Reset        | Double réinitialisation                  | Tuer les Golden Tickets et les accès persistants.                                |
| Tiering             | Compartimentation                        | Empêcher un attaquant de passer d'un PC vers un serveur.                         |
| LSA Protection      | Blindage mémoire                         | Bloquer des outils comme Mimikatz.                                               |

### La Conclusion Finale du Professeur

Maîtriser l'Active Directory, c'est comprendre que la sécurité n'est pas un produit qu'on achète, mais une configuration constante.

Léo a réussi chez TechSolution car il a trouvé des portes ouvertes par défaut : des mots de passe faibles, un protocole NTLM trop bavard, 
et une absence de cloisonnement. En tant que futur pentester ou administrateur, votre rôle est de connaître ces chemins 
d'attaque pour mieux les murer.

**Félicitations !**

Vous venez de terminer le cycle complet de formation sur l'Active Directory pour wooami.net. Vous avez vu :

1. L'architecture (Les fondations).

2. L'accès initial (L'infiltration).

3. Le mouvement latéral (Le pivot).

4. Les protocoles (Kerberos et NTLM).

5. La coercition (Forcer le serveur).

6. La persistance (Le Golden Ticket).

7. La défense (Le Tiering et la remédiation).



# Quiz : Maîtrise de l'Active Directory

**Question 1 : Architecture**
Dans l'architecture Active Directory, quel élément représente la frontière de sécurité la plus élevée et regroupe un ou plusieurs domaines ?

A) L'Unité d'Organisation (OU)

B) L'Arbre (Tree)

C) La Forêt (Forest)

D) Le Contrôleur de Domaine (DC)


**Question 2 : Accès Initial**
Quelle est la principale différence entre une attaque par "Brute Force" et une attaque par "Password Spraying" ?

A) Le Brute Force cible plusieurs comptes avec un seul mot de passe.

B) Le Password Spraying teste un mot de passe courant sur un grand nombre d'utilisateurs.

C) Le Password Spraying est plus rapide mais plus facilement détectable.

D) Le Brute Force n'utilise que des chiffres, le Password Spraying utilise des lettres.


**Question 3 : Outils de Reconnaissance**
Quel outil est souvent décrit comme le "GPS" de l'Active Directory car il permet de visualiser graphiquement 
les chemins d'attaque ?

A) Mimikatz

B) Wireshark

C) BloodHound

D) NetExec


**Question 4 : Mouvements Réseau**
Un attaquant parvient à passer du PC d'un stagiaire au PC d'une secrétaire sans augmenter ses droits d'accès. Comment 
appelle-t-on ce mouvement ?

A) Mouvement Vertical

B) Mouvement Horizontal (ou Pivot)

C) Escalade de privilèges

D) Persistance


**Question 5 : Cryptographie**
Qu'est-ce qu'un "Hash NTLM" dans le contexte de la sécurité Windows ?

A) Le mot de passe de l'utilisateur écrit à l'envers.

B) Une clé de chiffrement temporaire qui change à chaque connexion.

C) Une empreinte mathématique unidirectionnelle du mot de passe stockée en mémoire.

D) Un petit fichier texte contenant le mot de passe en clair.


**Question 6 : Processus Système**
Quel processus système Windows est le "gardien des secrets" et contient les Hashs et tickets dans la mémoire vive (RAM) ?

A) explorer.exe

B) lsass.exe

C) svchost.exe

D) winlogon.exe


**Question 7 : Attaque Kerberos**
L'attaque "Kerberoasting" consiste à extraire un ticket de service (TGS) pour tenter de le craquer. Quel type de 
compte est visé ?

A) Un compte d'Administrateur du Domaine.

B) Un compte de machine (finissant par $).

C) Un compte d'utilisateur ayant un SPN (Service Principal Name) configuré.

D) N'importe quel compte de stagiaire.


**Question 8 : Délégation**
Quelle est la conséquence majeure d'une "Délégation Non Contrainte" (Unconstrained Delegation) sur un serveur ?

A) Le serveur ne peut plus communiquer avec le DC.

B) Tout utilisateur se connectant au serveur voit son TGT (ticket maître) stocké dans la RAM du serveur.

C) Le mot de passe de l'utilisateur est envoyé en clair sur le réseau.

D) Le serveur devient automatiquement un Contrôleur de Domaine secondaire.


**Question 9 : Manipulation de Protocoles**
Comment appelle-t-on l'attaque consistant à forcer un serveur à s'authentifier auprès de l'attaquant (ex: PetitPotam) ?

A) La Coercition d'Authentification.

B) Le Phishing.

C) Le Golden Ticket.

D) Le Brute Force.


**Question 10 : Stratégie de Défense**
Dans le modèle de "Tiering" (Administration en couches), quel est l'objectif du Tier 0 ?

A) Isoler les ordinateurs des stagiaires.

B) Protéger les serveurs de fichiers et d'applications.

C) Regrouper les actifs les plus critiques (DC, Admins du Domaine).

D) Autoriser l'accès internet à tous les serveurs.


### Correction (Réponses)

| Question | Réponse Correcte | Explication Rapide |
| --- | --- | --- |
| 1 | C | La Forêt est la limite de sécurité maximale. |
| 2 | B | Le spraying évite le verrouillage des comptes. |
| 3 | C | BloodHound utilise des graphes pour cartographier le domaine. |
| 4 | B | On reste au même niveau de privilège, on change juste de machine. |
| 5 | C | C'est l'empreinte que Mimikatz cherche à voler. |
| 6 | B | LSASS est le coffre-fort des secrets en mémoire vive. |
| 7 | C | Le SPN lie un service à un compte dont on veut craquer le hash. |
| 8 | B | C'est une faille de confiance qui permet de voler des TGT. |
| 9 | A | On force le serveur à nous parler pour récupérer son hash machine. |
| 10 | C | Le Tier 0 est le "Saint des Saints" à protéger absolument. |
