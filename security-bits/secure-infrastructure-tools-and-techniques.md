---
description: >-
  The tools and techniques found handy by me to strengthen one's infra security
  have been shared in this post.
---

# Secure Infrastructure: Tools & Techniques

Using tools to automate security checks in the path to production itself is a effective way to deliver quality features and product. I learnt a few during the recent pen testing workshop I attended.

This is an attempt to share the tools and techniques to secure the infrastructure setup of your product found effective by me in my day to day work.\
If you know more tools or better capabilities of the tools please do share by creating a pull request on the repository.

## AWS Security Hub

* This tool gives  a comprehensive view of the high-priority security alerts and security posture across the AWS accounts.
* One can run automated, continuous security checks based on industry standards and best practices, such as the Center for Internet Security (CIS) AWS Foundations Benchmark and Payment Card Industry Data Security Standard (PCI DSS).

{% hint style="success" %}
Security Hub provides the remediation too which is linked with each misconfiguration.
{% endhint %}

## AWS IAM Policy Simulator

* One can use the IAM Policy Simulator to make sure the set of permissions is behaving the way we intend it to.&#x20;
* With the IAM policy simulator, we can test and troubleshoot identity-based policies, IAM permissions boundaries, Organizations service control policies and resource-based policies.

## AWS GaurdDuty

* One can review the findings of Guard Duty using this service that monitors VPC Flow Logs, AWS CloudTrail event logs and DNS logs to identify unexpected and potentially unauthorized malicious activity within the AWS environment.

## ScoutSuite

* This is a open source multi-cloud security-auditing tool, which enables security posture assessment of cloud environments.
* It uses the APIs exposed by the cloud providers(AWS, GCP and Azure) and gathers configuration data for manual inspection and highlights risk areas.
* One can run this tool against the instance and it generates a report highlighting the risk

{% hint style="success" %}
You can easily integrate the scanning with the CI/CD tool to weekly generate the report and take approriate action by safeguarding the deployments to production.\
One can fail the build if the danger level findings are reported.
{% endhint %}

## AWS Extender CLI

* This tools scans for misconfigurations in AWS S3 buckets
* The command line tool helps by listing the permissions of the S3 buckets and accordingly we can take appropriate actions

## Aquasec

* Aqua scans VMs, container images and serverless functions for known vulnerabilities, embedded secrets & malware
* You can integrate this scanning with your CI/CD solution to provide feedback periodically by safeguarding the deployments to production
* If the fix version of the dependency is available, it recommends the version to be used.
* If the finding is not applicable or the a fix version is not available and the threat model is  acceptabel, you can also acknowledge the  risk  to make the image or function compliant.

{% hint style="info" %}
I have seen it in action to scan vulnerabilities in containers and functions. Application dependencies too are scanned using Trivy but I would rely on this tool for the former reports (container and functions).
{% endhint %}

## Tenable.IO

* Tenable is used by us to scan VMs for known vulnerabilities.
* It can support accurate identification and investigation.

## **Shodan**

* Shodan can be used to report open ports and vulnerabilities in the services running on those ports
* We have found useful flags about open uncommon ports and expiration of SSL certificates

## RhinoSecurityLabs' Pacu

* Pacu is a toolkit designed for offensive security practitioners focusing on the misconfigurations in AWS
* Pacu can simulate a breach in case of the compromised credentials
* I have not used this but was shown a demo during a pen-testing workshop

{% hint style="info" %}
Once this tool has the compromised credentails, it can exploit S3 buckets, Lambda backdoor functions, EC2 instances, Escalate privileges, Disrupt monitoring and logging to provide a assessment of one's AWS infratsructure setup
{% endhint %}

## FSecureLabs' AWSPX

* awspx uses policy information to visualize which actions would affect which resources to produce attack paths.&#x20;

## Manual Review

* Checking for the rules/policies/ACL policies for **the **_**wildcard character asterisk (\*)**_  under action and resource helps determine the risk associated with any wide permissions
  * To configure many AWS services, one passes an _**IAM role to the service - iam:PassRole**_ and therefore the policy should be reviewed to avoid using asterisk (\*) as that could enable one to perform privilege escalation
* **Usage of n+1 accounts  in AWS is recommended**
  * One should lock down the 1st account which holds the user and groups and nothing except IAM to manage their account should be enabled
  * The 2nd account that has all the necessary roles and policies to access everything else should be created
  * Members should log into 1st account and then  assume role in 2nd account
  * This helps achieve the isolotion and separation of concerns
* The _**Security Command Center (SCC) and IAM audit in GCP**_ highlights issues in the configurations that can  be mitigated to secure the infra
*   **`nmap` scripts** that can be used to check for the _**below vulnerabilities**_

    1.  Check for _**Sweet32 vulnerability**_ using

        **`nmap -sV --script ssl-enum-ciphers -Pn <IP>`**
    2.  Identify _**BEAST attack**_ if the cipher algorithm is CBC along with TLS v1.0 and SSLv3.0 (and below) _\*\*_

        **`nmap -sV --script ssl-enum-ciphers -Pn <IP>`**
    3.  Check for the _**vulnerable services running on open ports**_ using

        **`nmap -sV -Pn <IP>`**\
        From the identified versions, vulnerabilities can be found from the ‘Browse all versions’ tab in the cve-details page. _\*\*_
    4.  To check for _**Multiple SSL Certificate**_ _**vulnerabilities**_&#x20;

        **`nmap -sC -Pn <IP>`**
    5. To check for _**Multiple SSH vulnerabilities**_ **`nmap --script ssh2-enum-algos <IP>`** 
    6. To check for _**weak Diffie-Hellman key exchange algorithm**_ _**vulnerabilities**_ **`nmap --script ssl-dh-params <IP>`**


* Deployment/Configuration Management Tool  (Ansible/Chef):
  * We should _**review how the credentials**_ (AWS access keys, database credentials etc) _**are stored**_ (can look for knife data bags/pem files)
* CI/CD setup:
  1. We should review how the credentials (AWS access keys, Github credentials etc) are stored
  2. We should make _**sure the service is not open to the internet**_. Ideally one should limit the number of services, and the number of services listening on non-local interfaces. If it needs to be open to the internet, review the authentication mechanism. Many are insecure by default.
  3. Think about setting up a firewall that whitelists only specific ports from expected hosts or addresses.
  4. We should also _**review the output of the build runs**_ such that SSH keys, database credentials, Git credentials, etc, are not logged.
* Database:
  * We should review that _**no ports should be open to the public**_
  * Normally _**authentication is not applied by default**_ or the authentication is just admin:admin so the authentication should be reviewed.
* **Message brokers** (RabbitMq, Kafka), **Search technologies** (Apache Solr, Kibana), **Distributed resource management** (Kubernetes, Mesos or Marathon) tools:
  * We should make sure the service is not open to the internet. Ideally we should limit the number of services, and the number of services listening on non-local interfaces.
