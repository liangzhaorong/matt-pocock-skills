# 好测试与坏测试

## 好测试

**集成风格**：通过真实接口测试，而不是对内部部件做 mock。

```typescript
// GOOD：测试可观察的行为
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

特征：

- 测试的是用户/调用方关心的行为
- 只使用公共 API
- 能挺过内部重构
- 描述"是什么"（WHAT），而不是"怎么做"（HOW）
- 每个测试只包含一个逻辑断言

## 坏测试

**实现细节测试**：与内部结构耦合。

```typescript
// BAD：测试实现细节
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

危险信号：

- mock 内部协作者
- 测试私有方法
- 断言调用次数/调用顺序
- 行为并没有变，重构却让测试挂掉
- 测试名描述的是"怎么做"而不是"是什么"
- 通过外部手段而不是接口进行验证

```typescript
// BAD：绕过接口进行验证
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// GOOD：通过接口验证
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```

**同义反复测试**：期望值只是复述了实现，因此测试从构造上就必然通过。

```typescript
// BAD：期望值是用与代码相同的计算方式重新算出来的
test("calculateTotal sums line items", () => {
  const items = [{ price: 10 }, { price: 5 }];
  const expected = items.reduce((sum, i) => sum + i.price, 0);
  expect(calculateTotal(items)).toBe(expected);
});

// GOOD：期望值是独立的、已知的字面值
test("calculateTotal sums line items", () => {
  expect(calculateTotal([{ price: 10 }, { price: 5 }])).toBe(15);
});
```
