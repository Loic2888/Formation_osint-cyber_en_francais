# Formation OSINT : Guide de Survie et de Configuration Tor (Whonix)

Bonjour. Bienvenue dans cette nouvelle étape de ton apprentissage. 

Aujourd'hui, nous passons de la théorie à la pratique. L'environnement que tu utilises (Whonix) est exigeant, mais c'est le prix à payer pour un anonymat réel. Ce document est ton manuel de référence pour configurer ta zone de travail et comprendre tes premiers points d'entrée sur le réseau Tor.

---

## 🛠️ Partie 1 : Préparation du sas de sécurité

Avant d'explorer, il faut s'assurer que notre équipement est étanche. Sur le réseau Tor, la moindre négligence technique peut compromettre une investigation.

### Étape 1 : Le téléchargement initial du Navigateur
Contrairement à un système classique, Whonix ne pré-installe pas le navigateur Tor. C'est une mesure de sécurité stricte : cela t'oblige à télécharger la version la plus récente au moment exact où tu commences à travailler, évitant ainsi les failles connues des anciennes versions.
1. Ouvre le menu **Applications** (en bas à gauche).
2. Clique sur **Tor Browser Downloader** (le téléchargeur initial).
3. Valide en cliquant sur **Yes** ou **Download**. Le système va chercher la version chiffrée directement à la source.
4. Laisse l'installation se terminer. Le navigateur s'ouvrira de lui-même.

### Étape 2 : L'activation du mode "Safest" (Le Bouclier)
Le web moderne utilise du JavaScript pour fonctionner. Le problème ? Le JavaScript peut être détourné pour analyser la taille de ton écran, ton système d'exploitation, et parfois même faire fuiter ta véritable adresse IP. En OSINT critique, on coupe tout.
1. Regarde en haut à droite de ton navigateur, tu y verras l'icône d'un **Bouclier gris**.
2. Clique dessus, puis sur **Settings** (Paramètres).
3. Dans la liste, choisis le niveau **Safest** (Le plus sûr).
> *Note du formateur : Beaucoup de sites s'afficheront mal ou sans images. C'est parfaitement normal. Nous ne sommes pas là pour le confort visuel, mais pour l'extraction de données brute.*

### Étape 3 : Le test d'étanchéité (Check-Tor)
On ne part jamais en mission sans tester son matériel.
1. Dans la barre d'adresse, tape exactement : `https://check.torproject.org`
2. Appuie sur Entrée.
3. **Le diagnostic :** L'écran doit t'afficher un gros texte vert indiquant *"Congratulations. This browser is configured to use Tor."* Cela signifie que ton adresse IP d'origine est masquée et remplacée par celle d'un serveur relais (un "nœud de sortie") situé quelque part dans le monde.

---

## 🧭 Partie 2 : Cartographie des premiers points d'entrée (.onion)

Le Dark Web n'a pas de plan officiel. Il n'y a pas de grand algorithme Google pour t'apporter la bonne réponse sur un plateau. Voici tes premiers outils d'investigation, à copier-coller dans ta barre d'adresse. 

### 1. Les Moteurs de Recherche (La base de l'exploration)

* **DuckDuckGo Onion**
  * `https://duckduckgogg42xjoc72x3sjasowoarfbgcmvfima3ogtwqm7e6dyd.onion/`
  * **Ce qu'on y trouve :** Attention, idée reçue fréquente : ce moteur ne cherche pas sur le Dark Web. Il te permet de faire des recherches sur le web normal (Clearnet), mais de manière totalement anonyme. Utile pour faire des recherches sur une cible sans que Google n'enregistre ton adresse IP.
* **Torch**
  * `http://xmh57jrbeic0mq60.onion/`
  * **Ce qu'on y trouve :** L'un des plus vieux moteurs de recherche du Dark Web. Il indexe de vraies pages `.onion`. Tu vas y trouver beaucoup de liens morts (car les sites du Dark Web apparaissent et disparaissent vite). C'est brut, chaotique, mais essentiel pour chercher un mot-clé précis (un pseudo, une adresse mail) dans les bas-fonds.
* **Ahmia**
  * `http://juhanurmih4p6wwt.onion/`
  * **Ce qu'on y trouve :** Un excellent moteur pour débuter. Il indexe le réseau Tor, mais il a la particularité de bloquer activement les sites liés à la pédocriminalité. Cela te permet de faire tes recherches de données (fuites, forums de hackers) de manière beaucoup plus "propre" et sécurisée sur le plan légal et moral.

### 2. Les Annuaires (Les listes de départ)

* **The Hidden Wiki**
  * `http://zqktlwiu3fadr05j.onion/wiki/index.php/Main_Page`
  * **Ce qu'on y trouve :** C'est une page d'accueil modifiée par la communauté, listant des sites par catégories. **Avertissement de l'analyste :** C'est ici que tu trouveras 90% des arnaques. Vendeurs de fausses cartes d'identité, tueurs à gages fictifs, multiplicateurs de Bitcoins... C'est un excellent terrain d'entraînement pour apprendre à identifier un site frauduleux (Scam).
* **Trantor / Imperial Library**
  * `http://kx5thpx2olielkihfyo4jgcmwxh7oj35oy6shs6nsqvov3scvppf6cqd.onion/`
  * **Ce qu'on y trouve :** Une immense archive militante. On y trouve des manuels de cybersécurité introuvables ailleurs, des manifestes politiques, des bases de données déclassifiées et des livres censurés dans certains pays. Une vraie mine d'or pour l'analyste.

### 3. Les Outils de Couverture et d'Alerte

* **ProtonMail**
  * `https://protonmailrmez3lotccipshtioleeabbu4u3sl6as6z7o66bhv7y7v5id.onion/`
  * **Ce qu'on y trouve :** C'est l'outil indispensable pour la création de ton "Sock Puppet" (ta fausse identité). Passer par le lien `.onion` de ProtonMail te permet de créer une adresse email d'investigation sans jamais lier l'inscription à l'adresse IP de ton domicile.
* **SecureDrop**
  * `http://sdolvtfnpvsysgoez33gn6ogcyasfqpuvzhvbh3bdls66st7ru7vjxid.onion/`
  * **Ce qu'on y trouve :** Ce n'est pas un site de lecture, mais une interface chiffrée utilisée par des ONG et de grands journaux pour permettre aux lanceurs d'alerte (Whistleblowers) de leur transmettre des documents confidentiels sans risquer de se faire identifier par leur gouvernement.

### 4. Les Forums de Hacking (Le cœur de l'information)

* **BreachForums**
  * `http://breachf5ykfuyogm5vstfgn6pccu3uovks7bxlmpxo5oyunlxk5ihaid.onion/`
  * **Ce qu'on y trouve :** C'est le carrefour mondial des fuites de données. Lorsqu'une entreprise ou un service est piraté, les bases de données (emails, mots de passe, informations personnelles) sont souvent publiées ici en premier. C'est une ressource indispensable pour l'OSINT liée aux fuites (Leaks).

* **XSS.is / Exploit.in**
  * `http://xssforum3n36vks656v3aooov5onow2mbs5n3m3v4y2f5v5n5n5n5n5n.onion/`
  * **Ce qu'on y trouve :** Ces forums russes sont les piliers techniques du milieu. On y trouve des discussions sur les vulnérabilités de type "0-day", la vente d'accès à des réseaux d'entreprise et des malwares sophistiqués. L'ambiance y est très professionnelle et "business-oriented".

* **Dread**
  * `http://dreadytof462v62bsh5p6v6rsc7spsscbtuoyk3iv6jowfayid33xozad.onion/`
  * **Ce qu'on y trouve :** Souvent décrit comme le "Reddit" du Dark Web. Grâce à ses sous-communautés (sub-dreads) comme `/d/hacking` ou `/d/cybersecurity`, c'est l'endroit idéal pour suivre l'actualité des cyber-attaques, lire des Retours d'Expérience (REX) ou trouver des tutoriels techniques.

---


## 🚨 Les 3 Règles d'Or de l'Analyste

1. **Ne redimensionne jamais ta fenêtre :** Laisse la fenêtre du navigateur exactement à la taille qu'elle avait en s'ouvrant. La mettre en plein écran permet aux sites de connaître la résolution de ton écran physique. C'est une empreinte (Browser Fingerprinting).
2. **La règle du portefeuille fermé :** En OSINT, on observe, on note, on ne participe pas. N'achète jamais rien, ne transfère jamais de cryptomonnaie, même pour "tester". 
3. **Accepte la lenteur :** Ton signal est chiffré en trois couches et rebondit sur trois serveurs différents à travers le monde avant d'atteindre le site. La lenteur est la preuve que la sécurité fonctionne. Patience.
