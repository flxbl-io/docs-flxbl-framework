# Pooling Sandboxes

The flxbl framework maximizes development efficiency by leveraging **both scratch org and sandbox pools** in a complementary strategy. While scratch orgs excel for development work with their lightweight, ephemeral nature, sandbox pools address critical enterprise needs where production-like environments with realistic data volumes and organizational complexity are essential.

## Strategic Rationale for Dual Pool Strategy

### Why Both Pool Types Matter

**Scratch Orgs**: Ideal for development activities
- Rapid provisioning and lightweight environments
- Package-based installations for clean development
- Cost-effective for individual developer workflows
- Perfect for feature development and unit testing

**Sandbox Pools**: Essential for enterprise validation
- Production-like data structures and organizational complexity
- Limited test data capacity (200MB for developer sandboxes, upgrade licenses available)
- Acceptance testing in environments that mirror production metadata structure
- Compliance and security testing with production-equivalent configurations

### The Review Environment Sweet Spot

Sandbox pools particularly shine in **review environments** where changes require validation against production-like conditions. This is where the investment in longer provisioning times and resource costs pays dividends through comprehensive validation.

## Sandbox Pool Focus: Review Environments and Acceptance Testing

### Review Environment Architecture

Review environments serve as the critical validation gateway in the flxbl framework, providing dedicated environments for each pull request or work item. These environments enable:

**Deployment Validation**
- Verify changes deploy successfully against production-like metadata
- Catch deployment conflicts early in the development process
- Validate complex dependency chains in realistic environments

**Comprehensive Testing**
- Execute impacted Apex test classes against production metadata structure
- Run end-to-end testing scenarios with production-equivalent workflows
- Functional testing with limited but representative test data (200MB capacity)

**Stakeholder Acceptance**
- Provide shared environments for business stakeholder review
- Enable user acceptance testing in production-like conditions
- Facilitate feedback collection before changes merge to trunk

### Test Data Strategy

**Snapshot Environment Preparation**
Load curated test data into snapshot sandboxes that serve as pool sources, working within the 200MB developer sandbox limit. This ensures every pooled environment contains:
- Essential test data for functional validation (upgrade licenses may be needed for larger datasets)
- Complete business process workflows for end-to-end testing
- Realistic user permission and sharing model scenarios
- Production-equivalent integration endpoints and external system connections

**Data Refresh Cycles**
Maintain snapshot environments with current, relevant test data through:
- Regular refresh cycles aligned with production data updates
- Anonymized production data sets for compliance requirements
- Synthetic data generation for specific testing scenarios

## Pool Architecture and Lifecycle

### Snapshot-Based Foundation

All sandbox pools originate from carefully maintained snapshot sandboxes:

**Main Branch Snapshot**
- Primary source template reflecting latest validated trunk state
- Contains current package installations and configurations
- Serves standard development and review pool creation

**Production Snapshot**
- Dedicated template for hotfix validation workflows
- Mirrors exact production state for emergency change testing
- Enables rapid critical fix validation against live configurations

### Lifecycle Management Constraints

**Salesforce Platform Limitations**
- 24-hour natural expiration limit for sandbox environments
- Platform creates approximately 4 sandboxes concurrently regardless of request volume
- 24-hour minimum deletion window before recreation is possible

**Replenishment Strategy**
- Scheduled full pool recreation during low-usage periods (4-6 hour window)
- Hyperforce infrastructure recommended for enhanced performance and reliability

### Review Environment Lifecycle

**Environment Allocation**
1. Pull request triggers review environment fetch from appropriate pool
2. Environment assigned to specific issue/work item for dedicated use
3. 24-hour validity period with optional lease extensions

**Usage Patterns**
- Initial deployment and validation testing
- Automated test execution and manual acceptance testing
- Stakeholder review and feedback collection
- Final validation before merge approval

**Resource Management**
- Lease-based allocation prevents resource contention
- Automatic expiration ensures pool turnover
- Status tracking enables efficient reuse within issue lifecycle

## Operational Excellence

### Pool Sizing and Performance

**Capacity Planning**
- Monitor allocation patterns to identify peak demand periods
- Maintain minimal buffer capacity due to creation time constraints
- Adjust pool sizes based on team velocity and acceptance testing requirements

**Performance Optimization**
- Hyperforce infrastructure reduces creation and operation times
- Package domain filtering limits installation overhead in snapshot environments
- Strategic snapshot refresh scheduling minimizes pool recreation downtime

### Monitoring and Observability

**Critical Metrics**
- Pool depth and allocation success rates
- Environment creation times and failure patterns
- Acceptance testing cycle times and resource utilization
- Snapshot environment health and data currency within 200MB constraints

**Integration Points**
- CI/CD pipeline integration for automated environment allocation
- Issue tracking system integration for environment assignment
- Observability platform integration (DataDog, New Relic) for operational insights

### Risk Management

**Planned Downtime**
Pool recreation requires 4-6 hours of planned downtime due to:
- Sequential sandbox creation constraints
- Platform concurrency limitations
- Snapshot environment refresh requirements

**Emergency Procedures**
- Manual pool deletion pipelines for emergency recreation
- Runbook documentation for disaster recovery scenarios
- Cross-team communication protocols for planned maintenance


## Implementation Integration

The sandbox pooling strategy integrates seamlessly with flxbl framework components:

- **sfp review environment commands** for automated lifecycle management
- **sfops GitHub Actions workflows** for CI/CD pipeline integration
- **Development workflows** that leverage both scratch org and sandbox pools for optimal efficiency

This dual-pool approach ensures teams have the right environment type for each development activity while maximizing the strategic value of sandbox pools for critical review and acceptance testing workflows.