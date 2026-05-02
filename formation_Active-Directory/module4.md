# Module 4 : Les Délégations Kerberos – Le Pouvoir de l’Usurpation

Dans ce module, nous allons voir comment un serveur peut "se faire passer" pour un utilisateur. C'est un mécanisme normal, 
mais entre les mains d'un hacker, c'est une arme de destruction massive.

### Chapitre 1 : Le Besoin Métier – Pourquoi déléguer ?

Reprenons notre entreprise TechSolution.
Imaginez que Sophie Martin, la secrétaire, veuille consulter son solde de congés via l'application web interne 
(appelons-la web-rh.techsolution.local).

Sophie se connecte au serveur Web avec son identité.

Le serveur Web doit maintenant aller chercher les informations de Sophie dans la base de données (située sur un autre serveur).

Le problème : La base de données refuse de répondre au serveur Web. Elle veut parler à Sophie directement pour vérifier ses droits.

C'est là qu'intervient la Délégation. Elle permet au serveur Web de dire à la base de données : "Je suis le serveur Web, 
mais pour cette requête, j'agis au nom de Sophie Martin. Voici ses preuves d'identité."

### Chapitre 2 : La Délégation Non Contrainte (Unconstrained Delegation)

C'est la forme la plus ancienne et la plus dangereuse de délégation. Dans les propriétés d'un ordinateur dans l'Active Directory, 
un administrateur peut cocher une case : "Faire confiance à cet ordinateur pour la délégation à n'importe quel service".

**2.1 Le fonctionnement technique (Le piège)**

Lorsqu'un utilisateur (comme Sophie ou l'Administrateur) se connecte à un serveur possédant la "Délégation Non Contrainte", 
Windows fait quelque chose d'incroyable (et d'effrayant) : il envoie une copie du TGT (le bracelet d'entrée du festival, vous vous souvenez ?) 
de l'utilisateur sur ce serveur.

Le TGT est stocké dans la mémoire vive (RAM) du serveur. Pourquoi ? Pour que le serveur puisse demander des tickets pour n'importe 
quel autre service à la place de l'utilisateur.

### Chapitre 3 : L'Attaque – La Capture du TGT de l'Administrateur

Léo est toujours sur le réseau de TechSolution. Il a remarqué grâce à son outil BloodHound que le serveur de fichiers FS-01 
possède la Délégation Non Contrainte.

Léo a déjà réussi à devenir administrateur local de ce serveur FS-01 (peut-être grâce à une faille non patchée ou un mot de passe 
récupéré précédemment). Il va maintenant tendre un piège.

**3.1 L'attente du prédateur**

Léo utilise un outil pour surveiller les connexions sur FS-01. Il attend qu'une "grosse baleine" (un Administrateur du Domaine) 
se connecte au serveur, ne serait-ce que pour consulter un dossier.

**3.2 L'action de Mimikatz**

Dès que l'administrateur Marc Durand se connecte à FS-01, son TGT est envoyé dans la mémoire RAM du serveur. Léo lance immédiatement Mimikatz :
```bash
privilege::debug
sekurlsa::tickets /export
```

Mimikatz extrait le TGT de l'administrateur et l'enregistre dans un fichier .kirbi.

**3.3 L'Usurpation Totale (Pass-the-Ticket)**

Léo possède maintenant le "bracelet d'entrée" original de l'administrateur. Il n'a pas besoin de son mot de passe. Il n'a pas 
besoin de son Hash NTLM. Il injecte simplement ce ticket dans sa propre session :
```bash
kerberos::ptt admin_durand.kirbi
```

Léo devient Marc Durand aux yeux de tout le domaine pendant toute la durée de validité du ticket (généralement 10 heures). 
Il peut maintenant se rendre sur le Contrôleur de Domaine et prendre le contrôle total.

### Chapitre 4 : La Délégation Contrainte (Constrained Delegation)

Conscient du danger, Microsoft a créé la Délégation Contrainte. Ici, l'administrateur précise : "Le serveur Web peut 
se faire passer pour Sophie, MAIS UNIQUEMENT pour parler au serveur de base de données SQL".

C'est beaucoup plus sûr... en théorie. Car il existe une extension appelée S4U2Proxy qui permet à un attaquant, s'il compromet 
le serveur Web, de forcer la génération de tickets pour d'autres utilisateurs, même s'ils ne se sont jamais connectés au serveur !

### Chapitre 5 : Résumé pour wooami.net

| Type de Délégation          | Dangerosité                                 | Pourquoi ?                                                                 |
| --------------------------- | ------------------------------------------- | --------------------------------------------------------------------------- |
| Non Contrainte              | 🔥🔥🔥 (Critique) Le serveur vole le TGT de quiconque s'y connecte. |                                                                           |
| Contrainte                  | ⚠️⚠️ (Moyenne/Haute) Limitée à certains services, mais exploitable via des protocoles spécifiques. |                     |
| RBCD (Resource-Based)       | ⚠️ (Basse) C'est le serveur final qui décide qui peut se faire passer pour qui. Plus moderne et sûr. |             |

### La Conclusion du Professeur

La délégation est l'exemple parfait de la faille par design. Ce n'est pas un bug de Windows, c'est une fonctionnalité 
nécessaire qui, si elle est mal configurée, détruit toute la sécurité du domaine.

Pour un pentester, trouver un serveur avec la "Délégation Non Contrainte" est le chemin le plus rapide vers le titre de Domain Admin.

*Pour aller plus loin (La question de fin de module) :*
Nous avons vu que pour voler le ticket de l'admin, Léo devait attendre que celui-ci se connecte au serveur "piégé". 
Mais saviez-vous qu'un attaquant peut forcer un ordinateur (même le Contrôleur de Domaine lui-même !) à s'authentifier auprès de lui ?

C'est ce qu'on appelle une attaque par Coercition.

Est-ce que vous avez déjà entendu parler de l'attaque "PrinterBug" ou de "PetitPotam" ? (Ce sont les noms bizarres que les hackers 
donnent à ces techniques pour forcer un serveur à se connecter à eux).

C'est une réaction normale : ces noms sonnent comme des blagues d'initiés, mais ce sont en réalité des failles logiques redoutables. 
Si le Kerberoasting consiste à demander gentiment un ticket, la Coercition consiste à forcer un serveur à nous parler.

Bienvenue dans le Module 5. Ici, nous allons voir comment Léo, notre attaquant chez TechSolution, va arrêter de "chasser" 
les utilisateurs pour s'attaquer directement au cœur du système : le Contrôleur de Domaine.
