---
icon: ring-diamond
---

# flxbl-execution-framework

| Availability | ✅          | ❌ |
| ------------ | ---------- | - |
| From         | October 25 | ​ |



A powerful, flexible framework for managing and orchestrating business logic execution in Salesforce. This framework provides a unified approach to handling synchronous and asynchronous operations, with built-in support for error handling, retry logic, and execution tracking.\


### Why Execution Unit Framework?

#### The Problems We're Solving

**1. Scattered Business Logic**

In typical Salesforce implementations, business logic gets scattered across triggers, batch jobs, queueables, and various classes. This leads to:

* Duplicate code across different execution contexts
* Difficulty in maintaining and testing business logic
* Inconsistent error handling and logging

**2. Complex Asynchronous Patterns**

Managing asynchronous operations in Salesforce is challenging:

* Platform limits (5 queueable jobs from non-async context)
* No built-in retry mechanisms
* Difficult to track execution status and results
* Complex chaining of async operations

**3. Lack of Flexibility**

Traditional approaches hardcode execution patterns:

* A trigger always runs synchronously
* A batch job always processes records in batches
* Changing execution mode requires code refactoring

**4. Poor Visibility and Control**

Without a framework:

* No central place to monitor execution status
* Difficult to pause, resume, or rerun operations
* Limited ability to track execution history and metrics

#### Our Solution

The Execution Unit Framework addresses these challenges by providing:

* **Unified execution model** for all business logic
* **Declarative configuration** of execution patterns
* **Automatic handling** of platform limits and retries
* **Complete visibility** into execution status and history
* **Flexible execution modes** (sync/async) without code changes

### Key Features

* ✅ **Unified Interface**: Single `ExecutionHandler` interface for all business logic
* ✅ **Flexible Execution**: Switch between synchronous and asynchronous execution without code changes
* ✅ **Built-in Persistence**: Automatic tracking of execution status, results, and metrics
* ✅ **Automatic Unique Naming**: UUID-based names prevent conflicts without developer intervention
* ✅ **Correlation Tracking**: Automatic correlation IDs to group related execution units
* ✅ **Parent-Child Relationships**: Track execution hierarchies with parent-child relationships
* ✅ **Error Handling**: Comprehensive error capture and retry mechanisms
* ✅ **Chaining Support**: Easy orchestration of complex multi-step processes
* ✅ **Platform Limit Management**: Automatic handling of governor limits
* ✅ **Security**: Built-in permission sets for admin and read-only access
* ✅ **Monitoring**: Custom object for tracking and monitoring executions

### Getting Started

#### Installation

1. Deploy the framework to your Salesforce org:

```bash
sf project deploy start --source-dir apps/execution-units
```

2. Assign permissions:

```bash
# For administrators
sf org assign permset --name ExecutionUnitAdmin

# For read-only users
sf org assign permset --name ExecutionUnitReader
```

#### Your First Execution Handler

Let's start with a simple example - a handler that welcomes new accounts:

```apex
public class WelcomeAccountHandler implements ExecutionHandler {
    
    public ExecutionResult execute(ExecutionContext context) {
        // Get account ID from context
        String accountId = (String) context.get('accountId');
        
        if (String.isBlank(accountId)) {
            return ExecutionResult.failure('Account ID is required');
        }
        
        try {
            // Fetch the account
            Account acc = [SELECT Id, Name, Type FROM Account WHERE Id = :accountId];
            
            // Create a welcome task
            Task welcomeTask = new Task(
                WhatId = acc.Id,
                Subject = 'Welcome ' + acc.Name + '!',
                Description = 'Please reach out to our new customer',
                ActivityDate = Date.today().addDays(1),
                Status = 'Not Started',
                Priority = 'High'
            );
            insert welcomeTask;
            
            // Return success with task ID
            Map<String, Object> result = new Map<String, Object>{
                'taskId' => welcomeTask.Id,
                'accountName' => acc.Name
            };
            
            return ExecutionResult.success(result);
            
        } catch (Exception e) {
            return ExecutionResult.failure('Failed to create welcome task: ' + e.getMessage());
        }
    }
}
```

#### Creating an Execution Unit

Now, let's create an execution unit that uses our handler:

```apex
// Option 1: Using the builder pattern (names are auto-generated with UUID)
ExecutionUnit.Configuration config = ExecutionUnit.configure()
    .withHandlerClass('WelcomeAccountHandler')  // Name will be auto-generated
    .withMode(ExecutionMode.SYNCHRONOUS)
    .withPriority(1)
    .withDescription('Creates welcome task for new accounts');

ExecutionUnit__c unit = config.build();
// unit.Name will be something like: "WelcomeAccountHandler_a1b2c3d4-e5f6-7890-abcd-ef1234567890"

// Option 2: Provide a custom name prefix (still gets UUID appended)
ExecutionUnit.Configuration config2 = ExecutionUnit.configure()
    .withName('Welcome New Account')  // Will become "Welcome New Account_[UUID]"
    .withHandlerClass('WelcomeAccountHandler')
    .withMode(ExecutionMode.SYNCHRONOUS)
    .withPriority(1)
    .withDescription('Creates welcome task for new accounts');

ExecutionUnit__c unit2 = config2.build();
```

#### Automatic Naming Convention

The framework automatically generates unique names for all Execution Units using UUID suffixes:

* **Without custom name**: Uses handler class name + UUID
  * Example: `AccountSyncHandler_a1b2c3d4-e5f6-7890-abcd-ef1234567890`
* **With custom name**: Uses your provided name + UUID
  * Example: `Daily Report_a1b2c3d4-e5f6-7890-abcd-ef1234567890`

This ensures:

* ✅ No naming conflicts when creating multiple units
* ✅ Developers don't need to worry about uniqueness
* ✅ Names remain readable with meaningful prefixes
* ✅ UUID generation has minimal CPU impact using `Crypto.GenerateAESKey(128)`

#### Correlation Tracking

The framework automatically tracks related execution units using correlation IDs:

**Automatic Correlation ID Generation**

Every execution unit gets a correlation ID automatically:

```apex
// Create a standalone unit - gets a new correlation ID
ExecutionUnit.Configuration config = ExecutionUnit.configure()
    .withName('Process Order')
    .withHandlerClass('OrderProcessor')
    .withMode(ExecutionMode.ASYNCHRONOUS);

ExecutionUnit__c unit = config.build();
// unit.CorrelationId__c = auto-generated UUID
```

**Parent-Child Relationships**

Child units automatically inherit their parent's correlation ID:

```apex
// Create parent unit
ExecutionUnit__c parentUnit = ExecutionUnit.configure()
    .withName('Order Fulfillment')
    .withHandlerClass('OrderFulfillmentHandler')
    .withMode(ExecutionMode.ASYNCHRONOUS)
    .build();

// Create child unit - inherits parent's correlation ID
ExecutionUnit__c childUnit = ExecutionUnit.configure()
    .withName('Inventory Check')
    .withHandlerClass('InventoryCheckHandler')
    .withParentExecutionUnit(parentUnit.Id)  // Links to parent
    .withMode(ExecutionMode.SYNCHRONOUS)
    .build();

System.assertEquals(parentUnit.CorrelationId__c, childUnit.CorrelationId__c);
```

**Explicit Correlation ID**

You can provide your own correlation ID to group related units:

```apex
String businessTransactionId = 'TXN-' + DateTime.now().getTime();

// Create multiple related units with same correlation ID
ExecutionUnit__c validationUnit = ExecutionUnit.configure()
    .withName('Validate Payment')
    .withHandlerClass('PaymentValidator')
    .withCorrelationId(businessTransactionId)
    .build();

ExecutionUnit__c processingUnit = ExecutionUnit.configure()
    .withName('Process Payment')
    .withHandlerClass('PaymentProcessor')
    .withCorrelationId(businessTransactionId)
    .build();

// Find all related units
List<ExecutionUnit__c> relatedUnits = 
    ExecutionUnit.findByCorrelationId(businessTransactionId);
// Returns both units
```

**Querying Related Units**

```apex
// Find all units in the same correlation group
List<ExecutionUnit__c> relatedUnits = 
    ExecutionUnit.findByCorrelationId(someUnit.CorrelationId__c);

// Find direct children of a parent unit
List<ExecutionUnit__c> childUnits = 
    ExecutionUnit.getChildUnits(parentUnit.Id);

// Get all related units (alias for findByCorrelationId)
List<ExecutionUnit__c> allRelated = 
    ExecutionUnit.getRelatedUnits(correlationId);
```

#### Basic Execution

**From a Trigger**

```apex
trigger AccountTrigger on Account (after insert) {
    for (Account acc : Trigger.new) {
        // Prepare context with unique correlation per account
        ExecutionContext context = new ExecutionContext();
        context.put('accountId', acc.Id);
        
        // Create unit with unique reference ID to prevent duplicates
        ExecutionUnit__c welcomeUnit = ExecutionUnit.configure()
            .withName('Welcome Account')
            .withHandlerClass('WelcomeAccountHandler')
            .withReferenceId('WELCOME_' + acc.Id)  // Prevents duplicate processing
            .withCorrelationId('ACCOUNT_' + acc.Id)  // Groups all account-related units
            .withMode(ExecutionMode.SYNCHRONOUS)
            .build();
        
        ExecutionResult result = ExecutionUnit.execute(welcomeUnit, context);
        
        if (!result.isSuccess()) {
            System.debug('Failed to welcome account: ' + result.getErrorMessage());
        }
    }
}
```

**From an API or Lightning Component**

```apex
@AuraEnabled
public static Map<String, Object> processAccount(String accountId) {
    try {
        // Prepare context
        ExecutionContext context = new ExecutionContext();
        context.put('accountId', accountId);
        
        // Execute and return result
        ExecutionResult result = ExecutionUnit.execute('Welcome New Account', context);
        
        return new Map<String, Object>{
            'success' => result.isSuccess(),
            'data' => result.getData(),
            'error' => result.getErrorMessage()
        };
    } catch (Exception e) {
        throw new AuraHandledException(e.getMessage());
    }
}
```

### Intermediate Patterns

#### Asynchronous Execution

Sometimes you want to offload work to run asynchronously to avoid governor limits:

```apex
public class DataEnrichmentHandler implements ExecutionHandler {
    
    public ExecutionResult execute(ExecutionContext context) {
        List<String> accountIds = (List<String>) context.get('accountIds');
        
        // Enrich accounts with external data
        Http http = new Http();
        List<Account> accountsToUpdate = new List<Account>();
        
        for (String accId : accountIds) {
            // Make callout to external service
            HttpRequest req = new HttpRequest();
            req.setEndpoint('https://api.example.com/enrich/' + accId);
            req.setMethod('GET');
            
            HttpResponse res = http.send(req);
            if (res.getStatusCode() == 200) {
                Map<String, Object> enrichedData = (Map<String, Object>) 
                    JSON.deserializeUntyped(res.getBody());
                
                Account acc = new Account(
                    Id = accId,
                    Industry = (String) enrichedData.get('industry'),
                    NumberOfEmployees = (Integer) enrichedData.get('employees')
                );
                accountsToUpdate.add(acc);
            }
        }
        
        if (!accountsToUpdate.isEmpty()) {
            update accountsToUpdate;
        }
        
        return ExecutionResult.success(
            new Map<String, Object>{
                'enrichedCount' => accountsToUpdate.size()
            }
        );
    }
}
```

Configure for async execution:

```apex
ExecutionUnit.Configuration config = ExecutionUnit.configure()
    .withName('Enrich Account Data')  // UUID will be appended automatically
    .withHandlerClass('DataEnrichmentHandler')
    .withMode(ExecutionMode.ASYNCHRONOUS)  // Will run in queueable
    .withPriority(5)
    .withDescription('Enriches accounts with external data via callout');

ExecutionUnit__c unit = config.build();
```

Execute from a batch or screen flow:

```apex
// This won't count against your synchronous callout limits
ExecutionContext context = new ExecutionContext();
context.put('accountIds', accountIds);

// Executes asynchronously - returns immediately
ExecutionUnit.executeAsync('Enrich Account Data', context);
```

#### Conditional Execution with Scheduling

You can schedule execution units to run at specific times or under certain conditions:

```apex
public class DailyReportHandler implements ExecutionHandler {
    
    public ExecutionResult execute(ExecutionContext context) {
        Date reportDate = (Date) context.get('reportDate');
        if (reportDate == null) {
            reportDate = Date.today().addDays(-1);
        }
        
        // Generate report logic
        List<AggregateResult> results = [
            SELECT COUNT(Id) total, SUM(Amount) revenue, OwnerId
            FROM Opportunity
            WHERE CloseDate = :reportDate AND IsWon = true
            GROUP BY OwnerId
        ];
        
        // Create report records or send emails
        // ...
        
        return ExecutionResult.success(
            new Map<String, Object>{
                'reportDate' => reportDate,
                'recordCount' => results.size()
            }
        );
    }
}
```

Schedule it to skip weekends:

```apex
ExecutionUnit.Configuration config = ExecutionUnit.configure()
    .withName('Daily Sales Report')
    .withHandlerClass('DailyReportHandler')
    .withMode(ExecutionMode.ASYNCHRONOUS)
    .withPriority(10)
    .withSkipUntil(DateTime.now().addDays(1))  // Start tomorrow
    .build();

// The execution framework will check the SkipUntil__c field
```

#### Error Handling and Retry Logic

Build robust handlers with retry capabilities:

```apex
public class ResilientIntegrationHandler extends AbstractExecutionHandler {
    
    protected override ExecutionResult process(ExecutionContext context) {
        String recordId = (String) context.get('recordId');
        Integer attemptNumber = (Integer) context.get('attemptNumber');
        if (attemptNumber == null) {
            attemptNumber = 1;
        }
        
        try {
            // Attempt integration
            performIntegration(recordId);
            return ExecutionResult.success();
            
        } catch (CalloutException e) {
            // Transient error - retry
            if (attemptNumber < 3) {
                // Schedule retry
                ExecutionContext retryContext = new ExecutionContext();
                retryContext.putAll(context);
                retryContext.put('attemptNumber', attemptNumber + 1);
                
                // Create retry execution unit
                ExecutionUnit.Configuration retryConfig = ExecutionUnit.configure()
                    .withName('Integration Retry - Attempt ' + (attemptNumber + 1))  // UUID ensures uniqueness
                    .withHandlerClass('ResilientIntegrationHandler')
                    .withMode(ExecutionMode.ASYNCHRONOUS)
                    .withPayload(JSON.serialize(retryContext.toMap()))
                    .withSkipUntil(DateTime.now().addMinutes(5 * attemptNumber))
                    .build();
                
                return ExecutionResult.failure('Retrying after error: ' + e.getMessage());
            }
            
            // Max retries exceeded
            return ExecutionResult.failure('Integration failed after ' + attemptNumber + ' attempts');
        }
    }
    
    private void performIntegration(String recordId) {
        // Integration logic here
    }
}
```

### Advanced Scenarios

#### Chaining Execution Units

One of the most powerful features is the ability to chain execution units for complex workflows with automatic correlation tracking:

```apex
public class OrderFulfillmentChain {
    
    public static void initiateFullfilment(String orderId) {
        ExecutionContext context = new ExecutionContext();
        context.put('orderId', orderId);
        
        // Generate a correlation ID for the entire order process
        String orderCorrelationId = 'ORDER_FLOW_' + orderId;
        
        // Step 1: Validate Order (Parent Unit)
        ExecutionUnit__c validateUnit = ExecutionUnit.configure()
            .withName('Validate Order')  // UUID ensures uniqueness
            .withHandlerClass('OrderValidationHandler')
            .withMode(ExecutionMode.SYNCHRONOUS)
            .withPayload(JSON.serialize(context.toMap()))
            .withReferenceId('ORDER_' + orderId + '_VALIDATE')  // Prevents duplicates
            .withCorrelationId(orderCorrelationId)  // Groups all order-related units
            .build();
        
        ExecutionResult validationResult = ExecutionUnit.execute(validateUnit, context);
        
        if (validationResult.isSuccess()) {
            // Step 2: Reserve Inventory (Child of validation)
            ExecutionUnit__c reserveUnit = ExecutionUnit.configure()
                .withName('Reserve Inventory')  // UUID ensures uniqueness
                .withHandlerClass('InventoryReservationHandler')
                .withMode(ExecutionMode.ASYNCHRONOUS)
                .withPayload(JSON.serialize(validationResult.getData()))
                .withReferenceId('ORDER_' + orderId + '_RESERVE')  // Prevents duplicates
                .withParentExecutionUnit(validateUnit.Id)  // Links to parent, inherits correlation ID
                .withPriority(1)
                .build();
            
            ExecutionUnit.executeAsync(reserveUnit);
        }
    }
}

// Inventory handler triggers next step
public class InventoryReservationHandler implements ExecutionHandler {
    
    public ExecutionResult execute(ExecutionContext context) {
        String orderId = (String) context.get('orderId');
        
        // Reserve inventory logic
        Boolean inventoryReserved = reserveInventory(orderId);
        
        if (inventoryReserved) {
            // Trigger next step: Payment Processing
            ExecutionContext paymentContext = new ExecutionContext();
            paymentContext.putAll(context);
            paymentContext.put('inventoryReserved', true);
            
            // Get parent's correlation ID to maintain workflow tracking
            ExecutionUnit__c currentUnit = [SELECT Id, CorrelationId__c 
                                           FROM ExecutionUnit__c 
                                           WHERE ReferenceId__c = :('ORDER_' + orderId + '_RESERVE')
                                           LIMIT 1];
            
            ExecutionUnit__c paymentUnit = ExecutionUnit.configure()
                .withName('Process Payment')  // UUID ensures uniqueness
                .withHandlerClass('PaymentProcessingHandler')
                .withMode(ExecutionMode.ASYNCHRONOUS)
                .withPayload(JSON.serialize(paymentContext.toMap()))
                .withReferenceId('ORDER_' + orderId + '_PAYMENT')  // Prevents duplicates
                .withParentExecutionUnit(currentUnit.Id)  // Links to parent, inherits correlation ID
                .withPriority(1)
                .build();
            
            ExecutionUnit.executeAsync(paymentUnit);
            
            return ExecutionResult.success(
                new Map<String, Object>{'nextStep' => 'payment'}
            );
        }
        
        return ExecutionResult.failure('Insufficient inventory');
    }
    
    private Boolean reserveInventory(String orderId) {
        // Implementation
        return true;
    }
}
```

#### Batch Processing with Execution Units

Process large data volumes efficiently:

```apex
public class AccountMaintenanceBatch {
    
    public static void scheduleMaintenace() {
        // Generate a correlation ID for this entire batch operation
        String batchCorrelationId = 'BATCH_MAINT_' + DateTime.now().getTime();
        
        // Create execution units for batch processing
        List<ExecutionUnit__c> batchUnits = new List<ExecutionUnit__c>();
        
        // Query accounts that need maintenance
        List<Account> accounts = [
            SELECT Id FROM Account 
            WHERE LastActivityDate < LAST_N_DAYS:90
            LIMIT 10000
        ];
        
        // Create chunks of 200 accounts each
        List<List<String>> chunks = new List<List<String>>();
        List<String> currentChunk = new List<String>();
        
        for (Account acc : accounts) {
            currentChunk.add(acc.Id);
            if (currentChunk.size() == 200) {
                chunks.add(currentChunk);
                currentChunk = new List<String>();
            }
        }
        if (!currentChunk.isEmpty()) {
            chunks.add(currentChunk);
        }
        
        // Create parent unit for the batch
        ExecutionUnit__c parentBatchUnit = ExecutionUnit.configure()
            .withName('Account Maintenance Batch')
            .withHandlerClass('BatchCoordinatorHandler')
            .withCorrelationId(batchCorrelationId)
            .withReferenceId('BATCH_PARENT_' + DateTime.now().getTime())
            .withMode(ExecutionMode.SYNCHRONOUS)
            .build();
        
        // Create execution unit for each chunk
        for (Integer i = 0; i < chunks.size(); i++) {
            ExecutionContext context = new ExecutionContext();
            context.put('accountIds', chunks[i]);
            context.put('chunkNumber', i + 1);
            context.put('totalChunks', chunks.size());
            
            ExecutionUnit__c unit = ExecutionUnit.configure()
                .withName('Account Maintenance - Chunk ' + (i + 1))  // UUID ensures uniqueness
                .withHandlerClass('AccountMaintenanceHandler')
                .withMode(ExecutionMode.ASYNCHRONOUS)
                .withPayload(JSON.serialize(context.toMap()))
                .withReferenceId('BATCH_CHUNK_' + batchCorrelationId + '_' + i)  // Unique reference
                .withParentExecutionUnit(parentBatchUnit.Id)  // Links to parent, inherits correlation ID
                .withPriority(100 - i)  // Process in order
                .build();
            
            batchUnits.add(unit);
        }
        
        // Insert all units - they'll be picked up by the batch processor
        insert batchUnits;
        
        // Start batch processor
        ExecutionBatchProcessor batch = new ExecutionBatchProcessor();
        Database.executeBatch(batch, 10);
    }
}
```

#### Platform Event Driven Execution

Trigger execution units from platform events:

```apex
trigger OrderEventTrigger on Order_Event__e (after insert) {
    List<ExecutionUnit__c> unitsToCreate = new List<ExecutionUnit__c>();
    
    for (Order_Event__e event : Trigger.new) {
        // Generate or use existing correlation ID for the order
        String orderCorrelationId = 'ORDER_' + event.Order_Id__c;
        
        ExecutionContext context = new ExecutionContext();
        context.put('orderId', event.Order_Id__c);
        context.put('eventType', event.Event_Type__c);
        context.put('eventData', event.Payload__c);
        
        String handlerClass;
        Integer priority;
        
        // Determine handler based on event type
        switch on event.Event_Type__c {
            when 'ORDER_PLACED' {
                handlerClass = 'NewOrderHandler';
                priority = 1;
            }
            when 'ORDER_CANCELLED' {
                handlerClass = 'OrderCancellationHandler';
                priority = 0;  // Urgent
            }
            when 'ORDER_SHIPPED' {
                handlerClass = 'ShipmentNotificationHandler';
                priority = 5;
            }
            when else {
                continue;
            }
        }
        
        ExecutionUnit__c unit = ExecutionUnit.configure()
            .withName(event.Event_Type__c)  // UUID ensures uniqueness
            .withHandlerClass(handlerClass)
            .withMode(ExecutionMode.ASYNCHRONOUS)
            .withPayload(JSON.serialize(context.toMap()))
            .withReferenceId('EVENT_' + event.Event_Type__c + '_' + event.Order_Id__c + '_' + event.ReplayId)  // Unique reference
            .withCorrelationId(orderCorrelationId)  // Groups all events for this order
            .withPriority(priority)
            .build();
        
        unitsToCreate.add(unit);
    }
    
    if (!unitsToCreate.isEmpty()) {
        insert unitsToCreate;
    }
}
```

#### Complex Orchestration with State Management

Build sophisticated workflows with state management:

```apex
public class LoanApprovalOrchestrator implements ExecutionHandler {
    
    public ExecutionResult execute(ExecutionContext context) {
        String loanApplicationId = (String) context.get('loanApplicationId');
        String currentStage = (String) context.get('currentStage');
        String correlationId = (String) context.get('correlationId');
        
        // Generate correlation ID for new loan applications
        if (correlationId == null) {
            correlationId = 'LOAN_PROCESS_' + loanApplicationId;
            context.put('correlationId', correlationId);
        }
        
        if (currentStage == null) {
            currentStage = 'CREDIT_CHECK';
        }
        
        ExecutionResult stageResult;
        String nextStage;
        
        switch on currentStage {
            when 'CREDIT_CHECK' {
                stageResult = performCreditCheck(loanApplicationId);
                nextStage = stageResult.isSuccess() ? 'INCOME_VERIFICATION' : 'REJECTED';
            }
            when 'INCOME_VERIFICATION' {
                stageResult = verifyIncome(loanApplicationId);
                nextStage = stageResult.isSuccess() ? 'RISK_ASSESSMENT' : 'MANUAL_REVIEW';
            }
            when 'RISK_ASSESSMENT' {
                stageResult = assessRisk(loanApplicationId);
                nextStage = stageResult.isSuccess() ? 'APPROVAL' : 'MANUAL_REVIEW';
            }
            when 'MANUAL_REVIEW' {
                stageResult = scheduleManualReview(loanApplicationId);
                nextStage = 'PENDING_REVIEW';
            }
            when 'APPROVAL' {
                stageResult = approveLoan(loanApplicationId);
                nextStage = 'COMPLETE';
            }
            when else {
                return ExecutionResult.success(
                    new Map<String, Object>{'status' => 'Process Complete'}
                );
            }
        }
        
        // Update loan application status
        updateLoanStatus(loanApplicationId, currentStage, stageResult);
        
        // Schedule next stage if needed
        if (nextStage != 'COMPLETE' && nextStage != 'REJECTED' && nextStage != 'PENDING_REVIEW') {
            ExecutionContext nextContext = new ExecutionContext();
            nextContext.put('loanApplicationId', loanApplicationId);
            nextContext.put('currentStage', nextStage);
            nextContext.put('correlationId', correlationId);  // Pass correlation ID forward
            nextContext.put('previousStageResult', stageResult.getData());
            
            // Get current unit to establish parent-child relationship
            ExecutionUnit__c currentUnit = [SELECT Id FROM ExecutionUnit__c 
                                           WHERE ReferenceId__c = :('LOAN_' + loanApplicationId + '_' + currentStage)
                                           LIMIT 1];
            
            ExecutionUnit__c nextUnit = ExecutionUnit.configure()
                .withName('Loan Approval - ' + nextStage)  // UUID ensures uniqueness
                .withHandlerClass('LoanApprovalOrchestrator')
                .withMode(ExecutionMode.ASYNCHRONOUS)
                .withPayload(JSON.serialize(nextContext.toMap()))
                .withReferenceId('LOAN_' + loanApplicationId + '_' + nextStage)  // Unique reference
                .withCorrelationId(correlationId)  // Maintain correlation throughout workflow
                .withParentExecutionUnit(currentUnit?.Id)  // Link to current stage if exists
                .withPriority(getStagePrority(nextStage))
                .withSkipUntil(DateTime.now().addMinutes(1))  // Brief delay between stages
                .build();
            
            ExecutionUnit.executeAsync(nextUnit);
        }
        
        return ExecutionResult.success(
            new Map<String, Object>{
                'stage' => currentStage,
                'nextStage' => nextStage,
                'stageResult' => stageResult.getData()
            }
        );
    }
    
    // Stage-specific methods
    private ExecutionResult performCreditCheck(String loanId) {
        // Implementation
        return ExecutionResult.success();
    }
    
    private ExecutionResult verifyIncome(String loanId) {
        // Implementation
        return ExecutionResult.success();
    }
    
    private ExecutionResult assessRisk(String loanId) {
        // Implementation
        return ExecutionResult.success();
    }
    
    private ExecutionResult scheduleManualReview(String loanId) {
        // Implementation
        return ExecutionResult.success();
    }
    
    private ExecutionResult approveLoan(String loanId) {
        // Implementation
        return ExecutionResult.success();
    }
    
    private void updateLoanStatus(String loanId, String stage, ExecutionResult result) {
        // Update loan application record
    }
    
    private Integer getStagePrority(String stage) {
        Map<String, Integer> priorities = new Map<String, Integer>{
            'CREDIT_CHECK' => 1,
            'INCOME_VERIFICATION' => 2,
            'RISK_ASSESSMENT' => 3,
            'MANUAL_REVIEW' => 0,
            'APPROVAL' => 1
        };
        return priorities.get(stage);
    }
}
```

### What to lookout for?

#### 1. Handler Design

* Keep handlers focused on a single responsibility
* Use `AbstractExecutionHandler` for common patterns
* Always validate input in your handlers
* Return meaningful data in ExecutionResult
* **Don't worry about name uniqueness - UUID generation is automatic**

#### 2. Error Handling

* Use try-catch blocks in handlers
* Return descriptive error messages
* Implement retry logic for transient failures
* Log errors for debugging

#### 3. Performance

* Use async mode for long-running operations
* Batch large datasets into chunks
* Consider platform limits when chaining
* Monitor execution metrics

#### 4. Security

* Validate all input data
* Use with sharing in handlers when appropriate
* Assign appropriate permission sets
* Don't store sensitive data in Payload\_\_c

#### 5. Testing

* Write comprehensive tests for handlers
* Test both success and failure scenarios
* Mock external dependencies
* Test async execution paths

#### 6. Monitoring

* Use the ExecutionUnit\_\_c list views
* Set up alerts for failed executions
* Regular cleanup of old execution records
* Monitor execution patterns and performance

#### 7. Naming Best Practices

* Use descriptive names that indicate the handler's purpose
* Don't add timestamps or random suffixes for uniqueness (UUID handles this)
* Use ReferenceId\_\_c field for business-specific identifiers
* Keep names concise - the framework ensures they fit within the 255 character limit

### Examples

#### Complete Workflow with Correlation Tracking

Here's a real-world example showing how correlation IDs track a complete order processing workflow:

```apex
public class OrderWorkflowExample {
    
    public static void processNewOrder(Id orderId) {
        // Generate a unique correlation ID for this order workflow
        String workflowCorrelationId = ExecutionUnitProcessor.generateUUID();
        
        // Step 1: Create the main order processing unit
        ExecutionUnit__c mainUnit = ExecutionUnit.configure()
            .withName('Process Order')
            .withHandlerClass('OrderProcessingHandler')
            .withCorrelationId(workflowCorrelationId)
            .withReferenceId('ORDER_MAIN_' + orderId)
            .withMode(ExecutionMode.SYNCHRONOUS)
            .build();
        
        // Step 2: Create parallel child units for different checks
        List<ExecutionUnit__c> parallelUnits = new List<ExecutionUnit__c>();
        
        // Credit check
        parallelUnits.add(ExecutionUnit.configure()
            .withName('Credit Check')
            .withHandlerClass('CreditCheckHandler')
            .withParentExecutionUnit(mainUnit.Id)  // Inherits correlation ID
            .withReferenceId('ORDER_CREDIT_' + orderId)
            .withMode(ExecutionMode.ASYNCHRONOUS)
            .withPriority(10)
            .build());
        
        // Inventory check
        parallelUnits.add(ExecutionUnit.configure()
            .withName('Inventory Check')
            .withHandlerClass('InventoryCheckHandler')
            .withParentExecutionUnit(mainUnit.Id)  // Inherits correlation ID
            .withReferenceId('ORDER_INVENTORY_' + orderId)
            .withMode(ExecutionMode.ASYNCHRONOUS)
            .withPriority(10)
            .build());
        
        // Execute all parallel units
        Map<Id, ExecutionResult> results = ExecutionUnit.executeAll(parallelUnits, new ExecutionContext());
        
        // Later, find all units related to this order
        List<ExecutionUnit__c> allOrderUnits = 
            ExecutionUnit.findByCorrelationId(workflowCorrelationId);
        
        System.debug('Found ' + allOrderUnits.size() + ' units for this order workflow');
        
        // Check status of all units
        for (ExecutionUnit__c unit : allOrderUnits) {
            System.debug(unit.Name + ': ' + unit.LastExecutionStatus__c);
        }
    }
    
    // Monitor workflow progress
    public static void monitorWorkflow(String correlationId) {
        List<ExecutionUnit__c> units = ExecutionUnit.findByCorrelationId(correlationId);
        
        Integer totalUnits = units.size();
        Integer completedUnits = 0;
        Integer failedUnits = 0;
        Integer pendingUnits = 0;
        
        for (ExecutionUnit__c unit : units) {
            if (unit.LastExecutionStatus__c == 'SUCCESS') {
                completedUnits++;
            } else if (unit.LastExecutionStatus__c == 'FAILURE') {
                failedUnits++;
                System.debug('Failed unit: ' + unit.Name + ' - ' + unit.LastErrorMessage__c);
            } else {
                pendingUnits++;
            }
        }
        
        System.debug('Workflow Status for ' + correlationId + ':');
        System.debug('  Total: ' + totalUnits);
        System.debug('  Completed: ' + completedUnits);
        System.debug('  Failed: ' + failedUnits);
        System.debug('  Pending: ' + pendingUnits);
        System.debug('  Progress: ' + (completedUnits * 100 / totalUnits) + '%');
    }
}
```

#### Example Handlers

The framework includes several example handlers in the `apps/examples` directory:

* **AccountSyncHandler** - Synchronizes account data
* **DataCleanupHandler** - Cleans up old records
* **EmailNotificationHandler** - Sends email notifications
* **AsyncExecutionHandler** - Demonstrates async patterns

### Troubleshooting

#### Common Issues

**Execution not starting:**

* Check if unit is OnHold\_\_c = true
* Verify SkipUntil\_\_c date hasn't passed
* Ensure handler class exists and is global/public

**Handler not found:**

* Verify class name is correct
* Check if class implements ExecutionHandler
* Ensure class is accessible (global/public)

**Async execution limits:**

* Monitor queueable job limits
* Use batch processing for large volumes
* Implement proper chaining delays

**Duplicate name errors (pre-UUID version):**

* This should not occur in the current version
* All names are automatically made unique with UUID suffixes
* If upgrading from older version, consider data migration

**Finding specific execution units:**

* Use `Name LIKE 'YourPrefix%'` in SOQL queries
* Use `HandlerClassName__c` field for exact handler matching
* Use `ReferenceId__c` for business-specific lookups
* Use `CorrelationId__c` to find all related units in a workflow
* Use `ParentExecutionUnit__c` to find direct child units
