+++
date = "2017-05-10"
title = "CircleCI integration for Elixir/Phoenix project"
slug = "circle-ci-integration-for-elixir-phoenix-project"
tags = []
categories = []
+++

At JobTeaser, we are strongly attached to use continuous integration on all our projects with all its facets; testing, linting and deploying.

Up to now, we use CirleCI 1.0 with Ruby/Ruby on Rails, Python and JavaScript. In this context, we tried to setup continuous integration for Elixir but it does not natively support Elixir and Erlang. Looking through the documentation, we discovered CircleCI 2.0 beta with native Docker support.


## Stack
The project is an umbrella application under Elixir 1.4, Erlang/OTP 19 and Phoenix 1.3-rc as our web layer. We use PostgreSQL 9.6 as our database.

## Setup
We’ve started by subscribing to the beta. Our CircleCI 1.0 builds were not impacted.

We were instantly able to create a project on CircleCI 2.0. We start by creating the new configuration file .circleci/config.yml for our project.

The first step is to choose Docker images to use. According to the documentation, you can use public (e.g. Docker Hub) or private registries.

```yaml
docker:
  — image: elixir:1.4.1
    environment:
      — MIX_ENV: test
  — image: postgres:9.6
```

> Note: as you can see, it is possible to add environment variables into containers.

We then declare build’s steps as follow:

```yaml
steps:
  — checkout
  — run:
    name: Install dependencies
    command: |
      mix local.rebar —-force
      mix local.hex --force
      mix deps.get
  — run:
    name: Run compile
    command: mix compile
  — run:
    name: Run Test
    command: mix test
  — run:
    name: Run Linter
    command: mix credo --strict
```

Comparing to CircleCI 1.0, the new config starts from a blank script where you precise each step of your build. In our case, we defined 4 steps of one of the following type:

  - checkout is a specific task which fetch code
  - run declaration is used to invoke a command


## Install dependencies
In this step, we want to fetch all our dependencies. We use `local.rebar --force` and `local.hex --force` to ensure that rebar and hex are installed.

> Note: --force is used to install without prompt.

As in local development, we call `deps.get` to fetch dependencies.

## Run compile
We compile before testing and linting to run these steps on the same binaries (and avoid useless recompilation).

## Run Test and Run Linter
We choose to run tests and linting for this build but you can imagine run any commands to your build.

We are eager to ear your feedback and questions. Moreover, you can find more info about CircleCI 2.0 in its documentation.
