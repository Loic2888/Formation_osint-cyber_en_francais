# 🎓 Formation OSINT : Le Dark Web (Étape 1 - Créer un Environnement Sécurisé)

**Objectif du cours :** Comprendre les risques liés à la navigation sur le réseau Tor (Darknet) et apprendre à configurer une machine étanche et amnésique pour mener des investigations sans compromettre son ordinateur personnel ou son identité.

**🛑 Avertissement Éthique et Sécurité :** Le Dark Web héberge le meilleur (journalisme sous censure, lanceurs d'alerte) comme le pire. En tant qu'analyste OSINT, votre règle d'or est stricte : **Regarder, mais ne jamais interagir.** Ne créez pas de compte avec des informations vous liant à la réalité, ne téléchargez aucun fichier, et n'achetez absolument rien. 

---

### 🧅 L'Entonnoir de l'OPSEC (Sécurité Opérationnelle)
Surfer sur le Dark Web avec votre navigateur habituel est un suicide numérique. Le réseau Tor est conçu pour l'anonymat, mais si votre système d'exploitation (Windows/Mac) bavarde en arrière-plan, cet anonymat est brisé. 
Nous devons isoler l'environnement en 3 niveaux :
1. **Le Navigateur :** Utiliser exclusivement le Tor Browser.
2. **Le Comportement :** Neutraliser le pistage (Fingerprinting).
3. **Le Système (Niveau Pro) :** Utiliser un système d'exploitation amnésique (Tails).

---

### 🛠️ Étape 1 : L'Outil de Base (Le Tor Browser)
Le réseau Tor fonctionne en faisant rebondir votre connexion sur trois serveurs aléatoires dans le monde (les "nœuds") avant d'atteindre le site cible. Pour y accéder, il faut un navigateur spécifique.

**1. L'Installation stricte :**
* Ne téléchargez **JAMAIS** le Tor Browser sur un site tiers (clubic, softonic, etc.).
* Allez **uniquement** sur le site officiel : `https://www.torproject.org/`.
* Téléchargez et installez la version correspondant à votre système.

**2. La Règle du plein écran (Canvas Fingerprinting) :**
Lorsque vous ouvrez Tor, ne mettez **jamais** la fenêtre en plein écran. 
*Pourquoi ?* Les sites web malveillants peuvent mesurer la taille exacte de votre écran au pixel près. Cette taille, combinée à d'autres données, crée une "empreinte digitale" unique de votre machine qui permet de vous pister. Tor Browser s'ouvre par défaut dans une taille standardisée partagée par des millions d'utilisateurs. Laissez-le ainsi.

---

### 🛡️ Étape 2 : Le Verrouillage (Désactiver JavaScript)
Le plus grand ennemi de l'anonymat sur le Dark Web n'est pas le FBI, c'est **JavaScript**. C'est un langage de programmation qui s'exécute directement sur votre ordinateur et qui peut être détourné pour révéler votre véritable adresse IP.

**Comment le désactiver sur Tor Browser :**
1. Cliquez sur l'icône de bouclier en haut à droite de la barre d'adresse.
2. Cliquez sur **Paramètres (Settings)**.
3. Changez le niveau de sécurité de *Standard* à **Le plus sûr (Safest)**.

*Le Résultat :* JavaScript est totalement coupé. Beaucoup de sites du "Surface Web" s'afficheront mal, mais les sites en `.onion` du Dark Web sont conçus pour fonctionner sans. Vous êtes désormais protégé contre 99% des attaques de désanonymisation.

---

### 👻 Étape 3 : L'Environnement Pro (Tails OS)
Si vous enquêtez sur des forums de ransomwares ou des marchés illégaux, installer Tor sur votre Windows personnel ne suffit plus. Windows garde des traces (logs) de ce que vous faites. Les professionnels de l'OSINT utilisent **Tails (The Amnesic Incognito Live System)**.

**Qu'est-ce que Tails ?**
C'est un système d'exploitation Linux complet qui tient sur une simple clé USB.
* **Amnésique :** Il s'exécute uniquement dans la mémoire vive (RAM) de votre ordinateur. Dès que vous éteignez le PC ou retirez la clé USB, la RAM se vide. Il ne reste absolument **aucune trace** physique de votre activité.
* **Hermétique :** Tails force *toutes* les connexions Internet à passer par le réseau Tor. Si un programme essaie de se connecter normalement, Tails le bloque.

**Comment créer votre station d'investigation :**
1. Prenez une clé USB de 8 Go (minimum) vierge.
2. Allez sur `https://tails.net/`.
3. Téléchargez l'image du système et utilisez un logiciel comme **BalenaEtcher** ou **Rufus** pour flasher l'image sur votre clé USB.
4. Éteignez votre PC, branchez la clé, et rallumez-le en démarrant (Boot) sur la clé USB.
5. Bienvenue dans votre forteresse jetable.

---

### 📝 Bilan de la Préparation
Votre OPSEC est désormais en place. 
* Si vous faites une petite recherche rapide, votre **Tor Browser en niveau "Safest" et en fenêtre réduite** fera l'affaire.
* Si vous partez en investigation profonde sur des acteurs de la menace, vous démarrerez votre ordinateur sur votre **clé USB Tails amnésique**.

Maintenant que vous avez la tenue de plongée, il est temps d'apprendre à nager dans l'obscurité. Dans le prochain module, nous verrons comment trouver des liens `.onion` (qui ne sont pas référencés sur Google) et comment utiliser les moteurs de recherche spécifiques au Dark Web.

---

### ⚠️ FAQ de l'Expert : Pourquoi ne pas installer Tails sur une Machine Virtuelle (VirtualBox) ?

Il est techniquement possible de faire tourner l'image ISO de Tails dans une machine virtuelle (VM) comme Oracle VirtualBox ou VMware. Cependant, **c'est une faille de sécurité majeure pour votre OPSEC**, et ce pour trois raisons :

**1. Le système hôte voit tout (Keyloggers)**
Une machine virtuelle tourne "au-dessus" de votre système d'exploitation principal (Windows ou macOS). Si votre Windows est infecté par un malware ou un enregistreur de frappe (Keylogger), ce malware peut voir tout ce que vous tapez dans votre machine virtuelle Tails. L'isolation n'est pas totale.

**2. La fin de l'amnésie (Le fichier Swap)**
Tails est conçu pour ne tourner que dans la mémoire vive (RAM) afin de ne laisser aucune trace physique. Mais Windows utilise un système appelé "Fichier d'échange" (Swap ou `pagefile.sys`). Si votre PC manque de RAM, Windows va prendre une partie de la mémoire de la machine virtuelle Tails et l'écrire **sur votre disque dur physique**. Vos données d'investigation Dark Web se retrouvent gravées sur votre disque, détruisant le concept même d'amnésie.

**3. Les traces de virtualisation**
VirtualBox crée des logs (journaux d'événements) sur votre disque dur indiquant à quelle heure la machine Tails a été allumée et éteinte.

---

### 💡 L'Alternative pour les Machines Virtuelles : Whonix

Si l'utilisation d'une clé USB bootable est trop contraignante et que vous souhaitez absolument mener vos investigations OSINT depuis une Machine Virtuelle sur votre PC principal, n'utilisez pas Tails. Utilisez **Whonix**.

**Qu'est-ce que Whonix ?**
C'est un système d'exploitation (également gratuit) spécialement conçu **pour** tourner dans une machine virtuelle (VirtualBox). 

Il fonctionne avec un système génial de **Double VM** :
1. **Whonix-Gateway :** Une première machine virtuelle qui ne fait qu'une seule chose : elle se connecte au réseau Tor.
2. **Whonix-Workstation :** Une deuxième machine virtuelle (votre bureau d'enquête) qui n'a pas accès à Internet. Son seul moyen de communiquer avec l'extérieur est de passer par le Gateway.

**Le résultat de sécurité (OPSEC) :** Même si vous téléchargez un virus sur la Workstation ou qu'un hacker prend le contrôle de votre VM d'investigation, il lui sera **impossible** de découvrir votre véritable adresse IP, car la Workstation ignore totalement comment elle est connectée à Internet.