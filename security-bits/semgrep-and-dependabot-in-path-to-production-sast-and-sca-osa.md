# Semgrep and Dependabot in path to production - SAST and SCA Tooling

## SAST and SCA embedded in the path to production

Prior to using Semgrep and Dependabot, I used Hawkeye extensively for detecting security vulnerabilities in custom code and in open source libraries.   
You are wondering why does the title state semgrep and dependabot and then I start this note about using Hawkeye.   
Well, recently, as it was announced as not maintained anymore, I started looking for alternatives fulfilling these requirements in existing toolchain and non-commercial tools

* Should detect vulnerabilities in a number of languages - I do not want the cognitive load of integrating a new tool for a new language in my ecosystem
* Should be easy to integrate in my path to production flow - I believe that any tool or process should be development first else these become bottlenecks towards delivery at speed
* Should cover IAC - Hawkeye did not cover this but as I started looking for alternatives, I added this to the requirements as detection of misconfigurations in languages and frameworks was a gap in my toolchain

Unfortunately I did not get one tool that did all but I stumbled upon two different tools that are extremely promising. 

Commercial tools like Veracode, Snyk, Checkmarx can cover these requirements but I am not covering my experiences with these tools in this blog. If it works out financially, definitely give these tools a try.  
  
For now, I shall share my experience of using semgrep and dependabot.   
For IaC, I will cover the tools ad frameworks such as cfn\_nag, inspec, tfsec and dev-sec.io in another blog post.

## Semgrep

As it is mentioned on [their site](https://r2c.dev/), semgrep enforces security on every commit.  
This tool detects vulnerabilities in custom code. It falls under the category of SAST

### A few highlights:

* It supports [15+ languages](https://semgrep.dev/docs/#language-support) as of today
* The [ruleset](https://semgrep.dev/explore) covers a number of scenarios - owasp top 10, command injection, xss, secrets, .....
  * It also covers frameworks like flask, react, javascript
* Though the configuration files analysis is still in beta, it helped me detect a few medium severity configurations in dockerfile which definitely caught my attention and prompted me try the various rulesets
* The [CI support and documentation](https://semgrep.dev/docs/semgrep-ci/sample-ci-configs/) for the same made it easy to integrate into the path to production 

### An example using Github actions

```yaml
Under workflows folder, you can create semgrep.yml
```

```yaml
name: Semgrep
on:  
  # Scan changed files in PRs, block on new issues only (existing issues ignored)  
  pull_request: {}
  
jobs:  
  semgrep:    
  name: Scan    
  runs-on: ubuntu-latest    
  # Skip any PR created by dependabot to avoid permission issues    
  if: (github.actor != 'dependabot[bot]')    
  steps:      
  # Fetch project source      
    - uses: actions/checkout@v2
     
    - uses: returntocorp/semgrep-action@v1        
    with:         
      config: >- 
        p/ci
        p/secrets
```

### Exceptions and acknowledgement

One can ignore the findings by adding inline comments or by using a .semgrepignore file.   
Details can be found [here](https://semgrep.dev/docs/ignoring-findings/)

### Interesting rules

{% hint style="success" %}
security-audit - This rule is recommended if you are looking to setup guardrails or investigating gaps for further review.  
It generally proceeds false positives that need to be reviewed manually
{% endhint %}

{% hint style="success" %}
ci - This rule is recommended for runtime errors, logic bugs, high-confidence security vulnerabilities.  
Recommended for use in CI to get early feedback before issues from reach production.  
Supports Python, Java, JavaScript, and Go.
{% endhint %}

{% hint style="success" %}
dockerfile - This rule is a linter that runs checks inspired from hadolint
{% endhint %}

{% hint style="success" %}
terraform - There are tools such as tfsec, cng nag, inspect that do a much better job at detecting misconfigurations in IaC but running terraform rule in semgrep is pretty helpful too.
{% endhint %}

Do share with me any rules that you have found useful.

{% hint style="info" %}
You can also add [your custom rules](https://semgrep.dev/docs/writing-rules/overview/) which has drawn most of the security professionals towards this tool
{% endhint %}

## Dependabot

[Github Dependabot](https://docs.github.com/en/code-security/supply-chain-security/managing-vulnerabilities-in-your-projects-dependencies/about-alerts-for-vulnerable-dependencies) supports scanning for security vulnerabilities in third party dependencies.   
  
When our code depends on a package that has a security vulnerability, this tool sends alerts to notify about the vulnerability and suggests the version we should upgrade/update and use.  
Pull requests are created by dependabot once it finds any vulnerability.  
This functionality falls under the category of SCA/OSA 

{% hint style="info" %}
This is available for free for public Github repositories  
For enterprise account, one would have to use the Advanced Security License
{% endhint %}

### **A few useful techniques for setup:**

* **Alerts**:  These alerts are sent to individuals and not to a group address. This tends to be ignored as individual emails are not checked frequently.  For me, group email address is given preference and hence getting a notification via this manner worked over individual email address.    For this, the webhook functionality can be used.  We setup a collector that can be sent this payload whenever there is a vulnerability and then our collector sends a notification to the group email address so that these alerts are not lost in anyone's individual mailbox. You can subscribe to [repository vulnerability alert](https://docs.github.com/en/developers/webhooks-and-events/webhooks/webhook-events-and-payloads#repository_vulnerability_alert) to set this up. 
* **Auto-merge:** Using Github actions, you can also auto merge the pull requests and run your tests for sanity

```bash
Under workflows folder, you can create a dependabot.yml file 
```

```yaml
name: Dependabot auto-merge
on: pull_request_target

permissions:
  pull-requests: write
  contents: write
  
jobs:
  dependabot:
    runs-on: ubuntu-latest
    if: github.actor == 'dependabot[bot]'
    steps:
      - name: Enable auto-merge for Dependabot PRs
        run: gh pr merge --auto --merge "$PR_URL"
        env:
          PR_URL: ${{github.event.pull_request.html_url}}
          GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
```

{% hint style="danger" %}
In case your test coverage is not good \(recommend to increase your test coverage\), you could only auto merge for severity version which is minor
{% endhint %}

```yaml
name: Enable auto-merge for Dependabot PRs
if: ${{steps.metadata.outputs.update-type == 'version-update:semver-minor'}}
run: gh pr merge --auto --merge "$PR_URL"
env:
  PR_URL: ${{github.event.pull_request.html_url}}
  GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
```

### A few considerations before you setup dependabot

{% hint style="danger" %}
Github does not yet support gradle package ecosystem. Supported package ecosystems can be read [here](https://docs.github.com/en/code-security/supply-chain-security/understanding-your-software-supply-chain/about-the-dependency-graph#supported-package-ecosystems). 
{% endhint %}

{% hint style="success" %}
You can use [OSSIndex](https://ossindex.sonatype.org/) to make up for this shortcoming. An example of CI/CD setup can be found [here](https://github.com/guillermo-varela/example-scan-gradle-plugin/blob/master/.github/workflows/gradle.yml)
{% endhint %}

{% hint style="info" %}
It is a good habit to enable the alerts for notifying an update is available for a dependency. These [**version**](https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/enabling-and-disabling-version-updates#enabling-github-dependabot-version-updates) ****alerts should **not** be confused with ****[**security vulnerabilities** ](https://docs.github.com/en/code-security/supply-chain-security/managing-vulnerabilities-in-your-projects-dependencies/configuring-dependabot-security-updates)alerts. Keeping all dependencies upto date is a desirable state but one should also be enabling security updates for the dependencies in addition.
{% endhint %}

### Exceptions and acknowledgement

If the alert is incorrect or located in unused code, you can use the "Dismiss" drop-down and select a reason for dismissing the alert.

Hope you found this useful. Share your experience of using these tools or any other which you have found to be useful. 

