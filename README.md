# create-web-wordpress

A bootstrap script that scaffolds the `restless/web-wordpress` Composer project inside the current directory using Docker — no local PHP or Composer required.

## Prerequisites

- **Docker** — running before you execute the script
- **`~/.composer/auth.json`** — Composer credentials file (see below)

## Composer credentials

Create `~/.composer/auth.json` with credentials for every private host the project uses. All entries can coexist in the same file.

### GitHub OAuth Token

Private `restless` packages are hosted on GitHub. Composer uses a GitHub OAuth token to authenticate and download them as zip archives via the GitHub API.

1. Go to **GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)**
2. Generate a new token with the **`repo`** scope only
3. Add it to `~/.composer/auth.json`:

```json
{
  "github-oauth": {
    "github.com": "YOUR_GITHUB_TOKEN"
  }
}
```

### Advanced Custom Fields PRO

ACF PRO requires http-basic credentials in `~/.composer/auth.json`. The password can be any site URL active on the license. Use `http://localhost` for development.

- [Installing ACF PRO with Composer](https://www.advancedcustomfields.com/resources/installing-acf-pro-with-composer/)

```json
{
  "http-basic": {
    "connect.advancedcustomfields.com": {
      "username": "${ACF_PRO_KEY}",
      "password": "http://localhost"
    }
  }
}
```

## Usage

Run from the empty directory that will become the project root:

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/restless-org/create-web-wordpress/main/install)
```

### Optional: specify a PHP version

Pass the version as the first argument (default is `84`):

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/restless-org/create-web-wordpress/main/install) 82
```

Supported versions: `80`, `81`, `82`, `83`, `84`

## What the script does

1. Checks that Docker is running
2. Loads `~/.composer/auth.json` into the container environment
3. Runs `composer create-project restless/web-wordpress` inside a `laravelsail/phpXX-composer` container
4. Runs `wp unicorn install` via a `wordpress:cli` container — generates a `.devcontainer` folder for VS Code Dev Containers
5. Runs `./vendor/bin/unicorn pull` and `./vendor/bin/unicorn build`
6. Fixes file ownership back to your local user

## Starting the project

Once the script completes:

```bash
./vendor/bin/unicorn up
```

### Dev Containers

The script generates a `.devcontainer` folder. Open the project in VS Code and choose **Reopen in Container** to develop inside a pre-configured container environment.
