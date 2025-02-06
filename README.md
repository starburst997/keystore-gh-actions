# keystore-gh-actions

Generate a .keystore using Github Actions which can then be used to sign your Android App easily.

The resulting .keystore will be available as a secret in the repository or you can download an artifact containing the files.

Both **PKCS12** and **JKS** variant are created for convenience.

## Usage

We cannot use password input securly in Github Action ([more info](https://github.com/orgs/community/discussions/12764)), so instead you need to create two secrets containing your password before running the action:

1. `ANDROID_KEYALIAS_PASS`: Your alias password
2. `ANDROID_KEYSTORE_PASS`: Your keystore password (can use the same for both)

Create a [Personal Access Token (PAT)](https://github.com/settings/tokens) with all repo access so the action can create secrets for you and save it as a secret named `GH_PAT` (*optional*).

After running the action you'll get additonal secrets created in your repository:

1. `ANDROID_KEYALIAS_NAME`: Your alias
2. `ANDROID_KEYSTORE_BASE64`: base64 value of your PKCS12 keystore
3. `ANDROID_KEYSTORE_JKS_BASE64`: base64 value of your JKS keystore

With all of those secrets, you can now use your .keystore as you see fit in other Github Actions.

## Create workflow file

Create a workflow file in your repository (`.github/workflows/keystore.yml`) and reference this workflow (you can always copy the original)

#### keystore.yml ([original](https://github.com/starburst997/keystore-gh-actions/blob/v1/.github/workflows/keystore.yml))
```yml
name: Generate .keystore

on:
  workflow_dispatch:
    inputs:
      alias:
        type: string
        description: Alias (anything you want)
      #password:
      #  type: password
      #  description: Password
      name:
        type: string
        description: Your Name
      organization:
        type: string
        description: Organization Name
      locality:
        type: string
        description: Locality / City
      state:
        type: string
        description: State / Province
      country:
        type: string
        description: Country (2 letter code)
      save-secrets:
        description: "Save secrets"
        type: choice
        default: 'true'
        options:
          - true
          - false
      upload-artifact:
        description: "Get an artifact to download"
        type: choice
        default: 'false'
        options:
          - true
          - false

jobs:
  setup:
    uses: starburst997/keystore-gh-actions/.github/workflows/keystore.yml@v1
    secrets: inherit
    with:
      alias: ${{ inputs.alias }}
      #password: ${{ inputs.password }}
      name: ${{ inputs.name }}
      organization: ${{ inputs.organization }}
      locality: ${{ inputs.locality }}
      state: ${{ inputs.state }}
      country: ${{ inputs.country }}
      save-secrets: ${{ inputs.save-secrets }}
      upload-artifact: ${{ inputs.upload-artifact }}
```

*Notes: `save-secrets` / `upload-artifact` use **string** as boolean, so you need to use `'true'` / `'false'`*

Simply run the workflow from the action tab, you'll have to input a few info and that's it.

Running the action again won't replace the auto-generated secrets, you need to delete them first.

Adapt as you see fit in your CI/CD workflow.