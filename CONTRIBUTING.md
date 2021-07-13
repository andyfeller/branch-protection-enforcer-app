# Contribute to the Branch Protection Enforcer App

## Prerequisites

* [ruby](https://www.ruby-lang.org/en/)  _(2.7.3 or newer)_
* [node.js](https://nodejs.org/en/)  _(v16.4.2 or newer)_
* [rbenv](https://github.com/rbenv/rbenv)  _(optional)_
* [nvm](https://github.com/nvm-sh/nvm)  _(optional)_
* [Docker Desktop](https://www.docker.com/products/docker-desktop)  _(optional)_

[Back to the top](#contribute-to-the-branch-protection-enforcer-app)

## Local Development Workflow

Local development for the Branch Protection Enforcer App is nearly the same as [our quickstart documentation](README.md#quickstart),
which was based on the ["Setting up your development environment to create a GitHub App" guide](https://docs.github.com/en/developers/apps/getting-started-with-apps/setting-up-your-development-environment-to-create-a-github-app), with the only difference being the **Start the server** step:

1. **Install dependencies**

   ```shell
   $ gem install bundler
   $ bundle update --bundler
   $ bundle install
   ```

1. **Build and run service**

   Via container image:

   ```shell
   $ rake build:container run:container
   ```

   Via Ruby:

   ```shell
   $ bundle exec ruby server.rb
   ```

[Back to the top](#contribute-to-the-branch-protection-enforcer-app)

## Resources

* [GitHub Actions Documentation](https://docs.github.com/en/actions)
  * [Building and testing Ruby](https://docs.github.com/en/actions/guides/building-and-testing-ruby)