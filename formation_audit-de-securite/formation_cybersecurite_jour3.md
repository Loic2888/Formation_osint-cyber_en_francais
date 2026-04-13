#### 🎓 Formation Cybersécurité : Jour 3

**Module 3 : L'Armement et l'Exploitation (Création du Piège)**

Objectif du cours : Comprendre comment les attaquants construisent techniquement une fausse page 
de connexion indétectable pour voler non seulement les mots de passe, mais aussi contourner 
la double authentification (MFA/2FA).

### 🕸️ Étape 1 : L'Infrastructure de l'Attaquant

Pour que notre email de phishing fonctionne, le lien sur lequel "Mahendra" (notre cible théorique) 
va cliquer doit mener vers un serveur que nous contrôlons, mais qui a l'air légitime.

1. Le nom de domaine "Typosquatté" :

Un attaquant n'utilise jamais une adresse IP brute. Il achète un nom de domaine qui ressemble à s'y méprendre à l'original.

Domaine réel : yahoo.com

Domaines d'attaque possibles : yahoo-corp-support.com, yaho0.com, yahoo-admin-portal.net

2. Le Serveur (VPS) :
L'attaquant loue un petit serveur virtuel (souvent chez un hébergeur cloud offshore) 
pour quelques euros, sur lequel il va installer ses outils.

### 🎣 Étape 2 : Le clonage et le "Reverse Proxy" (Evilginx2)

C'est ici que la technologie moderne rend les choses terrifiantes.

« Professeur, Mahendra est un ingénieur chez Yahoo. Il a forcément la double authentification 
(MFA) avec un code sur son téléphone. Avoir son mot de passe ne sert à rien ! »

C'est très juste ! Les anciennes techniques de phishing qui consistaient à faire une simple copie 
visuelle de la page web (un faux site en HTML) sont obsolètes. Aujourd'hui, les professionnels utilisent des Reverse 
Proxies d'attaque, dont le plus célèbre est Evilginx2.

**Comment fonctionne la mécanique d'un Reverse Proxy ?**

Au lieu de créer une fausse page, notre serveur d'attaque se place au milieu (Attaque Man-in-the-Middle).

Mahendra clique sur notre lien et arrive sur notre serveur (yahoo-corp-support.com).

Notre serveur affiche en temps réel la VRAIE page de connexion de Yahoo. Il agit comme un miroir transparent.

Mahendra tape son identifiant et son mot de passe.

Notre serveur lit ces identifiants au passage, les enregistre, et les transmet au vrai site Yahoo.

Yahoo demande le code MFA. Notre serveur affiche la vraie demande de code à Mahendra.

Mahendra tape son code MFA depuis son téléphone. Notre serveur le transmet à Yahoo.

Yahoo valide la connexion et renvoie un Cookie de Session (le ticket d'or qui dit "Cet utilisateur est connecté").

Notre serveur vole ce Cookie de Session et redirige Mahendra vers la vraie page d'accueil de Yahoo, comme si de rien n'était.

### 🏴‍☠️ Étape 3 : Le "Game Over" (L'accès au réseau)

Le résultat de l'attaque :
Mahendra n'a rien remarqué d'anormal. Il est connecté à son outil de travail et continue sa journée.

Mais de notre côté, dans notre terminal, nous n'avons pas seulement intercepté son mot de passe. 
Nous avons volé son Cookie de Session.
Nous pouvons maintenant ouvrir notre propre navigateur, injecter ce cookie, et nous serons instantanément 
connectés au portail d'administration de Yahoo en tant que Mahendra, sans même avoir besoin de connaître 
son mot de passe ou son code téléphone, car le cookie prouve à Yahoo que l'authentification a déjà eu lieu.

C'est avec ses accès légitimes que nous pouvons enfin atteindre la base de données mysql-prod identifiée au Jour 1.

### 📝 Bilan de la Formation Complète (Les 3 Jours)

Nous venons de boucler le cycle complet d'une cyberattaque moderne :

Jour 1 (Reconnaissance) : Découverte de l'infrastructure (subfinder, httpx, failles exposées).

Jour 2 (Armement Humain) : OSINT sur le personnel, profilage et création du scénario (Phishing ciblé).

Jour 3 (Exploitation) : Compréhension des mécanismes de contournement MFA via Reverse Proxy pour usurper une identité.

En tant que défenseur (Blue Team), comprendre ce cycle est la seule façon de le briser. 
Vous savez maintenant qu'il faut auditer la surface externe, former les employés, et utiliser des 
clés de sécurité physiques (comme des clés YubiKey) qui résistent aux attaques par Reverse Proxy.
