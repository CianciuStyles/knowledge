# Testing

### Desired properties

* fast: the sooner we get feedback about what we just added or changed, the better.
* deterministic: if a test becomes "flaky", how will we find out if it started failing for a good reason?
* predictive: the tests should provide confidence that everything is working as expected, and if all are green then it should be fine to release and deploy the sytem.
* sensitive to changes in behaviour: our tests should focus on the behaviour the end-user sees, and ensure it is correct and stable. If the behaviour changes, we have to adapt the tests to reflect the new behaviour.
* insensitive to changes in structure: as long as we do not change the behaviour of the component, the tests should run without adapting them. If your tests depend on "how" the application is solving the business problem rather than if it "is" solving the business problem, you lose the possibility of refactoring \(changing the structure of your software without affecting the behaviour\).
* cheap to write, read and maintain: tests should be helping us, not slow us down. If a test fails, we should immediately see what it is about, find the production code it is testing, and fix the problem. Changing them also needs to be easy and fast.
* focused: each test should be about one thing, and it should be visible about which one. In the test, we should see only the details important to a certain desired behaviour, everything else should be hidden and abstracted away.

### Basic structure of a test

* Arrange:
  * initialises the component under test
  * sets up some desired state on which the test will be based
  * creates some test data
* Act: triggers the behaviour that should be tested
* Assert: verifies if the action triggered in the Act phase produced the expected outcomes
* Annihilate: clean up resources after the test was executed

```java
@Test
public void add() throws Exception {
   // arrange
   var article1 = mock(Article.class);
   var article2 = mock(Article.class);
   setupAvailableInStock(article1, article2);
   setupCalculatedPrice(article1, 9.95);
   setupCalculatedPrice(article2, 7.5);
   setupShippingAmount(3.5);

   // act
   shoppingCart.add(article1, 1);
   shoppingCart.add(article2, 3);

   // assert
   assertEquals(2, shoppingCart.items().size());

   assertEquals(1, shoppingCart.items().get(0).quantity());
   assertEquals(BigDecimal.valueOf(9.95), shoppingCart.items().get(0).amount());

   assertEquals(3, shoppingCart.items().get(1).quantity());
   assertEquals(BigDecimal.valueOf(22.50), shoppingCart.items().get(1).amount());

   assertEquals(BigDecimal.valueOf(32.45), shoppingCart.subtotalAmount());
   assertEquals(BigDecimal.valueOf(3.5), shoppingCart.shippingAmount());
   assertEquals(BigDecimal.valueOf(35.95), shoppingCart.totalAmount());
}
```

### Make it a specification

* It's possible to write tests in a way that even non-developers, like the stakeholders of our system, would be able to read and understand them.
* One option it to follow the behaviour-driven development \(BDD\) methodology:
  * rename the test to describe the concrete part of the behaviour in a given situation, e.g. `should_calculate_subtotal_and_total_amount_if_two_items_with_different_prices_and_quantities_are_added`
  * change the arrange/act/assert phases in the test to be given/when/then sections

```java
@Test
public void should_calculate_subtotal_and_total_amount_ \ 
   if_two_items_with_different_prices_and_quantities_are_added() 
   throws Exception {
   // given
   Article article1 = givenAnArticle()
       .withPrice(9.95)
       .availableInStock()
       .andGetIt();
   Article article2 = givenAnArticle()
       .withPrice(7.5)
       .availableInStock()
       .andGetIt();
   givenShippingAmount(3.5);

   // when
   shoppingCart.add(article1, 1);
   shoppingCart.add(article2, 3);

   // then
   assertThat(shoppingCart).containsNumberOfItems(2);
   assertThat(shoppingCart).containsItemFor(article1)
       .withQuantity(1).withAmount(9.95);
   assertThat(shoppingCart).containsItemFor(article2)
       .withQuantity(3).withAmount(22.50);
   assertThat(shoppingCart).hasSubtotalAmount(32.45);
   assertThat(shoppingCart).hasShippingAmount(3.5);
   assertThat(shoppingCart).hasTotalAmount(35.95);
}
```

## Articles

* [Anatomy of a Good Test](https://www.innoq.com/en/blog/anatomy-of-a-good-test/) - Torsten Mandry, Jacek Bilski

