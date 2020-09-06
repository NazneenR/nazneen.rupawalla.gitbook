# AWS account access using saml2aws and assume-role

I have recently had the need to log into AWS using SSO using the CLI and assume role into other AWS account. 

The below commands will help you assume role into the AWS account:

* [saml2aws](https://github.com/Versent/saml2aws): 

  `saml2aws configure` 

  1. This command will prompt you to choose a idp and the mfa option
  2. Post that, you will need to add the aws profile and the AWS instance url connected with the federated identity
  3. Once you add the username and password, a file named ~/.aws/credentials will be created. Sensitive credential information would be stored in this file.

  `saml2aws login -p <profile_name>` 

  1. This command will login into the set AWS account using temporary credentials.
  2. `<profile_name>`is the profile set during configuration, you have privilege to assume role into another account.

You would need to use [version 2 of aws cli](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) if you would want to login using sso. Using saml2aws is a convenient wrapper to use the command line to work with AWS services. It works with most of identity providers.

{% hint style="danger" %}
If you want to use the password-less option of your idp, using aws cli would be beneficial as I have not been able to set the same for saml2aws yet.
{% endhint %}

* [assume-role](https://github.com/remind101/assume-role):

  `assume-role <profile you want to assume role into>`

  * This command sets `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` and `AWS_SESSION_TOKEN` for the given role
  * The role we want to assume would need a profile setup in `~/.aws/config` where in the role\_arn you can assume should be mentioned.

{% hint style="info" %}
Use eval command with the assume-role command to configure your shell to use the temporary credentials - `eval $(assume-role <profile>)`
{% endhint %}

These tools came in handy for me and hope you find it too.

