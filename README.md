# `buildx bake adoption --push`

Presentation of `buildx bake` @ [Lorraine JUG](https://lorrainejug.github.io/annonce/2021/11/18/docker-buildx.html)

___

* [Slides](#slides)
  * [Render](#render)
  * [Run and auto-reload](#run-and-auto-reload)
  * [GitHub Actions with `bake` and GitHub Pages](#github-actions-with-bake-and-github-pages)
* [Demo app](#demo-app)
  * [Sources](#sources)
  * [Build](#build)
  * [GitHub Actions](#github-actions)
* [License](#license)

## Slides

Slides available on GitHub Pages: https://crazy-max.github.io/lorrainejug-buildx-bake/

### Render

```shell
git clone https://github.com/crazy-max/lorrainejug-buildx-bake.git
cd lorrainejug-buildx-bake

# generate slides to ./www folder
docker buildx bake slides
```

### Run and auto-reload

```shell
docker run --rm --init -v $(pwd)/slides:/home/marp/app -p 8080:8080 -p 37717:37717 marpteam/marp-cli:v1.4.2 -w -s -I .
# open your browser at http://localhost:8080
```

### GitHub Actions with `bake` and GitHub Pages

GitHub Action workflow available in [.github/workflows/slides.yml](.github/workflows/slides.yml)

## Demo app

### Sources

Sources available in [./demo](demo) folder.

### Build

```shell
git clone https://github.com/crazy-max/lorrainejug-buildx-bake.git
cd lorrainejug-buildx-bake

# build docker image and output to docker with lorrainejug-bake:local tag (default)
docker buildx bake image-local

# build multi-platform image
docker buildx bake image-all

# create the artifact matching your current platform in ./dist
docker buildx bake artifact

# create artifacts for many platforms in ./dist
docker buildx bake artifact-all
```

### GitHub Actions

GitHub Action workflow available in [.github/workflows/build.yml](.github/workflows/build.yml)

* Using [docker/bake-action](https://github.com/docker/bake-action) to:
  * Create artifacts for [GitHub Releases](https://github.com/crazy-max/lorrainejug-buildx-bake/releases)
  * Publish multi-platform Docker image to [Docker Hub](https://hub.docker.com/r/crazymax/lorrainejug-buildx-bake/tags?page=1&ordering=last_updated) and [GHCR](https://github.com/users/crazy-max/packages/container/package/lorrainejug-buildx-bake)

## License

MIT. See `LICENSE` for more details.
