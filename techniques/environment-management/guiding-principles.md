# Guiding Principles

Environment Management in flxbl project follow guidelines as laid out by the [Salesforce Well Architected ](https://architect.salesforce.com/well-architected/adaptable/resilient)guide,  Rehashing some of the most important principles here

* **Release stages apply to release artifacts, not environments**.&#x20;
* _**Every**_**&#x20;development environment should be refreshed or destroyed as soon as the relevant work is finished (within limits)**.&#x20;
* **Environments are only as useful as the kind of production fidelity they do (and do not) provide. Optimize your environment setup workflows or automations to allow for environment teardown/refresh at the earliest possible interval**.&#x20;

In order to cater the above requirements, flxbl projects work with the concept of environment categories, any number of sandboxes can be added to any of these categories.  These environment categories form the deployment target in your pipelines.



<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Test Environments</p></figcaption></figure>

In addition to the above categories, flxbl project utilize three categories of development environments

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption><p>Development Environments</p></figcaption></figure>



