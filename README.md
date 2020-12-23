# Heroku Buildpack: OCaml

A buildpack for Heroku and compatible systems, for OCaml applications built by dune with dependencies managed by opam.

## How to use
- Create a Heroku account (or set up a compatible system like [dokku](https://github.com/dokku/dokku))
- Get a git repository with an OCaml project that can be built by the buildpack (you can use https://github.com/roddyyaga/opium-heroku-example)
- Clone the repo and run `heroku create some-globally-unique-name --buildpack https://github.com/roddyyaga/heroku-buildpack-ocaml`
- Run `git push heroku master`
- Go to the Heroku URL and check if the app is running

## Build process
This buildpack is for OCaml projects with:
- Dependencies specified by an opam lockfile in the root directory (you can make one with `opam lock`)
- Binaries that will be built/installed by `dune build @install && dune install` in the root directory

The build process will install dependencies, build the project, and then install the project under the directory
where Heroku runs the app. For instance, if your app's entrypoint has a dune `public_name` of `server` then you
should have a `Procfile` with contents `web: bin/server`. If no Procfile is provided, the buildpack will assume
it is `web: bin/main`.

As with all Heroku projects, your web server should use the port in the environment variable `PORT`.

## Caching
The buildpack will create an opam switch and cache dependencies after each run. The behaviour should be the same
as developing locally with a switch, where adding one dependency means only that dependency has to be installed
on the next build (rather than any change to the lockfile forcing a complete reinstall).

## Caveats
- opam sandboxing is disabled due to constraints of the Heroku build environment
- Installing external dependencies is currently not supported and might not be possible (there is no package manager
  available in the build environment)
