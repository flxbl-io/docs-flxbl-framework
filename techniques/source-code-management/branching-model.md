# Branching Model

flxbl projects follows   [Scaled Trunk Based Development](https://trunkbaseddevelopment.com/) with a release candidate ( an equivalent of release branch ) created for each commit to the trunk. As flxbl utilizes an artifact based deployment model,   branches are delinked from production.

## Scaled Trunk based development

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption><p>Scaled Trunk based development with automatically generated release candidates on every merge</p></figcaption></figure>

In [Scaled Trunk Based Development](https://trunkbaseddevelopment.com), developers utilize short-lived feature branches, where an individual developer work on a very small piece of functionality. Once it is tested properly, the developer would raise a Pull Request to be integrated into the trunk **("main")** branch. If the pull request validation is completed successfully and the reviewers are happy with the change, it is immediately merged into the trunk.

Each merge of a pull request into the main branch will result in creating a release candidate for the impacted domain, that is deployable to production. A release candidate is collection of artifacts and the associated version number , generated at every commit  in trunk (Release candidates are defined by[ release definition ](https://docs.flxbl.io/sfp/releasing-artifacts/release-definitions) in [sfp](https://docs.flxbl.io/sfp/)). If your team do not has need to deploy continuously  to production or has single domain, you can follow the **release branch** model as described in the [Scaled Trunk Development guide](https://trunkbaseddevelopment.com/branch-for-release/)

{% hint style="success" %}
flxbl recommends you adopt the ship/show/ask pattern for pull requests to ensure throughput in your development. You can read about ship/show/ask pattern here [https://martinfowler.com/articles/ship-show-ask.html](https://martinfowler.com/articles/ship-show-ask.html)&#x20;
{% endhint %}

## Applying Patches / Hotfix&#x20;

{% hint style="info" %}
flxbl projects with high thorough-put should use a roll-forward strategy than attempting to patch an existing release.&#x20;
{% endhint %}

As flxbl project operates on multiple independently deployable domains, applying a patch or hotfix has to be contextualised in the context of the domain. A temporary branch is created with commits of each individual artifact that constitutes the current state of production

{% embed url="https://docs.google.com/presentation/d/e/2PACX-1vQgwJ-xm3dtvXpjc7QxsowxCDz5B7bxNFIVTnVkeNiyKLTiQ-RRcV8DRh1z7n5LjdRnrdTyy55Os0Sc/pub?start=false&loop=false&delayms=3000" %}

A patch is applied to the temporary release branch and artifacts are generated, which are then deployed to production. A cherrypick of the commit applied on the temporary release branch is propagated into the trunk&#x20;

&#x20;**sfp/sfops** provide  you with options to create a just in time release branch and all the above operations with a few commands
