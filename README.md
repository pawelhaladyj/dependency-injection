# Dependency injection

## Motivation

This motivation is taken from Google Guice Wiki [https://github.com/google/guice/wiki/Motivation](https://github.com/google/guice/wiki/Motivation)

Wiring everything together is a tedious part of application development. There are several approaches to connect data, service, and presentation classes to one another. To contrast these approaches, we'll write the billing code for a pizza ordering website:

```
public interface BillingService {

  /**
   * Attempts to charge the order to the credit card. Both successful and
   * failed transactions will be recorded.
   *
   * @return a receipt of the transaction. If the charge was successful, the
   *      receipt will be successful. Otherwise, the receipt will contain a
   *      decline note describing why the charge failed.
   */
  Receipt chargeOrder(PizzaOrder order, CreditCard creditCard);
}
```
Along with the implementation, we'll write unit tests for our code. In the tests we need a `FakeCreditCardProcessor` to avoid charging a real credit card!

## Direct constructor calls
Here's what the code looks like when we just `new` up the credit card processor and transaction logger:

```
public class CreditCardBillingService implements BillingService {
  public Receipt chargeOrder(PizzaOrder order, CreditCard creditCard) {
    CreditCardProcessor processor = new PaypalCreditCardProcessor();
    TransactionLog transactionLog = new DatabaseTransactionLog();

    try {
      ChargeResult result = processor.charge(creditCard, order.getAmount());
      transactionLog.logChargeResult(result);

      return result.wasSuccessful()
          ? Receipt.forSuccessfulCharge(order.getAmount())
          : Receipt.forDeclinedCharge(result.getDeclineMessage());
     } catch (UnreachableException e) {
      transactionLog.logConnectException(e);
      return Receipt.forSystemFailure(e.getMessage());
    }
  }
}
```
This code poses problems for modularity and testability. The direct, compile-time dependency on the real credit card processor means that testing the code will charge a credit card! It's also awkward to test what happens when the charge is declined or when the service is unavailable.

To do:
* add all required classes
* use interfaces where possible
* create unit tests

## Factories

To do:
* implement factories for `CreditCardProcessor` and `TransactionLog`, factories should have `setInstance` and `getInstance` static methods
* create unit tests

## Dependency Injection

To do:
* use constructor parameters for providing required dependencies
* create unit tests

## Spring Core

To read:
* [Spring Framework - Quick Start](https://web.archive.org/web/20180523024533/https://projects.spring.io/spring-framework/)

To do:
* use Spring Core for dependency injection
* create unit tests

## Spring Boot & Spring Data

To read:
* [Building an application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)

To do:
* add required Spring Boot dependencies (starters)
* create controllers, services and repositories for users, orders, receipts, transaction logs and other useful objects
* add unit tests