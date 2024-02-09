# Deploy and Copy configs

Deploy and Copy configs action helps to deploy unpublished theme and merge settings from live theme.

Steps:
- setup node, ruby and Shopify CLI
- checkout theme source code from git repo
- pull settings from live theme
- push merged theme as a new unpublished theme copy

## Environmet variables
- **SHOPIFY_FLAG_STORE**: Required. Store URL, like your-store.myshopify.com
- **SHOPIFY_CLI_THEME_TOKEN**: Required. Password generated from Theme Access app
- **SHOPIFY_CLI_TTY**: Required. Pass this variable with a value of ```0``` to turn off interactive prompts. You might want to use this variable if your Shopify CLI pipeline step is timing out.

## Inputs

- **theme-name**: Optional. Text prefix for theme (Default value: Test Theme For Review -)
- **config-files**: Optional. List of files to pull from live theme (Default value: --only templates/*.json --only sections/*.json --only config/settings_data.json)
- **dest**: Optional. Set update mode. Full or incremental. Set "new" to delete theme and upload all theme files (shopify share). Set "search_or_new" for incremental theme update (shopify push). (Defalut value: search_or_new)

## Outputs
- **preview-theme-id** return the id of updated theme

## Usage example 

```yaml
name: Deploy Test
on: 
  workflow_dispatch:
  push:
    tags:
      - test*
jobs:
  ######
  # store 0
  #
  deploy-store-0:
    name: Deploy on ${{ vars.STORE_0_NAME }}
    runs-on: ubuntu-latest
    env:
      # Store URL, like your-store.myshopify.com
      SHOPIFY_FLAG_STORE: '${{ vars.STORE_0_URL }}'
      # Password generated from Theme Access app
      SHOPIFY_CLI_THEME_TOKEN: ${{ secrets.STORE_0_PASSWORD }}
      SHOPIFY_CLI_TTY: 0
    steps:
      - id: deploy
        uses: alexanderlukjanenko/deploy-and-copy-configs@v1
      - name: Add job summary
        run: echo "${{ vars.STORE_0_NAME }} | ${{ vars.STORE_0_URL }} | ${{ steps.deploy.outputs.preview-theme-id }}" >> $GITHUB_STEP_SUMMARY
  ######
  # add the same job for each of your store
  #
  ...
```

## License

This repository is licensed under the MIT license. See the [LICENSE.md](LICENSE.md) file for details.
