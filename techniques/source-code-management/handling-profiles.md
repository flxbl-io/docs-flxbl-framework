# Handling Profiles

We would recommend profiles to be version controlled, but the approach should ensure permissions configuration provides the level of flexibility required for the organization.

As mentioned in the [project structure](project-structure.md), profiles need to be placed in a source package at higher order of dependency, as profiles typically have dependency on almost all the components in your org. &#x20;

flxbl's tooling provides support for version controlling profiles. Please read the following article to gain a quick intro how the tools work.

Depending on your permissions strategy, one of the following approaches may be advisable:

* **Admin-managed profiles**. Here profiles are considered under the responsibility of an admin who can make changes without impacting functionality or having these changes overwritten. With this approach profiles are treated as [org-specific metadata,](dealing-with-org-specific-metadata.md) and settings like IP range restrictions can be added manually in production. This would usually be preferable in a multi-org deployment or an environment where admins should retain flexibility over all aspects of permissions in the org.
* **Source-managed profiles**. Under this approach profiles are treated as core to the application and changes driven from a single [source package](https://app.gitbook.com/s/YLI5Ts7pWhWQV9UaBn3H/concepts/supported-package-types/source-packages) definition. It's advisable here to use profiles to control only the minimal settings which cannot be provided through permission sets (e.g. page layout assignments, record type & app defaults and login restrictions) so admins have flexibility to control as much as possible by extending access using permission set combinations. This might be preferable in a single-org deployment where centralized control over permissions is desirable and settings like login IP ranges are static do not need to differ by org.

While using a scratch org based development, there is no specific need to use **sfp** command such as retrieve as normal sf push/pull would be suffice. sfp automatically does a reconcile against the target org when the profile is deployed.
