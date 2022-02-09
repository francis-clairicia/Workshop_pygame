# Workshop pygame
![pygame-logo](https://raw.githubusercontent.com/pygame/pygame/main/docs/pygame_logo.svg)

## C'est quoi pygame ?
`pygame` est une bibliothèque python permettant de créer des interfaces graphiques simplement avec Python !

Pour se faire, les développeurs se servent de [la SDL](https://www.libsdl.org/). Dites vous que c'est un export de ce framework sur Python (de la même manière que la CSFML avec la SFML).

Tous les jours, de nouveaux projets sont publiés sur [le site communautaire](https://www.pygame.org/tags/all), que ce soit jeux, moteurs de jeux, astuces de développement, etc.

Avantages de pygame :
- Compatible avec Python 3
- Beaucoup d'utilisateurs
- Développement actif ([pygame.org](https://www.pygame.org/))
- Portable sur les différents OS
- Plutôt facile d'utilisation
- Utilise [la SDL](https://www.libsdl.org/), disponible dans beaucoup de langages...

## Installation
Tout d'abord, vérifiez que Python 3 (de préférence >= 3.8) soit installé
```sh
python --version
```
Ensuite installez le package `pygame`
```sh
python -m pip install --user --upgrade pygame
```
Pour voir si ça a marché
```
$> python
Python 3.10.1 (main, Dec 18 2021, 23:53:45) [GCC 11.1.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import pygame
pygame 2.1.2 (SDL 2.0.16, Python 3.10.1)
Hello from the pygame community. https://www.pygame.org/contribute.html
>>> 
```
(Bien évidement, la version de Python et de pygame peut différer)

## Ouvrez votre première fenêtre
Vous allez travailler dans le fichier `window.py` du dépôt, il y a déjà un `main` pour commencer

### Les modules de pygame

Déjà si on va dans [la documentation](https://www.pygame.org/docs/) de pygame, on voit qu'il y a pas mal de choses en fait !

Pas de panique. En réalité, `pygame` est composé de plusieurs modules. On peut en utiliser qu'un seul comme ceci...
```py
import pygame.display
```
...comme tout prendre comme cela
```py
import pygame
```
Les modules les plus importants:
- [pygame.display](https://www.pygame.org/docs/ref/display.html): Module pour la gestion des fenêtres
- [pygame.event](https://www.pygame.org/docs/ref/event.html): Module pour la gestion des évenements
- [pygame.draw](https://www.pygame.org/docs/ref/draw.html): Module contenant des fonctions de base pour dessiner des formes géométriques
- [pygame.image](https://www.pygame.org/docs/ref/image.html): Module pour la création d'image
- [pygame.mixer](https://www.pygame.org/docs/ref/mixer.html): Module pour la gestion des sons
- [pygame.mouse](https://www.pygame.org/docs/ref/mouse.html): Module pour interagir directement avec la souris
- [pygame.time](https://www.pygame.org/docs/ref/time.html): Module pour le _time monitoring_

> Wow ça fait beaucoup ! :(

Je me doute, mais on va pas s'attarder sur tous, c'est à vous de vous y intéresser :)

### La boucle principale
Ok, à partir de maintenant, vous êtes dans le `main` du `window.py`

Tout d'abord, il faut initialiser `pygame`
```py
pygame.init()
```
Afin de notamment initialiser la SDL.

Ensuite on va créer un fenêtre de taille 640x480
```py
screen_size = (640, 480)
window = pygame.display.set_mode(screen_size)
```
La fonction `set_mode` contenu dans `pygame.display` va instancier une fenêtre et nous retourner une [Surface](https://www.pygame.org/docs/ref/surface.html)
(je vais expliquer plus loin ce que c'est et comment on s'en sert).

Retenez que vous n'avez pas d'objet `Window` par exemple (comme la `sfRenderWindow` de la CSFML).
La réelle fenêtre est précieusement gardée par `pygame.display` :)

Maintenant on va créer une boucle afin de maintenir le programme en exécution
```py
looping = True
while looping:
    continue
```

Ok, du coup votre code devrait ressembler plus ou moins à ça
```py
def main():
    pygame.init()

    screen_size = (640, 480)
    window = pygame.display.set_mode(screen_size)
    
    looping = True
    while looping:
        continue
```

Si vous lancez le programme vous aurez une jolie fenêtre noire ! (Faites Ctrl+C dans le terminal pour l'arrêter)

### Les évenements
C'est cool on a une fenêtre, maintenant ce serait de pouvoir _au moins_ fermer la fenêtre :)

C'est là qu'intervient le module `pygame.event`. On va le solliciter dans notre `while` pour arrêter la boucle normalement.
```py
looping = True
while looping:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            looping = False
    continue
```
> Un peu d'explications ?

Bien sûr ! `pygame.event.get` va récupérer tous les évenements reçu par notre fenêtre et nous la donner sous la forme d'une liste.

(Quand je vous ai dit que `pygame` garde la fenêtre pour elle, c'est surtout que c'est une variable globale partagé par tout le projet ! Pour leur défense, c'est la SDL qui fait ça, et le projet date de 1998, vous vous doutez bien que le coding style bon...)

Le `for` permet de parcourir chaque élément de la liste. Si ça semble flou pour vous, imaginez un truc similaire en C
```c
struct event **events = ...;

for (int i = 0; events[i] != NULL; i += 1) {
    struct event *event = events[i];
    // Do some stuff
}
```
`event` a un attribut `type` pour savoir de quel évenement il s'agit s'il vaut `pygame.QUIT`, c'est que l'utilisateur veut fermer la fenêtre.

Si vous lancez le programme maintenant, la croix devrait marcher :)

Je vous invite à aller voir [la documentation](https://www.pygame.org/docs/ref/event.html) pour connaître les autres évenements possibles !

## Ouvrez une image

### Les objets `Surface`
> La fonction `set_mode` contenu dans `pygame.display` va instancier une fenêtre et nous retourner une [Surface](https://www.pygame.org/docs/ref/surface.html)

Une `Surface` est un objet dessinable et sur lequel on peut dessiner. Quoi vous vous attendiez à autre chose ? :)

Dans notre cas, `window` est une `Surface` qui est directement lié à la fenêtre. Du coup, si on dessine dessus, on dessine sur la fenêtre ! (C'est stylé non ?)

Pas convaincu ? Faites l'essai !
```py
looping = True
while looping:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            looping = False
    
    window.fill(pygame.Color("blue"))
    pygame.display.flip()
```

Si vous n'avez pas un fond bleu avec ça, le tutoriel s'arrête là et je vais pleurer dans mon coin. (Bien sûr que non haha...)

`pygame.display.flip` doit être appelé afin de rafraîchir la fenêtre, c'est à dire se mettre à jour par rapport à notre Surface `window`.
Si vous le retirez, bizarrement vous aurez à nouveau un écran noir :)

### Avoir une `Surface` à partir d'une image
Entre la création de `window` et le début de la boucle, on va charger une image.

Normalement vous devriez voir un `background.jpg`. Il est de la même taille que la fenêtre (quel hasard) et on s'en servira comme fond au lieu de ce bleu flashy et affreux
```py
screen_size = (640, 480)
window = pygame.display.set_mode(screen_size)

# Création de l'image
background = pygame.image.load("background.jpg").convert()
```
`pygame.image.load` va ouvrir le fichier et nous retourner une image. Dans la foulée on appelle la fonction `convert` contenu dans la surface, afin de retourner une version optimisée pour l'affichage sur notre `window` (décidément il est partout lui !).

Si ça vous perd, remplacez le code par ça:
```py
background = pygame.image.load("background.jpg")
background = background.convert()
```
Ça revient au même :)

Viens le moment de l'afficher
```py
looping = True
while looping:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            looping = False
    
    background_x = 0
    background_y = 0
    window.blit(background, (background_x, background_y))
    pygame.display.flip()
```
On demande de _coller_ `background` à notre background (tel un post-it) à la position donnée. La position correspond au point en haut à gauche de l'image.

Essayez de mettre notre `perso.png` à la position `(200, 300)` maintenant !

> Mec y'a un fond noir ??

C'est normal ! Par défaut, une `Surface` ne connaît pas le transparent, à moins de remplacer `.convert()` par `.convert_alpha()` :)

## Les animations

### La framerate
Là notre `while looping` essaie d'aller aussi vite que ce que votre PC peut permettre. Plus énergivore tu meurs. Du coup on va demander à ce qu'elle soit exécutée un certain nombre de fois par seconde. On appelle ça une `framerate`.

Petit point vocabulaire: Une `frame` est l'exécution de notre boucle principale sur __une__ boucle. Par exemple un framerate de 60fps signifie qu'on a 60 frames par secondes, donc 60 tours de boucle par secondes.

```py
def main():
    pygame.init()

    screen_size = (640, 480)
    window = pygame.display.set_mode(screen_size)
    
    background = pygame.image.load("background.jpg").convert()

    # On crée une Clock qui va servir à gerer la framerate
    clock = pygame.time.Clock()

    looping = True
    while looping:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
               looping = False
    
        background_x = 0
        background_y = 0
        window.blit(background, (background_x, background_y))
        pygame.display.flip()
        
        # A la fin de la boucle on dit qu'à la base on veut du 60fps
        clock.tick(60)
```
> Comment `clock` permet ça ?

Déjà avant toute chose, `frame` et `tick`, c'est la même chose. Ensuite, pour répondre à la question, `clock` va calculer le temps que devrait faire un `tick` (donc une `frame` en somme), et si on est arrivé un peu trop tôt, il attend. Purement et simplement.

## Déplacer notre `perso`

Normalement, vous avez une Surface de notre `perso.png` affiché à l'écran. Pour ce qui va suivre, ça va vous arranger d'avoir un `perso_x` et un `perso_y` comme variable pour placer le petit personnage.
```py
def main():
    pygame.init()

    screen_size = (640, 480)
    window = pygame.display.set_mode(screen_size)
    
    background = pygame.image.load("background.jpg").convert()

    clock = pygame.time.Clock()

    perso_x = 0  # Déclarez-les ici pour éviter les bugs chelou :)
    perso_y = 0

    looping = True
    while looping:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
               looping = False
    
        background_x = 0
        background_y = 0
        window.blit(background, (background_x, background_y))
        pygame.display.flip()
        
        clock.tick(60)
```

On va se servir des évenements clavier pour déplacer Jean-Guy. (Ah je ne vous avais pas encore dit son nom ?)
Je vais vous simplifier la vie et vous épargner la doc.
```py
for event in pygame.event.get():
    if event.type == pygame.QUIT:
        looping = False
    elif event.type == pygame.KEYDOWN:
        if event.key == pygame.K_UP:
            perso_y -= 10
        elif event.key == pygame.K_DOWN:
            perso_y += 10
        elif event.key == pygame.K_LEFT:
            perso_x -= 10
        elif event.key == pygame.K_RIGHT:
            perso_x += 10
```
Ça permettra de déplacer Jean-Guy de 10px avec les flèches !

## Conclusion
Vous avez déjà vu pas mal de choses, et vous avez encore beaucoup à découvrir (afficher du texte, jouer un son, etc.) !

Si le développement de jeux-vidéo vous plaît, je vous encourage à vous intéresser à `pygame` et à Python de manière plus générale.

Bien sûr, pour faire des vrais jeux à la _The last of us_ ou _Call of Duty_ c'est certainement pas en Python que vous les ferez, mais pour les débutants c'est un très bon moyen de développer des connaissance dans le domaine :)
