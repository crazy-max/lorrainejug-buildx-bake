---
marp: true
title: buildx bake adoption --push
description: Première utilisation de `buildx bake` dans votre workflow
theme: default
class:
  - lead
  - invert
paginate: true
_paginate: false
footer: 'github.com/crazy-max'
---

<style scoped>
footer a { color: #404040; }
</style>
![bg 40% opacity blur](assets/docker.png)

# `buildx bake adoption --push`

### Première utilisation de `buildx bake` dans votre workflow.

---

<style scoped>
footer a { color: #404040; }
</style>
![bg 40% opacity blur](assets/docker.png)

## Contexte

Il existe actuellement quatre façons différentes de construire une image avec Docker
  * Le builder "legacy" dans Docker Engine: `docker build .`
  * [BuildKit](https://github.com/moby/buildkit) dans le Docker Engine: `DOCKER_BUILDKIT=1 docker build .`
  * [Docker driver](https://github.com/docker/buildx/blob/master/docs/reference/buildx_create.md#docker-driver) avec buildx CLI plugin: `docker buildx build .`
  * [Container driver](https://github.com/docker/buildx/blob/master/docs/reference/buildx_create.md#docker-container-driver) avec buildx CLI plugin:
    ```shell
    $ docker buildx create --use # créera le conteneur BuildKit
    $ docker buildx build .
    ```

<!-- La première que vous utilisez peut-être encore probablement aujourd'hui est le backend "legacy". Je mets legacy entre guillemets car nous considérons aujourd'hui cette façon de build obsolète. -->
<!-- Celle-ci est le comportement par défaut si vous utilisez Docker Desktop sur votre PC. -->
<!-- Ensuite nous arrivons à buildx avec le driver par défaut (docker) qui a le même comportement que BuildKit dans Docker Engine avec Docker CLI. -->
<!-- Et enfin le mode le plus avancé et optimisé, nous avons buildx avec le driver de type conteneur que je vais vous présenter aujourd'hui. -->

<!-- Comme vous le voyez l'état actuel n'est pas seulement source de confusion
pour les utilisateurs qui ne comprennent pas les différences entre nos
expériences de construction, il est également en contradiction avec ce vers
quoi check Docker nous investissons nos efforts d'ingénierie de construction.
A savoir dans Buildx et BuildKit. -->

---

<style scoped>
footer a { color: #404040; }
</style>
![bg 40% opacity blur](assets/docker.png)

## Docker buildx

* Nouveau builder en tant que plugin CLI disponible depuis Docker 19.03
* `docker buildx build ` <> `docker build` 
* Mais avec de nombreuses nouvelles fonctionnalités
  * Instances de builder dédiées
  * Construction simultanée sur plusieurs nœuds
  * Source de cache externe pour votre build
  * Construire des images multi-plateforme
  * :rocket: Commande de haut niveau avec **`bake`**

<!-- Buildx est un plugin de la CLI de Docker disponible depuis Docker 19.03 et
qui, combiné au composant BuildKit côté serveur, fournit un système de
construction de conteneurs de nouvelle génération pour Docker. Buildx est conçu
de manière à pouvoir fonctionner avec différents environnements d'exécution de
conteneurs par le biais de drivers. Certains de ces drivers (`container` et `kubernetes`)
utilisent une version conteneurisée de BuildKit, ce qui signifie que la chaîne
d'outils de construction peut être mise à jour indépendamment des autres outils
de conteneurs. -->

<!-- Chez Docker nous misons beaucoup sur la rétro compatibilité et c'est
pourquoi nous voulons offrir la même expérience utilisateur que `docker build` -->

<!-- Mais avec de nombreuses nouvelles fonctionnalités telles que -->

<!-- - la création d'instances de builder scopées -->
<!-- - et la construction sur plusieurs nœuds simultanément. -->
<!-- - source de cache externe comme le registry utilisant un manifeste de cache,
ou file system local, et également assez récent via des blobs storage comme
Azure avec GitHub Actions ... -->
<!-- - images multi-plateformes comme `linux/amd64`, `linux/arm64` avec une seule
commande. Fini les opérations avec docker manifest. -->

---

<style scoped>
footer a { color: #404040; }
section { font-size: 120%; }
</style>
![bg 40% opacity blur](assets/docker.png)

## Commande buildx

```shell
$ docker buildx --help
Usage:  docker buildx [OPTIONS] COMMAND

Extended build capabilities with BuildKit

Options:
      --builder string   Override the configured builder instance

Management Commands:
  imagetools  Commands to work on images in registry

Commands:
  bake        Build from a file
  build       Start a build
  create      Create a new builder instance
  du          Disk usage
  inspect     Inspect current builder instance
  ls          List builder instances
  prune       Remove build cache
  rm          Remove a builder instance
  stop        Stop builder instance
  use         Set the current builder instance
  version     Show buildx version information

Run 'docker buildx COMMAND --help' for more information on a command.
```

<!-- Utilisons maintenant la nouvelle commande buildx qui va nous permettre
de construire une image multi-platforme avec toutes les fonctionnalités de
BuildKit en utilisant le driver de type conteneur -->

---

<style scoped>
footer a { color: #404040; }
li { font-size: 90%; }
</style>
![bg 40% opacity blur](assets/docker.png)

## `hello world`

* :gun: Démo!

<!-- Passons à une démo pour montrer comme interagir simplement avec buildx
dans votre workflow avec un hello world typique que vous feriez avec un
classique docker build. -->

---

<style scoped>
footer a { color: #404040; }
li { font-size: 90%; }
</style>
![bg 40% opacity blur](assets/docker.png)

## Image multi-platforme

Docker buildx fournit trois options pour construire des images multi-platforme:
* QEMU mode (facile mais lent)
* Cross-compilation mode (peut s'avérer difficile mais rapide)
* Remote mode (facile et rapide mais requiert une autre machine dédiée)

<!-- L'option la plus simple consiste à utiliser le mode QEMU (option 1), mais
elle entraîne une baisse significative des performances. Il est donc recommandé
d'utiliser la cross-compilation (option 2) si cela ne vous dérange pas d'adapter
vos Dockerfiles avec la toolchain de cross-compilation. Si vous disposez déjà
d'une machine ARM par exemple, la troisième option est probablement le
meilleur choix pour vous. -->

---

<style scoped>
footer a { color: #404040; }
li { font-size: 90%; }
</style>
![bg 40% opacity blur](assets/docker.png)

## QEMU mode

* Ce mode utilise le [QEMU User Mode Emulation](https://www.qemu.org/docs/master/user/index.html) pour exécuter la toolchain cible dans un environnement Linux x86_64.
* :gun: Démo!

<!-- L'intégration QEMU est déjà activée et disponible par défaut dans Dcoker
Dekstop pour Mac et Windows. -->

<!-- Passons à une démo pour construire notre précédent hello world mais
en ciblant plusieurs platformes -->

---

<style scoped>
footer a { color: #404040; }
</style>
![bg 40% opacity blur](assets/docker.png)

## Récap multi-platforme

* Nous avons vu comment construire une image multi-platforme
* Optimiser notre Dockerfile grâce au multi-stage
* Pousser notre image sur Docker Hub

<!-- Avec QEMU pour le moment -->

<!-- À la fin de la construction, toutes ces images sont fusionnées en une
seule image multiplateforme. -->

---

<style scoped>
footer a { color: #404040; }
li { font-size: 90%; }
ul { list-style-type: none; }
</style>
![bg 40% opacity blur](assets/docker.png)

## Commande buildx avancée

* `docker buildx build \`
  * `--push \`
  * `--cache-from "type=registry,ref=foo/myapp" \`
  * `--cache-to "type=inline" \`
  * `--platform "linux/amd64,linux/arm/v6,linux/arm/v7,linux/arm64" \`
  * `--label "org.opencontainers.image.title=myapp" \`
  * `--label "org.opencontainers.image.source=https://github.com/foo/myapp" \`
  * `--label "org.opencontainers.image.version=1.0.0" \`
  * `--label "org.opencontainers.image.licenses=Apache-2.0" \`
  * `--tag "foo/myapp:v1.0.0" \`
  * `--tag "foo/myapp:latest" \`
  * `--file "./main.Dockerfile" \`
  * `.`

<!-- Voyons une command buildx avancée ou comment vous faire détester la
ligne de commande -->

<!-- On va vouloir la pousser bien sûr -->

<!-- Partons avec docker buildx build qui est la commande que vous utiliserez
le plus dans votre workflow -->

<!-- Mais attends nous pourrions utiliser un cache externe via Docker Hub! -->

<!-- Bien sûr je veux que mon image soit multi-platforme -->

<!-- Ok ajoutons quelques labels pour être en conformité avec l'OCI Image
Format Specification -->

<!-- Taggons notre image -->

<!-- Choisissons un Dockerfile -->

<!-- Et notre contexte -->

---

<style scoped>
footer a { color: #404040; }
li { font-size: 90%; }
ul { list-style-type: none; }
</style>
![bg 40% opacity blur](assets/docker.png)

## Avec `bake`

* `docker buildx bake \`
  * `--push`
  * ~~`--cache-from "type=registry,ref=foo/myapp" \`~~
  * ~~`--cache-to "type=inline" \`~~
  * ~~`--platform "linux/amd64,linux/arm/v6,linux/arm/v7,linux/arm64" \`~~
  * ~~`--label "org.opencontainers.image.title=myapp" \`~~
  * ~~`--label "org.opencontainers.image.source=https://github.com/foo/myapp" \`~~
  * ~~`--label "org.opencontainers.image.version=1.0.0" \`~~
  * ~~`--label "org.opencontainers.image.licenses=Apache-2.0" \`~~
  * ~~`--tag "foo/myapp:v1.0.0" \`~~
  * ~~`--tag "foo/myapp:latest" \`~~
  * ~~`--file "./main.Dockerfile" \`~~
  * ~~`.`~~

<!-- Buildx vise également à fournir un support pour les concepts de
construction de haut niveau qui vont au-delà de l'appel d'une seule commande
de construction. Nous voulons prendre en charge la construction de toutes les
images de votre application et permettre aux utilisateurs de définir des
flux de construction réutilisables spécifiques au projet qui peuvent ensuite
être facilement invoqués par n'importe qui. -->

<!-- Comme vous pouvez le constater, bake va supprimer toutes les contraintes
liées aux flags dont nous avons besoin pour notre construction. -->

<!-- Vous n'avez pas non plus besoin de --push mais nous le recommandons car
ça reste un moyen simple de switch avec la ligne de commande dans votre CI par
exemple (PR vs branche) -->

<!-- Certains vont me dire, oui mais tu pourrais utiliser make? Oui et non. Ici
l'exécution invoquée par bake gère efficacement plusieurs demandes de
construction simultanées et dédouble le travail pour une exécution asynchrone.
Les commandes comme make invoquent généralement les constructions en séquence
et ne peuvent donc pas exploiter tout le potentiel de la parallélisation de
BuildKit, ni combiner les résultats de BuildKit pour l'utilisateur. Et c'est
pourquoi nous avons créé bake. -->

---

<style scoped>
footer a { color: #404040; }
</style>
![bg 40% opacity blur](assets/docker.png)

## Commande `buildx bake`

```shell
$ docker buildx bake --help
Usage:  docker buildx bake [OPTIONS] [TARGET...]

Build from a file

Aliases:
  bake, f

Options:
      --builder string           Override the configured builder instance
  -f, --file stringArray         Build definition file
      --load                     Shorthand for --set=*.output=type=docker
      --metadata-file string     Write build result metadata to the file
      --no-cache                 Do not use cache when building the image
      --print                    Print the options without building
      --progress                 Set type of progress output (auto, plain, tty). Use plain to show container output (default "auto")
      --pull                     Always attempt to pull a newer version of the image
      --push                     Shorthand for --set=*.output=type=registry
      --set stringArray          Override target value (e.g., targetpattern.key=value)
```

<!-- Voici à quoi ressemble la commande buildx bake -->

---

<style scoped>
footer a { color: #404040; }
</style>
![bg 40% opacity blur](assets/docker.png)

## bake definition

* Peut être au format Docker Compose, JSON ou **HCL** :heart:
* Plusieurs fichiers peuvent être spécifiés avec le flag `--file`
  * Leur configuration sera mergée
* Par défaut:
  * Compose: `docker-compose.yml`, `docker-compose.yaml`
  * JSON: `docker-bake.json`, `docker-bake.override.json`
  * HCL: `docker-bake.hcl`, `docker-bake.override.hcl`

<!-- Avec bake, nous voulons permettre aux utilisateurs de définir des flux de
construction réutilisables spécifiques à un projet, qui peuvent ensuite être
facilement invoqués par quiconque utilise un fichier de définition. -->

<!-- Oui HCL est chaudement recommandé et fortement extensible. Nous pensons
également à introduire une définition au format CUE -->

<!-- Mergée dans l'ordre spécifié -->
<!-- Par défaut les fichiers suivant seront détectés si aucun n'est spécifié -->

---

<style scoped>
footer a { color: #404040; }
code { font-size: 65%; }
section { font-size: 170%; }
</style>
![bg 40% opacity blur](assets/docker.png)

## HCL format

```hcl
variable "TAG" {
  default = "v0.0.0"
}

group "default" {
  targets = ["image"]
}

target "tag" {
  tags = ["foo/myapp:${TAG}"]
}

target "image" {
  inherits = ["tag"]
  context = "."
  dockerfile = "./main.Dockerfile"
  cache-from = "type=registry,ref=foo/myapp"
  cache-to = "type=inline"
  labels = [
    "org.opencontainers.image.title=myapp",
    "org.opencontainers.image.source=https://github.com/foo/myapp",
    "org.opencontainers.image.version=1.0.0",
    "org.opencontainers.image.licenses=Apache-2.0"
  ]
  tags = ["foo/myapp:latest"]
}

target "image-all" {
  inherits = ["image"]
  platforms = [ "linux/amd64", "linux/arm/v6", "linux/arm/v7", "linux/arm64" ]
}
```

<!-- HCL ajoute la prise en charge de règles de construction personnalisées
permettant une meilleure réutilisation du code et différents groupes cibles. -->

<!-- Une cible (target) reflète une invocation unique de "docker build" avec
les mêmes options que vous spécifieriez pour docker build. Un groupe est un
regroupement de cibles. -->

<!-- Un groupe peut spécifier sa liste de cibles avec l'option targets. Une
cible peut hériter des options de construction en définissant l'option inherits
sur la liste des cibles ou des groupes dont elle doit hériter. -->

<!-- Plusieurs fichiers peuvent inclure la même cible et les options de
construction finales seront déterminées en les fusionnant. -->

<!-- De la même manière que Terraform permet de définir des variables, le
format de fichier HCL prend également en charge les définitions de blocs de
variables. Celles-ci peuvent être utilisées pour définir des variables avec
des valeurs fournies par l'environnement actuel ou une valeur par défaut
lorsqu'elles ne sont pas définies. Le typage et vérification pour ces variables
n'est pas encore en place mais nous travaillons dessus pour les intégrer
prochainement. -->

<!-- Un ensemble de fonctions généralement utiles fournies par go-cty sont
disponibles pour une utilisation dans les fichiers HCL. En outre, les fonctions
définies par l'utilisateur sont également prises en charge. -->

---

<style scoped>
footer a { color: #404040; }
</style>
![bg 40%](assets/demo-time.jpg)

<!-- Passons à une démo pour montrer le fonctionnement de bake! -->

<!-- Mais avant cela j'ai une surprise à vous montrer par rapport à la création
de cette présentation. Oui ces slides sont dockerisés et construits avec bake -->

<!-- Créons une vraie app exposant un simple web service que nous construirons
après avec bake -->

  <!-- Ici je vais utiliser un frontend spécifique car je veux utiliser de
nouvelles fonctionnalités présentes dans BuildKit qui ne sont pas encore
disponibles dans le frontend actuellement livré dans Docker engine
Voir https://github.com/moby/buildkit/blob/master/frontend/dockerfile/docs/syntax.md -->

  <!-- Dans notre Dockerfile nous n'allons par utiliser QEMU mais la 
cross-compilation native qui est prise en charge nativement par Go pour 
construire une image multi-platforme -->

  <!-- BUILDPLATFORM fait partie d'un ensemble d'arguments de construction
définis automatiquement (portée globale) que vous pouvez utiliser. Il
correspondra toujours à la plateforme ou à votre système actuel et le builder
remplira la valeur correcte pour nous.
Voir https://docs.docker.com/engine/reference/builder/#automatic-platform-args-in-the-global-scope -->

  <!-- Ici j'utilise une nouvelle syntaxe --mount proposée par le frontend et
directement intégrée à l'instruction RUN pour monter mon contexte plutôt que
d'utiliser l'instruction COPY. Mount permet également de monter des secrets,
un agent SSH ou un tmpfs, du cache. -->

  <!-- Tiens d'ailleurs je vais créer un mount pour monter mon cache de deps et
de build. Comme ça si mon code source change et que je reconstruits mon image je
pourrias réutilsier les metadata de build précédentes. -->

  <!-- La seule contrainte de BUILDPLATFORM est que la syntaxe les architectures
dans ces variables sont spécifiques à Docker. Il faut donc traduire ces variables
par rapport au langage cible pour que notre cross-compilation réussisse. -->

  <!-- Pour Go, j'ai créé un projet nommé goreleaser-xx qui est un petit wrapper
autour du fantastique outil de construction GoReleaser pour être capable de
gérer une image Docker multi-plateforme fonctionnelle afin de faciliter
l'intégration et la cross-compilation dans un Dockerfile pour les projets Go.
Voir https://github.com/crazy-max/goreleaser-xx -->

<!-- Imprime les options résultantes des cibles que l'on souhaite construire -->

<!-- Remplacer les configurations de la cible à partir de la ligne de commande. -->

---

<style scoped>
footer a { color: #404040; }
</style>
![bg 20% opacity blur](https://avatars1.githubusercontent.com/u/1951866?v=4)

# Thanks for your time!

* :newspaper: Slides et démo dispo [sur GitHub](https://github.com/crazy-max/lorrainejug-buildx-bake)
* :test_tube: Bake est encore expérimental donc n'hésitez pas à donner [votre feedback](https://github.com/docker/buildx/issues)!
* :rocket: Une GitHub Action est déjà disponible: https://github.com/docker/bake-action
* :whale: Jetez à oeil au [projet Diun](https://github.com/crazy-max/diun) qui utilise bake
