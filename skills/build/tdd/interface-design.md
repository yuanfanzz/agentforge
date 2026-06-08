---
name: tdd-interface-design
description: Use when designing public interfaces for testability — accept dependencies, return results, keep surface area small.
---

# Interface Design for Testability

Good interfaces make testing natural:

## 1. Accept dependencies, don't create them

```
# Testable
function process_order(order, payment_gateway):
    ...

# Hard to test
function process_order(order):
    gateway = StripeGateway()
    ...
```

When a function creates its own dependencies internally, you can't substitute them in tests. Pass them in.

## 2. Return results, don't produce side effects

```
# Testable — assert on return value
function calculate_discount(cart) -> Discount:
    ...

# Hard to test — must inspect state after call
function apply_discount(cart):
    cart.total -= discount   # void, side effect only
```

Functions that return values are trivially testable. Functions that only mutate state require setup, teardown, and state inspection.

## 3. Small surface area

- Fewer methods = fewer tests needed
- Fewer parameters = simpler test setup
- One public entry point that orchestrates internal complexity (see [deep-modules.md](deep-modules.md))

## 4. Separate construction from use

```
# Construction is a separate concern
gateway = PaymentGateway(config)
# Use is simple and testable
result = gateway.charge(amount)
```

Don't mix configuration, dependency creation, and business logic in one function. Each concern should be independently testable.
