# Bestow Quote API Documentation Site

Runs as a forked repo of Slate: https://github.com/lord/slate

Live URL: https://bestowinc.github.io/quote-api-docs/#introduction

### Prerequisites

You're going to need:

- **Linux or macOS** — Windows may work, but is unsupported.
- **Ruby, version 2.3.1 or newer** - `brew install ruby`
- **Bundler** — If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.
  - If you're having permission issues installing bundler, follow the steps to use rbenv [here](https://github.com/rbenv/rbenv/issues/938) 

### Getting Set Up

1. Clone to your hard drive with `git clone git@github.com:Bestowinc/quote-api-docs.git`
2. `cd quote-api-docs`
3. Initialize and start. You can either do this locally, or with Vagrant:

```shell
# either run this to run locally
bundle install
bundle exec middleman server

# OR run this to run with vagrant
vagrant up
```

You can now see the docs at http://localhost:4567. Whoa! That was fast!

Now that Slate is all set up on your machine, you'll probably want to learn more about [editing Slate markdown](https://github.com/lord/slate/wiki/Markdown-Syntax), or [how to publish your docs](https://github.com/lord/slate/wiki/Deploying-Slate).

If you'd prefer to use Docker, instructions are available [in the wiki](https://github.com/lord/slate/wiki/Docker).

### Deployments

To deploy to the gh-pages branch, run the following:
```shell
cd quote-api-docs
./deploy.sh
```
