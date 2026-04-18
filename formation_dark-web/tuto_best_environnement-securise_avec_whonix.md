# 🎓 Formation OSINT : Le Dark Web (L'Environnement Virtuel avec Whonix)

**Objectif du cours :** Déployer une architecture sécurisée à double machine virtuelle (Whonix-Gateway et Whonix-Workstation) sur Oracle VirtualBox pour enquêter sur le réseau Tor avec un anonymat maximal, sans quitter son système d'exploitation principal.

**🛑 Avertissement OPSEC :** Bien que Whonix soit extrêmement robuste contre les fuites d'adresse IP, votre machine hôte (Windows/macOS) doit être saine. Si votre ordinateur principal est compromis par un logiciel espion, tout ce que vous taperez dans Whonix pourra être lu.

---

### Comprendre l'Architecture Whonix (La Double Barrière)
Contrairement à un système classique, l'installation de Whonix va créer **deux** machines virtuelles distinctes sur votre VirtualBox. C'est ce qui fait sa force :

1. **La Whonix-Gateway (Le Pare-feu) :** C'est une machine virtuelle qui n'a qu'un seul rôle : se connecter à Internet et forcer tout le trafic à passer par le réseau Tor. Elle n'a pas de navigateur web.
2. **La Whonix-Workstation (Le Bureau) :** C'est la machine sur laquelle vous allez travailler. Elle est totalement coupée d'Internet. Son seul moyen de communiquer avec le monde extérieur est un câble virtuel relié à la Gateway. 

*Conclusion : Même si un pirate infecte votre Workstation, il ne pourra jamais découvrir votre vraie adresse IP, car la Workstation ne la connaît pas elle-même !*

---

### Étape 1 : Les Prérequis et le Téléchargement
Pour commencer, vous avez besoin d'un logiciel de virtualisation et du fichier d'installation de Whonix.

**1. Le Logiciel Hôte :**
* Installez **Oracle VirtualBox** sur votre ordinateur (disponible gratuitement sur `virtualbox.org`).

**2. Le Fichier Whonix :**
* Rendez-vous **exclusivement** sur le site officiel : `https://www.whonix.org/`.
* Cliquez sur **Download**, choisissez **VirtualBox**, puis téléchargez la version **Whonix LXQt**.
* Vous obtiendrez un gros fichier au format `.ova` (environ 2 à 3 Go). Ce fichier contient les deux machines prêtes à l'emploi.

---

### Étape 2 : L'Importation dans VirtualBox
L'équipe de Whonix a rendu le processus incroyablement simple. Vous n'avez pas à configurer les disques durs ou les cartes réseau manuellement.

1. Ouvrez **VirtualBox**.
2. Allez dans le menu **Fichier** > **Importer un appareil virtuel**.
3. Sélectionnez le fichier `.ova` que vous venez de télécharger.
4. Cliquez sur **Suivant**. Un tableau récapitulatif s'affiche (vous verrez qu'il s'apprête à créer deux machines). 
5. Cliquez sur **Importer** et acceptez les termes du contrat de licence.
6. Patientez quelques minutes pendant que VirtualBox décompresse l'architecture.

---

### Étape 3 : La Séquence de Démarrage (L'Ordre est crucial)
Vous avez maintenant deux nouvelles machines dans votre liste VirtualBox. **L'ordre d'allumage est la règle la plus importante de Whonix.**

**1. Allumer la Gateway en premier :**
* Sélectionnez `Whonix-Gateway-XFCE` et cliquez sur **Démarrer**.
* Lors du premier démarrage, un assistant de configuration (Setup Wizard) apparaît.
* Cochez la case **Connect** pour autoriser la machine à se connecter au réseau Tor, puis cliquez sur **Next**.
* Une fenêtre *Anon Connection Wizard* s'ouvre. Choisissez de vous connecter directement au réseau public Tor.
* Laissez la machine ouverte et réduisez la fenêtre. Elle doit tourner en arrière-plan en permanence.

**2. Allumer la Workstation en second :**
* Sélectionnez `Whonix-Workstation-XFCE` et cliquez sur **Démarrer**.
* Lors du premier démarrage, acceptez les mêmes écrans d'information (Setup Wizard).
* Un outil appelé `Systemcheck` va se lancer automatiquement pour vérifier que la connexion avec la Gateway fonctionne et que le réseau Tor est opérationnel.

---

### Étape 4 : Les Règles d'Or post-installation (OPSEC)
Votre bureau d'investigation est prêt, mais comme tout système fraîchement installé, il y a deux actions obligatoires à réaliser avant de lancer votre première recherche.

**1. Changer les mots de passe par défaut :**
Par défaut, le nom d'utilisateur est `user` et le mot de passe est `changeme`. Il faut impérativement modifier ce mot de passe sur **les deux** machines (Gateway et Workstation).
* Ouvrez le terminal (l'icône noire en bas de l'écran) sur chaque machine.
* Tapez la commande suivante :
```bash
passwd
```
Entrez l'ancien mot de passe (changeme), puis tapez votre nouveau mot de passe deux fois (rien ne s'affiche quand vous tapez, c'est normal sous Linux).

**2. Mettre à jour le système :**
En cybersécurité, on ne travaille jamais avec des logiciels obsolètes. Whonix possède une commande simplifiée pour tout mettre à jour d'un coup. Sur la Workstation, ouvrez le terminal et tapez :

```Bash
upgrade-nonroot
```
Le système va télécharger les dernières mises à jour du navigateur Tor et des outils de sécurité. Appuyez sur Y (Yes) quand il vous le demande.

### Bilan de la configuration
Félicitations. Vous possédez désormais l'un des environnements numériques les plus sécurisés au monde.

Pour vos investigations OSINT sur le Dark Web, vous n'utiliserez que la Workstation. Le Tor Browser y est déjà installé et configuré au niveau de sécurité maximum. La Gateway, elle, reste silencieusement en arrière-plan pour assurer le chiffrement et le rebond de toutes vos données.