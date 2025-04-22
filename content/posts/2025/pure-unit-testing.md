---
title: "The Secret to Reliable Salesforce Deployments: Pure Unit Testing"
date: 2025-04-22T11:34:01+02:00
tags:
    [
        apex,
        unit testing,
        unit,
        testing,
        pure,
        shift left,
        deployment,
        performance,
        dependency injection,
        mocking,
    ]
thumbnail: "/images/posts/2025/pure-unit-testing/apex-unit-testing-icon.webp"
summary: "Write pure Apex unit tests that are isolated from the database and external dependencies. You will achieve faster deployments, more reliable code, and easier maintenance in Salesforce development."
draft: false
---

In Salesforce development, Apex unit tests are key to reliable deployments. Yet, many tests aren't true unit tests. They rely on database operations, insert records, and trigger Salesforce's order of execution. This means your Apex unit test also tests flows, validation rules, and more. It's an integration test disguised as a unit test. Today, we'll explore this problem and how to write pure unit tests that test your Apex logic in isolation.

With this simple change in mindset, you can dramatically improve your unit test execution time and keep your business happy! After reading this article, you'll be eager to join the pure unit testing movement. Feel free to share your experience with me.

![Pure Unit Testing, by Nicolas Vanden Bossche](/images/posts/2025/pure-unit-testing/pure-unit-testing-title-image.png)

## The Problem with Integration Tests Masquerading as Unit Tests

When Apex tests perform DML operations, they tie themselves to Salesforce's order of execution. Changes to validation rules, flows, or duplicate rules can break these tests, even if your Apex code is unchanged.

[![Salesforce Order of Execution, showing the 20 steps that happen when you save a record in Salesforce.](/images/posts/2025/pure-unit-testing/order-of-execution.png)](https://architect.salesforce.com/view/MCOOL7W3TDDVFZZGSTXP7SLSI6NI)
_In the above image you can see the Order of Execution, where the actual Apex execution is highlighted. Executing DML will run through all steps instead of only the business logic to test._

This leads to unpredictability. Tests become slow, brittle, and hard to maintain. Also, increased test execution time impacts development cycles and deployments. As you add tests, the cutover phase during go-live lengthens, delaying releases and increasing risk. Long test suites extend cutover times, delaying releases.

## What is a Pure Unit Test and How to Write Them

A pure unit test isolates and tests a single class or method, free from outside dependencies like the database. Mocking dependencies is crucial for this isolation. The benefits: faster execution, predictable results, easier debugging, and better maintenance. By focusing on logic, not data persistence, you create robust and reliable tests.

The speed of pure unit tests enables you to write more of them. This reduces the need for lengthy integration tests or manual testing. Shifting testing earlier in the development process, known as "shift left," allows you to find bugs sooner. Early bug detection reduces the cost of fixing issues later in the lifecycle. Not only does this save time and money by finding problems earlier, but it also minimises the expense of late-stage bug fixes.

![Visual showing the shift left model, indicating a higher attention to quality early on in the lifecycle (design & build).](/images/posts/2025/pure-unit-testing/shift-left-model.png)

For a detailed walkthrough and code examples, I highly recommend Mitch Spano's blog post, 'Pure Unit Testing in Apex'.
Mitch's post highlights ways to improve test performance and achieve purity:

-   Use fake record IDs to bypass database lookups, speeding up tests.
-   Use mockable query methods to control SOQL results in tests.
-   Decouple code from dependencies for easier mocking and testing.

By using these techniques, you can write Apex unit tests that are faster, more reliable, and easier to maintain.

## Addressing Common Objections

Developers often worry about the practicality of pure unit testing. Common concerns: testing the whole flow, the complexity of mocking, and tightly coupled code. Let's address these concerns.

### Testing the entire flow

Integration tests are still useful, and Apex tests can be used for them. However, keep them separate from unit tests. Use integration tests to verify interactions between components, but keep unit tests focused on individual code units.

This is a typical issue in Salesforce implementations: all tests are integration tests, and barely any tests are pure unit tests. This means that you're missing out on a huge benefit from testing early, i.e. shifting left. You're also pushing the majority of testing into a slower form of testing. This approach should be avoided at all costs.

![Pyramid showing unit testing at the bottom, integration testing at the middle, UI testing at the top. Arrows indicating the lower part of the pyramid is faster & more isolated. The lower in the pyramid, the higher the number of tests](/images/posts/2025/pure-unit-testing/testing-strategy.png)
_The above image shows an overview of how integrations tests and unit tests can be combined in the best way._

### Mocking is complex

Mocking is a skill to learn. Start with simple examples, removing one DML operation at a time, which can help. The [Salesforce Apex Testing Documentation](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_unit_tests_mocks.htm) is extremely helpful. Use mocking frameworks like [Apex Mockery](https://developer.salesforce.com/blogs/2023/06/introducing-apex-mockery-a-unit-test-mocking-library) or the [ffLib Mocking framework](https://github.com/apex-enterprise-patterns/fflib-apex-mocks) to speed up development.

Improve gradually. Don't apply mocking everywhere and all at once, you will go crazy. Tackle it one step, one method, one class at a time and improve steadily rather than all at once.

### Tightly coupled code

Your Apex code might directly instantiate another class or directly perform SOQL queries within its methods, making it difficult to isolate for testing. It's crucial to refactor your code to decouple dependencies. As with mocking, don't try to tackle everything at once. Use the principle of "leave it cleaner than you found it": when you change a piece of code, improve it by reducing the tight coupling. Use design patterns like dependency injection, interfaces or service layer to achieve this.

**Dependency injection** will pass the dependencies into the class Instead of a class creating its dependencies internally. This allows you to inject mock implementations during testing.

{{<highlight java "linenos=true">}}
public class OrderService {
private AccountDao accountDao;

    public OrderService(AccountDao dao) { // Dependency passed in
        this.accountDao = dao;
    }

    public Account getAccountInfo(Id accountId) {
        return accountDao.getAccount(accountId);
    }

}
{{</highlight>}}

**Interfaces** hide the concrete implementation details and allow your classes to depend instead on the abstract interface.

{{<highlight java "linenos=true">}}
public class FileLogger implements Log {
public void writeLog(String message) {
// ... write to file ...
}
}

public class Logger {
private Log logger;

    public Logger(Log logImpl) { // Dependency on interface
        this.logger = logImpl;
    }

    public void log(String message) {
        logger.writeLog(message);
    }

}
{{</highlight>}}

**Service Layer Pattern** allows you to separate your database logic from your business logic, creating independence between several layers.

{{<highlight java "linenos=true">}}
public class AccountService {
private AccountDao accountDao = new AccountDao();

    public void updateAccountDescription(Id accountId, String newDescription) {
        Account acc = accountDao.getAccount(accountId); // Delegate data access
        acc.Description = newDescription; // Business logic
        accountDao.updateAccount(acc); // Delegate data access
    }

}

public class AccountDao {
public Account getAccount(Id accountId) {
return [SELECT Id, Description FROM Account WHERE Id = :accountId];
}

    public void updateAccount(Account acc) {
        update acc;
    }

}
{{</highlight>}}

You'll make your Apex code more modular, maintainable, and easier to test with pure unit tests once you apply these techniques.

## Conclusion: Embrace Pure Unit Testing for Robust Salesforce Development

Pure unit testing is essential for strong Salesforce applications. Unit tests verify individual components, while integration tests validate interactions between them. It provides faster test execution, predictable results, and easier debugging, which leads to quicker development cycles. Additionally, by "shifting left" and finding bugs earlier, you reduce costs and improve quality.

Start by adopting pure unit testing today! Look at your longest-running tests and see where you can remove DML statements, or just improve the code that you touch. Slow and steady wins the race.
