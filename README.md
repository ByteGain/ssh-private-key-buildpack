# ssh-private-key-buildpack

A Heroku buildpack for setting the ssh private key or multiple deploy keys as part of the application build. It's meant to be used as part of a setup [using multiple buildpacks](https://devcenter.heroku.com/articles/using-multiple-buildpacks-for-an-app), so other buildpacks can authenticate with hosts using ssh keys, for instance to install dependencies from private git repositories.

# Example usage

## Configure Multiple Buildpacks
Add the buildpack to your Heroku app either using the CLI or the Heroku dashboard. The `ssh-private-key-buildpack` needs to run before any buildpack trying to get ssh access. In the following example, it runs before the `heroku/go` buildpack.

    $ heroku buildpacks:add --index 1 https://github.com/debitoor/ssh-private-key-buildpack.git
    $ heroku buildpacks:add heroku/go

## Configure SSH Key

Set the private key environment variable `SSH_KEY` of your Heroku app (note that the key needs to be base64 encoded).

    $ heroku config:set SSH_KEY="$(cat path/to/your/keys/id_rsa | base64)"

By default the buildback adds Github to `known_hosts`. However you can configure your app to allow custom hosts, too. All that's needed is the set `SSH_HOSTS` for you app to a comma-separated list of hosts, e.g. `git@github.com,example.com`

    $ heroku config:set SSH_HOSTS="git@github.com,example.com"

## Configure Deploy Keys

Set the private key environment variable `DEPLOY_KEYS` of your Heroku app (note that the key needs to be base64 encoded).

    $ heroku config:set DEPLOY_KEYS="$(cat path/to/deploy/key1 | base64 -w 0),$(cat path/to/deploy/key2 | base64 -w 0)"

Each deploy key is added as a global identity to a generated SSH config.
