# hello-user
A Demo repo for the Git Workshop at Codeable

## Exercise 6
Our Git journey for today will end with this last exercise: release hello user with a release GitHub workflow.

### Step 1
Add a new workflow config file in the `.github/workflows` folder. Name it `release.yml`.
```yaml
# This snippet is adapted from graphql-api-for-wp.
#
# GitHub Action: whenever creating a new release of the source code,
# also create a release of the installable plugin.
# Steps to execute:
# - Checkout the source code
# - Create a .zip file, excluding:
#   - All hidden files (.git, .gitignore, etc)
#   - All development files, ending in .dist
#   - All composer files <= after installing dependencies, no need for them anymore
#   - Markdown files concerning development
#   - Folder build/ <= created only to store the .zip file
#   - Folder tests/ <= not needed for the plugin
# - Upload the .zip file as an artifact to the action (this step is possibly optional)
# - Upload the .zip file as a release, for download
name: Generate Installable Plugin, and Upload as Release Asset
on:
    release:
        types: [published]
jobs:
    build:
        name: Upload Release Asset
        runs-on: ubuntu-latest
        steps:
            - name: Checkout code
              uses: actions/checkout@v3

            - name: Setup PHP
              uses: shivammathur/setup-php@v2
              with:
                  php-version: '8.0'
                  tools: composer:v2

            - name: Install Composer dependencies
              uses: ramsey/composer-install@v2
              with:
                  composer-options: "--no-progress --no-ansi --no-interaction --no-dev"
                  ignore-cache: "yes"

            - name: Build project
              run: |
                  npm install
                  npm run build
                  rm -rf node_modules
                  mkdir hello-user
                  rsync -a ./ ./hello-user --exclude hello-user
                  mkdir artifact
            - name: Create artifact
              uses: montudor/action-zip@v1
              with:
                  args: zip -X -r artifact/hello-user-${{ github.event.release.tag_name }}.zip hello-user -x *.git* node_modules/\* .* "*/\.*" CODE_OF_CONDUCT.md CONTRIBUTING.md ISSUE_TEMPLATE.md PULL_REQUEST_TEMPLATE.md *.dist *composer.* *tests** *vendor* *src* *package*
            - name: Upload artifact
              uses: actions/upload-artifact@v3
              with:
                  name: hello-user
                  path: artifact/hello-user-${{ github.event.release.tag_name }}.zip
            - name: Upload to release
              uses: JasonEtco/upload-to-release@master
              with:
                  args: artifact/hello-user-${{ github.event.release.tag_name }}.zip application/zip
              env:
                  GITHUB_TOKEN: ${{ secrets.GH_TOKEN }}
```

### Step 2
You will need to create a personal access token for the last GitHub action to use. Go to your GitHub profile, then Settings > Developer Settings > Personal access tokens. Create a new token with the `repo` scope. Copy the token and save it somewhere safe. You will need it in the next step.

### Step 3
Add the token as a secret in your repository. Go to your repository Settings > Secrets. Create a new secret with the name `GH_TOKEN` and paste the token you just created as the value.

### Step 4
Time to do our first release! Go to the Releases tab in your repository. Click on the Draft a new release button. Enter a tag version, that matches the version in your plugin header. For example, `v1.0.0`.

Get the release title and description ready and click on Publish release.

### Step 5
Go to the Actions tab in your repository. You should see a new workflow running. Wait for it to finish. Once it's done, go to the release page and check if the zip file is there.
