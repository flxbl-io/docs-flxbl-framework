# Principles

## 1.  Changes should be traceable in your version control

Any aspect of code or config that gets deployed (not applied directly) into a Salesforce org should be traceable to an **artifact** built from a point in time from a source code repository. The artifact should have a name, version number and an associated lifecycle.  Any changes that are directly applied should be recorded in runbooks stored in version control.&#x20;

## 2. Developer Productivity is Paramount

**flxbl** projects will strive to improve developer productivity by ensuring ease of usability and use easily configurable  automation across the projects.

## 3. Loosely Coupled Architecture

\#**flxbl** practitioners will design their modules in such a way that it reduce the inter-dependencies, facilitate integration, deployment and test. Modular development offer numerous advantages over monolithic architectures, including easier maintenance, better scalability, and more straightforward updates. In a modular system, changes to one part of the application will not impact others, reducing the risk of widespread system failures.

Understanding fundamental software engineering principles (loose coupling, abstraction, etc.) and proceeding iteratively by extraction and refactoring transforms a monolithic Salesforce implementation into a modular system.&#x20;

## 4. Automate Manual Deployment Steps

Recurring manual steps hampers the path to production by making it unaffordable to deploy at will.  Manual deployments lead to environments discrepancies. Leading to cascading issues, and any Salesforce implementation relying on manual deployments ultimately grinds to a halt.

Organizations must invest in tools and processes to enable automated deployments, with version control systems at their core.

## 5. Continuous Emphasis on Reducing Technical Debt

Technical debt can arise in various forms. It can be a functional element that can be realized differently or short cuts that have taken to release a feature. #flxbl emphasizes technical debts will always be prioritized along with other development and due care will taken to mitigate it.

## 6. Traceability, Repeatability and Accountability

Artifacts, run books, and release notes are fundamental tools to enable complete visibility to changes in the environments. Ensure the team adheres to a  governance process and configuration changes outside the deployment pipeline are documented consistently to refer to historically as needed.
