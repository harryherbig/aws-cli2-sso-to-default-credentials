# aws cli v2 sso login to default credentials export

## Goals of this project
- Use session token from SSO login across shell sessions
- Use SSO Login within IntelliJ with the aws-toolkit plugin 

## Prerequisites
- AWS Account with SSO enabled
- aws cli v2

## Steps
### 1. Configure sso for cli

run this command to create a sso profile, which is added to `$HOME/.aws/config`, allowing to set the profile name

    aws configure sso

Hint: `sso_start_url = https://d-${ACCOUNT_ID}.awsapps.com/start#/`


### 2. Copy or create your script at your favourite location
this example uses `$HOME/.aws` for storing the script from this repository

example creation with pbcopy: (use `cp`, `ln -s`, or whatever you prefer)

copy the contents of the `setDefaultCredentialsFromSsoToken` to your clipboard and run

    touch $HOME/.aws/setDefaultCredentialsFromSsoToken
    pbpaste > $HOME/.aws/setDefaultCredentialsFromSsoToken
    chmod +x $HOME/.aws/setDefaultCredentialsFromSsoToken

### 3. create a zsh alias

add a line like the following to `$HOME/.zsh_aliases`

replace the profile name; if necessary also replace the script name & location

    alias awssso="AWS_PROFILE=my-fancy-sso-profile zsh -c '$HOME/.aws/setDefaultCredentialsFromSsoToken'"

## Usages
### terminals

For fetching and persisting credentials for your sso account run your alias, e.g.

    awssso

The invoked script will:
- run `aws sso login`, opening a browser window with your sso_start_url so you can login with your sso providers credentials.
- run `aws sso get-role-credentials` with the returned session tokens
- write a new `$HOME/.aws/credentials` with the derived temporary credentials, backing up the old file (with pattern `credentials-${epoch_timestamp}`)

### Using the aws-toolkit with AWS SSO Logins
:warning: You need to invoke your alias in your favorite terminal first.

Link to the setup: https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/setup-toolkit.html

The IntelliJ Plugin sadly does not work [yet](https://github.com/aws/aws-toolkit-jetbrains/issues/1360) with SSO Logins, but this script sets credentials to the default profile, which can be used easily in IntelliJ to provide Credentials for the `DefaultAWSCredentialsProviderChain`
