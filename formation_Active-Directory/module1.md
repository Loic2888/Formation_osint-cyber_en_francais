# Fondamentaux de l'Active Directory

Bienvenue dans ce module de formation dédié à l'Active Directory (AD). Ce guide est conçu pour vous fournir une base solide, 
tant théorique que pratique, afin de comprendre pourquoi ce service est le pilier des réseaux Windows en entreprise et une 
cible privilégiée en cybersécurité.

**1. Qu'est-ce que l'Active Directory ?**

L'Active Directory est un service d'annuaire développé par Microsoft. Il permet de centraliser la gestion des ressources d'un réseau. 
Imaginez-le comme le cerveau du système d'information : il sait qui est chaque utilisateur, à quel ordinateur il appartient, et quels 
sont ses droits d'accès.

Les trois piliers de l'AD :
-Authentification : Vérifier l'identité de l'utilisateur (ex: login/mot de passe).
-Autorisation : Vérifier si l'utilisateur a le droit d'accéder à une ressource (ex: un dossier partagé).
-Administration centralisée : Gérer 10 000 ordinateurs aussi facilement qu'un seul.

**2. Structure Logique de l'AD**

Pour organiser des milliers d'objets, l'AD utilise une hiérarchie stricte :
|Composant|Description|
 
|Objets|Utilisateurs, ordinateurs, imprimantes, groupes.|
|Unités d'Organisation (OU)|Conteneurs permettant de classer les objets par département ou lieu géographique.|
|Domaine|Frontière administrative et de sécurité (ex: entreprise.local).|
|Forêt|Le niveau le plus élevé, regroupant un ou plusieurs domaines.|

**3. Le Contrôleur de Domaine (DC)**

Le Domain Controller est le serveur qui héberge le service Active Directory. C'est lui qui stocke la base de données NTDS.dit 
contenant tous les secrets (mots de passe hachés, configurations).
Note importante : Le DNS (Domain Name System) est indispensable à l'AD. Sans un DNS fonctionnel, les clients ne peuvent 
pas trouver le DC pour se connecter.

**4. Groupes et Stratégies de Groupe (GPO)**

### Les Groupes de Sécurité
On ne donne jamais de droits directement à un utilisateur. On crée un groupe (ex: "RH_Full_Access"), 
on y ajoute l'utilisateur, et on donne les droits au groupe.
### Les GPO (Group Policy Objects)
C'est l'outil le plus puissant pour un administrateur. Une GPO permet d'imposer des configurations 
sur des milliers de machines simultanément :
-Forcer un fond d'écran spécifique.
-Désactiver les ports USB sur tous les PC.
-Imposer une complexité de mot de passe (ex: 12 caractères minimum).

**5. Commandes de base pour l'exploration (Admin & Pentest)**

En tant que futur expert en cybersécurité, vous devez savoir interroger l'AD via la ligne de commande.
Via l'invite de commande (CMD)

# Lister les utilisateurs du domaine
net user /domain

# Obtenir des détails sur un utilisateur spécifique
net user Alice /domain

# Lister les administrateurs du domaine
net group "Domain Admins" /domain


Via PowerShell (Module ActiveDirectory)
# Trouver tous les ordinateurs du domaine
Get-ADComputer -Filter *

# Trouver les utilisateurs dont le mot de passe n'expire jamais (faille potentielle)
Get-ADUser -Filter 'PasswordNeverExpires -eq $true'

# Lister les Unités d'Organisation
Get-ADOrganizationalUnit -Filter *


**6. Conclusion : Vers la Sécurité**

Maîtriser ces concepts est la première étape. Dans la suite de votre parcours sur wooami.net, nous verrons 
comment ces mêmes structures (GPO mal configurées, groupes trop permissifs) deviennent des vecteurs d'attaque 
pour un pentester.
