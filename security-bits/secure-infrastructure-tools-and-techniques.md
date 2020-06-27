---
description: >-
  The tools and techniques found handy by me to strengthen one's infra security
  have been shared in this post.
---

# Secure Infrastructure Tools & Techniques

Using tools to automate security checks in the path to production itself is a effective way to deliver quality features and product.

This is an attempt to share the tools and techniques to secure the infrastructure setup of your product found effective by me in my day to day work.  
If you know more tools or better capabilities of the tools please do share by creating a pull request on the repo.

## AWS Security Hub

* This tool gives  a comprehensive view of the high-priority security alerts and security posture across the AWS accounts.
* One can run automated, continuous security checks based on industry standards and best practices, such as the Center for Internet Security \(CIS\) AWS Foundations Benchmark and Payment Card Industry Data Security Standard \(PCI DSS\).

{% hint style="success" %}
Security Hub provides the remediation too which is linked with each misconfiguration.
{% endhint %}

## AWS IAM Policy Simulator

* One can use the IAM Policy Simulator to make sure the set of permissions is behaving the way we intend it to. With the IAM policy simulator, we can test and troubleshoot identity-based policies, IAM permissions boundaries, Organizations service control policies, and resource-based policies.

## AWS GaurdDuty

* You can review the findings of Guard Duty using this service that monitors VPC Flow Logs, AWS CloudTrail event logs, and DNS logs to identify unexpected and potentially unauthorized and malicious activity within the AWS environment.

## ScoutSuite

* This is a open source multi-cloud security-auditing tool, which enables security posture assessment of cloud environments.
* It uses the APIs exposed by the cloud providers\(AWS, GCP and Azure\) and gathers configuration data for manual inspection and highlights risk areas.
* One can run this tool against the instance and it generates a report highlighting the risk

{% hint style="success" %}
You can easily integrate the scanning with the CI/CD tool to weekly generate the report and take approriate action by safeguarding the deployments to production.  
One can fail the build if the danger level findings are reported.
{% endhint %}

## Aquasec

* Aqua scans VMs, container images and serverless functions for known vulnerabilities, embedded secrets, configuration and permission issues, malware and open-source licensing
* You can integrate this scanning with your CI/CD solution to provide feedback periodically by safeguarding the deployments to production

{% hint style="info" %}
I have seen it in action to scan vulnerabilities in containers and functions. Application dependencies too are scanned using Trivy but I would rely on this tool for the former reports \(container and functions\).
{% endhint %}

## AWS Extender CLI

* This tools scans for misconfigurations in AWS S3 buckets
* The command line tool helps by listing the permissions of the S3 buckets and accordingly we can take appropriate actions

## Tenable.IO

* Tenable is used by us to scan VMs for known vulnerabilities.
* It can support accurate identification and investigation.

## **Shodan**

* Shodan can be used to report open ports and vulnerabilities in the services running on those ports
* We have found useful flags about open uncommon ports and expiration of SSL certificates

## Manual Review

* Checking for the rules/policies/ACL policies for the wildcard character \* under action and resource helps determine the risk associated with any wide permissions
* To configure many AWS services, one passes an IAM role to the service - iam:PassRole. It is useful to review if the pass role policy uses asterisks \(\*\) because that could enable one to perform privilege escalation.
* I have recently learnt about nmap scripts and use them to check for the below vulnerabilities

  1. Check for Sweet32 vulnerability using

     **`nmap -sV --script ssl-enum-ciphers -Pn <IP>`**

  2. Identify BEAST attack if the cipher algorithm is CBC along with TLS v1.0 and SSLv3.0 \(and below\) _\*\*_

     **`nmap -sV --script ssl-enum-ciphers -Pn <IP>`**

  3. Check for the vulnerable services running on open ports using

     **`nmap -sV -Pn <IP>`**  
     From the identified versions, vulnerabilities can be found from the ‘Browse all versions’ tab in the cve-details page. _\*\*_

  4. To check for Multiple SSL Certificate related vulnerabilities **`nmap -sC -Pn <IP>`**
  5. To check for Multiple SSH vulnerabilities **`nmap --script ssh2-enum-algos <IP>`** 
  6. To check for weak Diffie-Hellman key exchange algorithm vulnerabilities **`nmap --script ssl-dh-params <IP>`** 

