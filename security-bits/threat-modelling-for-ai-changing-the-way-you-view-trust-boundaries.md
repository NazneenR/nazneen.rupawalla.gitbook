# Threat Modelling for AI - Changing the way you view Trust Boundaries

This blog will highlight the frameworks that currently exist to prepare for threat modelling products/services interacting with or taking dependencies on AI/ML-based services

This is an interesting topic for me because businesses eager to harness the potential of LLMs, are rapidly integrating them into their operations but these use cases challenge the current set understanding of the trust boundaries as well as has outpaced the establishment of comprehensive security protocols, leaving many applications vulnerable to high-risk issues. \


### What can help us navigate this ambiguity and secure AI based applications?

> Threat Modelling can help with understanding how do the trust boundaries change with respect to creating products/services interacting with or taking dependencies on AI/ML-based services or for products/services being built with AI/ML at their core

With threat modeling, we can get a better picture early on to think through these scenarios to safeguard our models and the apps relying on these models. We can predict some of these threats and and plan approaches to deal with them via threat modelling

Threat modelling is a risk-based approach to designing secure systems. It is based on identifying threats in order to develop mitigations to them. With cyber security risk increasing and enterprises becoming more aware of their liabilities, and the expectation to ride the AI wave and deliver demos quickly, software delivery teams should ideally start with a threat modelling exercise to iron out the considerations that could become costly later.

### But how do we get started with threat modelling LLMs?&#x20;

We can use the list created by [OWASP for LLM](https://llmtop10.com/) where they have created an actionable resource, with a set of vulnerabilities, along with common examples, prevention tips, attack scenarios, and references and use this resource to build the questions to ask in a threat modeling exercise.&#x20;

### Questions:

Here is the list of questions I have collated for helping me with threat modelling for AI. This is by no means a exhaustive list but should be treated as a starting point. The STRIDE methodology for web applications still stands tall and the below questions are only in addition to those mentioned here - [https://thoughtworksinc.github.io/sensible-security-conversations/](https://thoughtworksinc.github.io/sensible-security-conversations/)&#x20;

1. **Prompt Injection:** Attackers can manipulate LLMs through crafted inputs, causing it to execute the attacker's intentions
   * Attack Scenarios:
     * A malicious user uploads a resume with a prompt injection. Due to the prompt injection, the LLM says yes, despite the actual resume contents
     * An attacker provides a direct prompt injection to an LLM-based support chatbot. The injection contains “forget all previous instructions” and new instructions to query private data stores are executed.
   * **Questions:**
     * What kind of input validation and sanitization are you doing on that content?
     * Does your LLM follow the principle of least privilege?
     * Do privileged operations require a human to approve the action? \

2. **Insecure Output Handling:** Insecure Output Handling is a vulnerability that arises when a downstream component blindly accepts LLM output without proper scrutiny
   * Attack Scenarios:
     *   An application utilizes an LLM plugin to generate responses for a chatbot feature.

         However, the application directly passes the LLM-generated response into an internal function without proper validation.

         This allows an attacker to manipulate the LLM output to execute arbitrary commands
   * **Questions:**
     * Is your LLM data directly being entered into a system shell or similar functions?
     * Is the output being encoded?\

3. **Training Data Poisoning:** Manipulation of the data to introduce vulnerabilities, backdoors or biases that could compromise the model’s security, effectiveness or ethical behavior.&#x20;
   * Attack Scenarios:
     * The victim model trains using falsified information which is reflected in outputs of generative AI prompts to its consumers.
     * If the training data is not correctly filtered and/or sanitized, a malicious user of the application may try to influence and inject toxic data into the model
   *   **Questions:**

       * If your data is poisoned or tampered with, how would you know?
       * What telemetry do you have to detect a skew in the quality of your training data?
       * Are you training from user-supplied inputs?&#x20;
       * Are you ensuring sufficient sandboxing to prevent the model from scraping unintended data sources?


4. **Model Denial of Service:** An attacker interacts with an LLM in a method that consumes an exceptionally high amount of resources, which results in a decline in the quality of service for them and other users, as well as potentially incurring high resource costs.
   * Attack Scenarios:
     * An attacker continuously bombards the LLM with input that exceeds its context window. The attacker may use automated scripts or tools to send a high volume of input, overwhelming the LLM's processing capabilities. As a result, the LLM consumes excessive computational resources, leading to a significant slowdown or complete unresponsiveness of the system.
   *   **Questions:**

       * Do you cap resource use per request or step?
       * Do you continuously monitor the resource utilization of the LLM to identify abnormal spikes or patterns that may indicate a DoS attack?
       * Do you enforce API rate limits to restrict the number of requests an individual user or IP address can make within a specific timeframe?
       * Do you limit the number of queued actions and the number of total actions in a system reacting to LLM responses?


5. **Supply Chain Vulnerabilities:** The supply chain in LLMs can be vulnerable, impacting the integrity of training data, ML models, and deployment platforms. These vulnerabilities can lead to biased outcomes, security breaches, or even complete system failures.
   * Attack Scenarios:
     * An attacker exploits a vulnerable Python library to compromise a system. This happened in the first Open AI data breach.
   *   **Questions:**

       * Do you implement a patching policy to mitigate vulnerable or outdated components?
       * Do you have a procedure to vet the data sources?
       * Do you implement monitoring to cover component and environment vulnerabilities scanning, use of unauthorized plugins, and out-of-date components, including the model and its artifacts?


6. **Sensitive Information Disclosure:** LLM applications can inadvertently disclose sensitive information, proprietary algorithms, or confidential data, leading to unauthorized access, intellectual property theft, and privacy breaches.&#x20;
   * Attack Scenarios:
     * Personal data such as PII is leaked into the model via training data due to either negligence from the user themselves, or the LLM application. This case could increase risk and probability of the attack
   *   **Questions:**

       * Is the classification done to record the sensitivity of your training data?
       * How are you ensuring proper filtering of sensitive information in the LLM responses?
       * Is access to external data sources (orchestration of data at runtime) limited?
       * Do you prevent overfitting?
       * Is the rule of least privilege applied?
       * How do you make sure to not train the model on information that the highest-privileged user can access which may be displayed to a lower-privileged user?


7. **Insecure Plugin Design:** LLM plugins are extensions that, when enabled, are called automatically by the model during user interactions. They are driven by the model, and there is no application control over the execution.
   * Attack Scenarios:
     * An attacker uses indirect prompt injection to exploit an insecure code management plugin with no input validation and weak access control to transfer repository ownership and lock out the user from their repositories
   *   **Questions**:

       * Do you apply a layer of typed calls where parsing of requests is introduced and strict parameterized input is not possible?
       * Does your plugin design consider least-privilege access control?
       * Do your plugins use appropriate authentication identities, such as OAuth2, to apply effective authorization and access control?
       * Do you use API keys to provide context for custom authorization decisions which reflect the plugin route rather than the default interactive user?
       * Is manual user authorization and confirmation of any action taken required by your sensitive plugins?


8. **Excessive Agency:** An LLM-based system is often granted a degree of agency by its developer - the ability to interface with other systems and undertake actions in response to a prompt.
   * **Attack Scenarios:**
     * An LLM-based personal assistant app is granted access to an individual’s mailbox via a plugin in order to summarize the content of incoming emails. To achieve this functionality, the email plugin requires the ability to read messages, however the plugin that the system developer has chosen to use also contains functions for sending messages. The LLM is vulnerable to an indirect prompt injection attack, whereby a maliciously-crafted incoming email tricks the LLM into commanding the email plugin to call the ‘send message’ function to send spam from the user’s mailbox.
   *   **Questions:**

       * Do you limit the functions that are implemented in LLM plugins/tools to the minimum necessary?
       * Do you track user authorization and security scope to ensure actions taken on behalf of a user are executed on downstream systems in the context of that specific user?
       * Do you implement authorization in downstream systems rather than relying on an LLM to decide if an action is allowed or not?
       * Do you log and monitor the activity of LLM plugins/tools and downstream systems to identify where undesirable actions are taking place, and respond accordingly?


9. **Overreliance** (very human vulnerability for a tech list): Overreliance on LLMs can lead to serious consequences such as misinformation, legal issues, and security vulnerabilities. It occurs when an LLM is trusted to make critical decisions or generate content without adequate oversight or validation.
   * Attack Scenarios:
     * AI fed misleading info leading to disinformation&#x20;
     * AI's code suggestions introduce security vulnerabilities
     * Developer unknowingly integrates malicious package suggested by AI.
   *   Questions:

       * What is the process in place to regularly monitor and review LLM outputs?
       * Do you cross-check LLM outputs with trusted sources?


10. **Model Theft:** LLM model theft involves unauthorized access to and exfiltration of LLM models, risking economic loss, reputation damage, and unauthorized access to sensitive data. Robust security measures are essential to protect these models.
    * Attack Scenarios:&#x20;
      * An attacker queries the API with carefully selected inputs and collects sufficient number of outputs to create a shadow model.
      * A security control failure is present within the supply-chain and leads to data leaks of proprietary model information
    * **Questions**:
      * Is RBAC implemented?
      * Is rate limiting implemented?
      * Will extraction queries be detected?
      * What are the policies in place to recover from a theft or breach?&#x20;

\
This is my attempt to make sense of various frameworks out there with common attack scenarios, vulnerabilities and references that aim to bridge the divide between general application security principles and the specific challenges posed by LLMs.\
\
As you attempt to threat model LLMs, do share your questions so we all practice and get better at this together. Share your questions [here](threat-modelling-for-ai-changing-the-way-you-view-trust-boundaries.md).
