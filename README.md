# Branch Protection Enforcer App

## Challenge

> "I need my people capable of creating GitHub repositories with minimal controls in place, but I can't give them `admin` permission."

For any company with regulatory controls around how they build and deliver products, there is a real challenge of how to make it easy for their developers to be productive with the minimal necessary controls in place.

* [IT General Control](https://en.wikipedia.org/wiki/ITGC)
* [Sarbanes Oxley](https://en.wikipedia.org/wiki/Sarbanes%E2%80%93Oxley_Act#Sarbanes%E2%80%93Oxley_Section_404:_Assessment_of_internal_control)
* [Secure Software Development Life Cycle](https://en.wikipedia.org/wiki/Software_development_security), etc.

The Branch Protection Enforcer App project is a GitHub App you can setup and install within your GitHub organization with a simple purpose:

1. Watch for newly created repositories
1. Create branch protection rule for the default branch
1. Create issue notifying the repository creator of branch protection rule created

[Back to the top](#branch-protection-enforcer-app)

## Demo

Example of GitHub branch protection rule created immediately after repository creation
![Branch protection rule created](docs/demo_new_repo_branch_protection.png)

Example of GitHub issue documenting branch protection rule created
![Issue created letting the repository creator know branch protection rule created](docs/demo_new_repo_issue.png)

[Back to the top](#branch-protection-enforcer-app)

## Quickstart

1. **Setup Smee client for proxying GitHub App webhook events**

   ```shell
   $ npm install --global smee-client
   $ smee --path /event_handler --port 3000
   ```

   noting the Smee channel url generated:

   ```
   Forwarding https://smee.io/XXXXXX#XXXX#XXXX to http://127.0.0.1:3000/event_handler
   Connected https://smee.io/XXXXXX#XXXX#XXXX
   ```

1. **Register a new GitHub App**

   Navigate to the [Register new GitHub App](https://github.com/settings/apps/new)  _(from **New GitHub App** button on [GitHub > Developer Settings > GitHub Apps](https://github.com/settings/apps) screen)_ and provide the following information:

   * **GitHub App name**<br />
     _Set this to something like "branch-protection-enforcer-dev-USERNAME"; allow you and others in your organization to develop simultaneously_

   * **Homepage URL**<br />
     _Set this to the Smee channel URL; https://smee.io/XXXXXX#XXXX#XXXX_

   * **Webhook URL**<br />
     _Set this to the Smee channel URL; https://smee.io/XXXXXX#XXXX#XXXX_

   * **Webhook secret**<br />
     _Set this to something random to ensure webhook events are likely coming from GitHub_

   * **Repository permissions**<br />
     _Select the following permissions for the GitHub App to request when installed_

     * **Administration**: Read & write
     * **Issues**: Read & write

   * **Subscribe to events**<br />
     _Select the following events for the GitHub App to receive when installed_

     * **Repositories**

   * **Where can this GitHub App be installed?**<br />
     _Set this to "Only on this account"_

   What this form would look like with some of the extraneous permissions removed:<br />
   ![Trimmed view of new GitHub App form with relevant fields](docs/contributing/developer_github_new_app_settings.png)

1. **Use GitHub App private key and App ID to generate `.env` configuration**

   With the newly created developer GitHub App, we need to generate the necessary private key along with the app ID for our developer environment to authenticate.<br />
   ![Recently created developer GitHub App](docs/contributing/developer_github_new_app_ready.png)

   Clicking link in header jumps down to the Private keys section<br />
   ![Generating private key triggers a file download containing private key](docs/contributing/developer_github_new_app_key.png)

   1. Create `.env` file in root of repository based on `.env-example`

      ```shell
      $ cp .env-example .env
      ```

   1. Customize `.env` file based upon information from developer GitHub App information

      ```
      GITHUB_APP_IDENTIFIER=#####

      GITHUB_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----
      ... file contents from generating private key ...
      -----END RSA PRIVATE KEY-----"

      GITHUB_WEBHOOK_SECRET=... random value entered when creating developer GitHub App ...
      ```

1. **Start the server**

   ```shell
   $ docker run -d -it -v "$PWD"/.env:/app/.env -p 3000:3000 ghcr.io/andyfeller/branch-protection-enforcer-app:latest
   ```

1. **Install the app on your organization**

   For development purposes, you should install the developer GitHub App on your personal GitHub organization
   ![Installing developer GitHub App on personal organization](docs/contributing/developer_github_app_install.png)

   Confirmation of the permissions we selected when we initially registered the developer GitHub App
   ![Confirmation of permissions being granted to installation](docs/contributing/developer_github_app_install_confirm.png)

1. **Create new repository and verify it works**

   ![Demo creating new repository](docs/demo_new_repo.png)<br />
   ![Demo new repository issues](docs/demo_new_repo_issues.png)<br />
   ![Demo new repository issue describing branch protection rule created](docs/demo_new_repo_issue.png)<br />
   ![Demo new repository branch protection rule](docs/demo_new_repo_branch_protection.png)

[Back to the top](#branch-protection-enforcer-app)

## Roadmap

Base GitHub App
- [x] Supports being installed for an organization
- [x] Supports creating basic branch protection rule with sane defaults
- [x] Supports documenting work as issue

Enhancements
- [x] GitHub Action workflow(s) for testing, packaging, and publishing container image and Ruby gem
- [ ] Documentation around installing releases
- [ ] Supports arbitrary default branch names  _(bug in repository create payload on default branch name)_
- [ ] Supports use cases repository does not support branch protection rules  _(GitHub Free plan only supports on public repositories)_
- [ ] Supports fine grained customizations of repositories based on arbitrary criteria  _(artisianal sausage making)_
- [ ] Supports installing [GitHub App from manifest](https://docs.github.com/en/developers/apps/building-github-apps/creating-a-github-app-from-a-manifest)  _(why not?)
- [ ] Supports rich webhook delivery responses  _(make it easier to troubleshoot on both ends)_

[Back to the top](#branch-protection-enforcer-app)

## Contributing

Are you some who gets this isn't a sexy challenge but means all the world to product developers that need to go fast?

Please help: consider [contributing to this project](CONTRIBUTING.md) so others can have the best GitHub experience!

[Back to the top](#branch-protection-enforcer-app)

## Known Limitations

* This is project is in its infancy and lacks some boilerplate efforts around building, packaging, testing, and promotion.
* Error handling and logging are not the most robust currently

[Back to the top](#branch-protection-enforcer-app)

## Troubleshooting

### Expiration time' claim ('exp') is too far in the future

When I first saw this issue, it was due to clock skew between the machine the app was running on versus GitHub.

You can compare your date/time against GitHub and even sync your clock with appropriate atomic clock:

```shell
$ date && curl -I https://api.github.com | grep -Fi "date"
$ sudo apt-get install ntpdate
$ sudo ntpdate pool.ntp.org
$ date && curl -I https://api.github.com | grep -Fi "date"
```

[Back to the top](#branch-protection-enforcer-app)
