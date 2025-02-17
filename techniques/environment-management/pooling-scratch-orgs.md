# Pooling Scratch Orgs

Scratch Org pools help development teams to solve the time taken to spin up a scratch org, install all dependencies, load sample data and make it ready for development. Another use case for Scratch Org Pools is for a just in time review environments. The following links discuss how sfp enable scratch org pools.

{% embed url="https://youtu.be/09WEqN1emIM" %}

{% embed url="https://medium.com/@dieffrei/introduction-to-salesforce-scratch-org-pools-e1616772499c" %}

{% embed url="https://medium.com/@dieffrei/dx-scale-scratch-org-pools-1dbecfcda8f8" %}

The following sections deal with the typical pool strategies followed by **flxbl** practitioners.

## Number of Pools Required

At the minimum, flxbl requires three scratch org pools to be created. These pools serve different purposes:

*   **Review pools with all packages installed**

    The review pools are typically used for validating and reviewing changes. They are pre-installed with the packages from the latest successful build, which allows optimised validations where only the packages that have changed are validated.
*   **Review pools with only dependencies installed**

    Scratch orgs in this pool only have managed packages installed. They are used in special cases where a clean install of all the packages is needed, such as for failures involving picklist value updates. Switching to this pool is handled by pipeline runtime variable or a specific commit message.
* **Developer Pool** These are the scratch orgs that will be used by developers to do their development work. They can either have all of the packages installed or just the dependencies.

The number of scratch orgs in the pool have to be determined depending on the size of the program and adjusted accordingly.

## Replenishing Pools

Scratch Org pools need to be replenished in the following manner.

* **Review pools with full packages installed** Replenished periodically through out the day, completely deleted at end of the day and recreated in the very early hours of the day to provide a fresh set of pools each day. These pools will have the expiry set to 2 day only.
* **Review pools with only dependencies installed** Replenished periodically through out the day, completely deleted at end of the day and recreated in the very early hours of the day to provide a fresh set of pools each day. These pools will have the expiry set to 2 day only.
* **Developer Pools** Replenished periodically through out the day, never deleted automatically. Scratch orgs are left to expire by itself. The expiry for each org is set to 15 days and developers need to be aware of the expiry and should be encouraged to work on feature/tasks that should be less than these number of days and should fetch a new org from the pool

Pipelines should be built and invoked using a scheduler to replenish the scratch orgs in the pool.

## Limiting Packages in Pools

As the number of packages in your repository increases, it would become a significant hurdle to build scratch org pools with all the packages in your repository.  sfp provide options to limit the packages installed by using domain.&#x20;

## Recreating Pools

There would be situations when pools need to be killed, such as change in shape of the org or due to any other inconsistencies. This involves a significant downtime, as creating pool with full strength could take anywhere between 1-3 hours depending on the number of scratch orgs involved. This should be communicated to all the developers in the team workspace to let them know this is happening.

It is highly recommended to build a delete pool pipeline that could be triggered manually to delete all the pools.

## Monitoring Pools

Scratch Org Pools can be monitored using an analytic tool like DataDog or New Relic. Check sfp documentation to understand the [metrics](https://app.gitbook.com/s/YLI5Ts7pWhWQV9UaBn3H/command-guide/advanced/metrics) emitted by sfp. A sample dashboard is attached below



<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

To learn more about how to build a pool of scratch orgs using sfp, click on the link below:

{% content-ref url="https://app.gitbook.com/s/YLI5Ts7pWhWQV9UaBn3H/environment-management/pools/scratch-org-pools/pool-operations/preparing-pools" %}
[Preparing pools](https://app.gitbook.com/s/YLI5Ts7pWhWQV9UaBn3H/environment-management/pools/scratch-org-pools/pool-operations/preparing-pools)
{% endcontent-ref %}
