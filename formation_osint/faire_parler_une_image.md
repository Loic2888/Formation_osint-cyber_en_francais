# Formation OSINT : GEOINT & IMINT (Faire parler une photographie)

**Objectif du cours :** Apprendre à analyser les détails d'une image (IMINT) et à croiser ces données avec des outils cartographiques (GEOINT) pour retrouver le lieu exact, la date, et l'heure où une photographie a été prise, souvent à partir de simples indices visuels.

**🛑 Avertissement Éthique et Légal :** La géolocalisation est une technique puissante. Elle doit être utilisée dans le cadre de défis (CTF type TraceLabs), de journalisme d'investigation (OSINT for Good) ou de cybersécurité. S'en servir pour traquer ou harceler un individu dans la vie réelle (Stalking) est un délit grave.

---

### L'Entonnoir de la Géolocalisation

Retrouver un lieu précis sur une planète de 510 millions de km² demande une méthode rigoureuse. On ne devine pas, on déduit :
1. **Les Métadonnées (L'invisible) :** L'image contient-elle ses propres coordonnées GPS ?
2. **La Recherche Inversée (L'automatisé) :** Les moteurs de recherche connaissent-ils déjà cette image ou ce bâtiment ?
3. **L'Analyse Contextuelle (L'IMINT) :** Que nous disent les ombres, la végétation, l'architecture et les panneaux ?
4. **La Cartographie Avancée (Le GEOINT) :** Comment filtrer la carte du monde pour trouver ce lieu exact ?

---

### Étape 1 : Lire les données invisibles (EXIF)

Quand un smartphone prend une photo, il n'enregistre pas que des pixels. Il enregistre un fichier texte caché (les métadonnées EXIF) qui contient souvent le modèle de l'appareil, l'heure exacte, et surtout, les coordonnées GPS.

L'outil de référence en ligne de commande pour cela est **ExifTool**.

**1. Installation (Linux/WSL) :**
```bash
sudo apt update
sudo apt install libimage-exiftool-perl -y
```

**2. La commande d'analyse :**
(Remplacez photo_cible.jpg par le nom de votre fichier).

```Bash
exiftool photo_cible.jpg
```
*Que chercher dans le résultat ?*

L'outil va cracher des dizaines de lignes. Cherchez spécifiquement :

-GPS Latitude et GPS Longitude : Bingo ! Vous avez les coordonnées exactes à copier dans Google Maps.

-Date/Time Original : La date et l'heure exactes de la prise de vue.

-Software : Indique si l'image a été modifiée sur Photoshop.

**Le Piège (L'OPSEC des réseaux sociaux) :**

Si vous téléchargez une image depuis Facebook, Twitter, WhatsApp ou Instagram, l'outil ne trouvera rien. 
Ces plateformes suppriment automatiquement les données EXIF pour protéger la vie privée de leurs utilisateurs. 
Les métadonnées se trouvent généralement sur les images issues de blogs, d'emails originaux, ou de forums non sécurisés.

### Étape 2 : La Recherche d'Image Inversée (Reverse Image Search)

Si les métadonnées sont vides, on passe à l'analyse visuelle automatisée. L'erreur du débutant est de ne 
compter que sur Google. Un vrai analyste OSINT utilise plusieurs moteurs, car ils ont des algorithmes différents.

L'Arsenal de la recherche inversée :

Google Lens : Excellent pour identifier des monuments célèbres, des objets de consommation, du texte ou des vêtements.

Yandex Images : Le moteur russe. C'est incontestablement le meilleur algorithme au monde pour la reconnaissance 
faciale et l'identification de paysages/bâtiments en Europe de l'Est et en Asie. Si Google échoue, Yandex trouve.

Bing Images : Possède un outil de "rognage" (crop) très puissant pour sélectionner un petit 
détail de l'image (comme un logo sur un t-shirt).

TinEye : Idéal pour savoir si l'image est ancienne. Il vous donne la date de première parution de l'image sur le web.

**La méthode de l'Analyste :**
Ne soumettez jamais l'image entière si elle est encombrée. Rognez la photo pour n'isoler qu'un clocher d'église, 
un logo de magasin, ou une montagne spécifique, et passez ce recadrage dans Yandex et Google.

### Étape 3 : L'IMINT (L'art de l'observation)

Si les moteurs de recherche ne trouvent rien, c'est à vous de jouer les détectives. Vous devez extraire 
chaque "artefact" de l'image.

**1. L'écriture et la Langue :**

*Un panneau flou en arrière-plan ?*

S'il y a des trémas (Ä, Ö, Ü) ou le caractère ß, vous êtes probablement en Allemagne/Autriche.

Des lettres cyrilliques (Д, Ж, П) ? Russie, Ukraine, Bulgarie.

Identifiez le domaine internet sur une vitrine (ex: .pl pour la Pologne, .cz pour la République Tchèque).

**2. L'Infrastructure Routière :**

De quel côté roulent les voitures ? (Royaume-Uni, Australie, Japon, Inde, Afrique du Sud roulent à gauche).

Quelle est la couleur des plaques d'immatriculation ? (Jaunes à l'arrière au Royaume-Uni ou aux Pays-Bas, blanches en France).

Regardez les lignes au sol : Aux États-Unis, la ligne centrale séparant deux voies de sens contraire 
est jaune. En Europe, elle est blanche.

**3. La Chronolocalisation (Trouver l'heure) :**

S'il y a du soleil, il y a des ombres. Si vous identifiez le lieu (ex: la Tour Eiffel), vous pouvez utiliser 
le site SunCalc.org. En alignant l'angle de l'ombre de la photo avec l'outil SunCalc, vous pouvez déterminer à 
la minute près à quelle heure la photo a été prise.

### Étape 4 : Le GEOINT Avancé (Overpass Turbo)

Vous avez analysé l'image et déduit des indices : "Je vois une église avec un toit bleu, il y a un terrain de tennis 
juste à côté, et on est au bord d'une rivière".

Comment trouver ce lieu sans chercher à l'aveugle sur Google Maps ? Avec Overpass Turbo.

*La méthode :*
Overpass Turbo (**overpass-turbo.eu**) est un outil qui permet d'interroger l'immense base de données 
d'OpenStreetMap avec des requêtes spécifiques.

Vous pouvez écrire un script (ou utiliser l'assistant intégré) pour lui demander :

"Trouve-moi tous les terrains de tennis situés à moins de 100 mètres d'un lieu de culte, dans un 
rayon de 50 km autour de Paris."

L'outil va filtrer la carte et vous placer des points rouges uniquement sur les endroits qui correspondent 
exactement à votre description. Il ne vous reste plus qu'à vérifier ces 3 ou 4 points avec 
Google Street View pour confirmer le lieu exact !

### Bilan de l'Enquête Visuelle

La géolocalisation d'une image est un puzzle. Vous avez appris à :

Chercher les coordonnées GPS cachées (ExifTool).

Utiliser les bons algorithmes pour la recherche inversée (Yandex > Google).

Déduire le pays via les panneaux, les routes et l'architecture (IMINT).

Isoler le lieu exact en croisant des données topographiques (Overpass Turbo).

Le GEOINT demande de la patience et une grande culture générale. N'oubliez jamais : Chaque pixel est un indice.
