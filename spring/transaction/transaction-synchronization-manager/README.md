## TransactionSynchronizationManager

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/transaction/support/TransactionSynchronizationManager.html
- https://stackoverflow.com/questions/32524327/spring-transactionsynchronizationmanager-isactualtransactionactive-and-getcurr
- https://translate.google.com/translate?hl=ko&sl=zh-CN&tl=en&u=https%3A%2F%2Fcodeday.me%2Fbug%2F20190405%2F884693.html
```

## Status of transaction

When Transaction get started, TransactionManager prepares transaction as following:

```java
/**
 * Initialize transaction synchronization as appropriate.
 */
protected void prepareSynchronization(DefaultTransactionStatus status, TransactionDefinition definition) {
  if (status.isNewSynchronization()) {
    // .. omitted ..
    TransactionSynchronizationManager.setActualTransactionActive(status.hasTransaction());
    TransactionSynchronizationManager.initSynchronization();
  }
}
```

#### \#isActualTransactionActive

Resume and Pause will affect this flags:

```java
protected final SuspendedResourcesHolder suspend(Object transaction) {
  if (TransactionSynchronizationManager.isSynchronizationActive()) {
    List<TransactionSynchronization> suspendedSynchronizations = doSuspendSynchronization();
    suspendedResources = doSuspend(transaction);

    try {
      String name = TransactionSynchronizationManager.getCurrentTransactionName();
      boolean readOnly = TransactionSynchronizationManager.isCurrentTransactionReadOnly();
      Integer isolationLevel = TransactionSynchronizationManager.getCurrentTransactionIsolationLevel();
      boolean wasActive = TransactionSynchronizationManager.isActualTransactionActive();

      TransactionSynchronizationManager.setCurrentTransactionName(null);
      TransactionSynchronizationManager.setCurrentTransactionReadOnly(false);
      TransactionSynchronizationManager.setCurrentTransactionIsolationLevel(null);
      // actualTransactionActive is paused
      TransactionSynchronizationManager.setActualTransactionActive(false);

      return new SuspendedResourcesHolder(
        suspendedResources, suspendedSynchronizations, name, readOnly, isolationLevel, wasActive);
    }
    catch (Error err) {
      doResumeSynchronization(suspendedSynchronizations);
      throw err;
    }
  }
}
```

```java
protected final void resume(Object transaction, SuspendedResourcesHolder resourcesHolder) {
  if (resourcesHolder != null) {
    Object suspendedResources = resourcesHolder.suspendedResources;
    List<TransactionSynchronization> suspendedSynchronizations = resourcesHolder.suspendedSynchronizations;

    if (suspendedSynchronizations != null) {
      // resumed
      TransactionSynchronizationManager.setActualTransactionActive(resourcesHolder.wasActive);
      TransactionSynchronizationManager.setCurrentTransactionIsolationLevel(resourcesHolder.isolationLevel);
      TransactionSynchronizationManager.setCurrentTransactionReadOnly(resourcesHolder.readOnly);
      TransactionSynchronizationManager.setCurrentTransactionName(resourcesHolder.name);

      doResumeSynchronization(suspendedSynchronizations);
    }
  }
}
```

#### \#isSynchronizationActive

Changed If transaction is completed due to completion and/or exception

|                 | isActualTransactionActive | isSynchronizationActive |
| --------------- | ------------------------- | ----------------------- |
| InitTransaction | true                      | true                    |
| Pause           | **false**                 | true                    |
| Resume          | true                      | true                    |
| Finished        | false                     | false                   |

> Propagation.REQUIRES_NEW create a new transaction, and suspend the current transaction if one exists.

## Action of Transaction

```java
@Transactional
public void updateUser(Long userId) {
  // .. CUD

  if (TransactionSynchronizationManager.isActualTransactionActive()) {
    TransactionSynchronizationManager.registerSynchronization(new TransactionSynchronizationAdapter() {
      @Override
      public void afterCommit() {
        // send email/sms
      }

      @Override
      public void afterCompletion(int status) {
        // retain histories
      }
    });
  }
}
```

> This manual handling could be improved using [application-event pattern](../../application-event)

