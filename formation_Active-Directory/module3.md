# Module 3 : Kerberos – Le Système de Tickets et l'Attaque "Kerberoasting"

Dans le module précédent, nous avons vu comment voler un Hash NTLM dans la mémoire vive. Mais Microsoft a renforcé ses défenses.
Aujourd'hui, dans un réseau moderne, le protocole roi, c'est Kerberos. Pour un pentester, comprendre Kerberos, c'est comprendre 
comment l'Active Directory respire.

### Chapitre 1 : NTLM vs Kerberos – Le Duel des Protocoles

Avant d'attaquer, il faut comprendre l'outil.

NTLM est un protocole de "Challenge-Response". C'est comme si le serveur disait : "Prouve-moi que tu es toi en me montrant ton 
empreinte (le Hash)". C'est simple, mais c'est ce qui permet l'attaque Pass-the-Hash.

Kerberos est un protocole basé sur des Tickets. C'est beaucoup plus sophistiqué. On ne montre jamais son mot de passe au serveur final. 
On utilise un "Tiers de confiance" : le Contrôleur de Domaine (DC).

**L'Analogie du Festival (Pour comprendre Kerberos)**

Imaginez que vous allez dans un festival de musique (le réseau de l'entreprise) :

L'Entrée (Authentification) : Vous montrez votre carte d'identité (votre mot de passe) au guichet principal (le DC).

Le Bracelet (TGT) : Le guichet vous donne un bracelet spécial (appelé TGT - Ticket Granting Ticket). Ce bracelet prouve que 
vous êtes bien "Jean-Michel".

Le Stand de Boisson (Le Service) : Vous voulez une bière (accéder à une base de données). Le serveur de bière ne veut pas voir 
votre carte d'identité. Il veut un Ticket spécifique pour une bière.

Le Ticket de Service (TGS) : Vous retournez au guichet avec votre bracelet (TGT) et demandez : "Je veux un ticket pour 
le stand de bière". Le guichet vous donne un petit coupon (le TGS - Ticket Granting Service).

La Consommation : Vous donnez le coupon (TGS) au serveur de bière. Il vérifie que le coupon est authentique et vous sert.

### Chapitre 2 : La Faille dans le Système – Qu'est-ce que le Kerberoasting ?

Revenons à notre attaquant, Léo. Il est toujours sur le PC de la secrétaire, Sophie Martin. Il ne peut pas voler le Hash NTLM 
de l'admin car la mémoire est protégée. Par contre, en tant qu'utilisateur du domaine, il a un droit incroyable : il peut 
demander des tickets pour n'importe quel service du réseau.

**2.1 Le concept de SPN (Service Principal Name)**

Dans l'Active Directory, certains comptes utilisateurs (souvent des comptes de service comme svc_sql ou svc_backup) ont ce 
qu'on appelle un SPN. C'est une étiquette qui dit : "Ce compte fait tourner le service SQL sur tel serveur".

**2.2 La vulnérabilité technique**

Lorsqu'un utilisateur demande un ticket (TGS) pour un service (ex: SQL), le Contrôleur de Domaine génère ce ticket et le chiffre 
avec le mot de passe du compte de service.
Léo, avec son compte de stagiaire ou de secrétaire, peut dire au DC : "Hé, je voudrais accéder au serveur SQL, donne-moi un ticket !".
Le DC lui envoie le ticket. Léo reçoit ce ticket dans sa propre mémoire vive.

Le problème : Le ticket contient une partie chiffrée avec le mot de passe du compte svc_sql. Léo peut extraire ce ticket de sa mémoire, 
le sortir du réseau, et essayer de le "déchiffrer" chez lui, sur sa machine de guerre, en testant des millions de mots de passe par seconde.

**Chapitre 3 : L'Exécution Technique – La Chute de TechSolution**

Léo va maintenant passer à l'action. Il n'a plus besoin d'être "Admin" pour cette étape. Un simple compte utilisateur suffit.

**3.1 Lister les cibles (Énumération des SPN)**

Léo veut savoir quels comptes de service sont "Kerberoastables". Il utilise un script PowerShell (comme PowerView) ou un outil comme 
GetUserSPNs.py de la suite Impacket.
```bash
GetUserSPNs.py techsolution.local/s.martin:motdepasse -dc-ip 10.10.20.50 -request
```

**3.2 L'Extraction du "Hash" Kerberos**

L'outil interroge le DC et récupère les tickets de service. Ces tickets apparaissent sous une forme cryptique dans la console. 
Ce n'est pas un Hash NTLM, c'est un Hash Kerberos 5 TGS-REP.

Exemple de ce que Léo voit :
$krb5tgs$23$*svc_sql$TECHSOLUTION.LOCAL$MSSQLSvc/db01.techsolution.local:1433*$6f72...

**3.3 Le Craquage Hors-Ligne (Offline Cracking)**

Léo copie ce hash et rentre chez lui. Il utilise Hashcat, l'outil de craquage le plus puissant au monde. Il utilise une liste de mots 
de passe courants (la célèbre liste rockyou.txt).
```bash
hashcat -m 13100 hash_kerberos.txt rockyou.txt
```

Si le compte svc_sql a un mot de passe un peu faible comme SqlAdmin2022!, Hashcat le trouvera en quelques minutes.

**Chapitre 4 : Pourquoi est-ce une catastrophe ?**

Léo possède maintenant le mot de passe en clair du compte svc_sql.
À ton avis, qu'est-ce qu'un compte de service a le droit de faire ?

Souvent, les comptes de service sont Administrateurs Locaux des serveurs sur lesquels ils tournent.

Parfois, par erreur de configuration, ils sont même membres du groupe Domain Admins.

Léo se connecte maintenant avec le compte svc_sql. Il n'est plus un stagiaire, il n'est plus une secrétaire, il est un Service Système. 
Il peut maintenant accéder à toutes les données de la base de données SQL (fiches de paie, clients, secrets industriels).

### Chapitre 5 : Résumé détaillé pour wooami.net

|Concept|Explication Profonde|
|KDC (Key Distribution Center)|C'est le rôle du Contrôleur de Domaine dans Kerberos. C'est lui qui distribue les tickets.|
|TGT (Ticket Granting Ticket)|Votre "bracelet" d'entrée. Il prouve que vous avez fourni le bon mot de passe au début.|
|TGS (Ticket Granting Service)|Le coupon pour un service précis. C'est lui qu'on intercepte pour le Kerberoasting.|
|SPN (Service Principal Name)|L'identifiant unique d'un service dans l'AD. Sans SPN, pas de Kerberoasting possible.|
|Hashcat|L'outil qui permet de tester des millions de mots de passe sur le ticket volé.|

### Conclusion du Professeur

Le Kerberoasting est une attaque redoutable car :

Elle est indétectable par la plupart des antivirus (car demander un ticket est un comportement normal de Windows).

Elle ne nécessite aucun privilège élevé au départ.

Elle repose sur la faiblesse des mots de passe choisis par les administrateurs pour les comptes de service.

*La question pour passer au module suivant :*
Léo a maintenant le mot de passe d'un compte de service. Mais imaginons que le mot de passe soit ultra-complexe 
(30 caractères aléatoires) et impossible à cracker. Léo est bloqué... à moins qu'il n'utilise une autre faiblesse de l'Active 
Directory liée aux Délégations.

Sais-tu ce qu'est une "Délégation" dans l'Active Directory ? 
*C'est quand un serveur a le droit de se faire passer pour toi auprès d'un autre serveur.*

C'est ici que nous entrons dans l'un des recoins les plus sombres et les plus puissants de l'Active Directory. 
Si le Kerberoasting (Module 3) est une attaque contre la force des mots de passe, la Délégation est une attaque contre 
la logique de confiance du réseau.

En tant que professeur, je vais vous expliquer pourquoi cette fonctionnalité, indispensable au bon fonctionnement d'une entreprise, 
est un véritable cadeau pour un attaquant comme Léo. Suite dans le module 4!
