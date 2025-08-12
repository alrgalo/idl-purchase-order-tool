---
trigger: always_on
---


### File and Folder Naming

* Use `kebab-case` for all files and folders.
* **Controllers**: `{resource}.controller.ts`
  → e.g., `purchase-order.controller.ts`
* **Services**: `{resource}.service.ts`
* **Models/Entities**: Use **singular** nouns
  → e.g., `user.ts`, not `users.ts`

---

### Class and Interface Naming

* Use `PascalCase` for class and interface names.
* Do **not** prefix interfaces with `I`.

```ts
// ✅ Good
interface PurchaseOrderPayload {}
class PurchaseOrderService {}

// ❌ Avoid
interface IPurchaseOrder {}
```

---

### Variables and Functions

* Use `camelCase`.
* Function names must **start with a verb** and clearly describe the action.
* Avoid abbreviations (`po`, `resp`, `val`, etc.)

```ts
// ✅ Clear
const createdPurchaseOrder = await createPurchaseOrder(data);

// ❌ Ambiguous
const po = await createPO(val);
```

---

### Import Order

1. Node built-ins
2. External libraries (`@nestjs`, `axios`, etc.)
3. Internal modules (`@app/common`, `@lib/utils`, etc.)
4. Relative imports (`./utils`)

Separate groups with a blank line.

```ts
import { readFileSync } from 'fs';

import { Controller } from '@nestjs/common';

import { CreateUserDto } from '@app/common/dtos';

import { hashPassword } from './utils';
```

---

### Controllers

* Keep request handlers minimal. Delegate logic to services.
* Use dependency injection—**never use `new` manually**.
* Return structured responses using **object literals**, not step-by-step mutation.

```ts
// ✅ Good
return {
  status: true,
  result: createdPO,
  message: 'Purchase order created successfully',
};
```

```ts
// ❌ Bad
response.status = true;
response.result = createdPO;
response.message = 'Purchase order created successfully';
```

> Why it matters: Easier to test, debug, and refactor.

---

### Services

* Services must be **stateless** and **single-responsibility**.
* Never return raw DB records—convert to DTOs or plain objects.
* Extract reusable logic into private methods or shared utilities.

```ts
async create(data: CreateUserDto): Promise<UserDto> {
  const user = await this.repo.create(data);
  return this.toDto(user);
}
```

---

### DTOs

* DTOs must be **pure** (no methods, no logic).
* Group DTOs per feature inside a `dtos/` folder.
* Use DTOs for both **input validation** and **output formatting**.

```ts
export class PurchaseOrderDto {
  id: string;
  amount: number;
  status: string;
}
```

---

### Error Handling

* Throw structured exceptions (`BadRequestException`, `NotFoundException`, etc.)
* Include **descriptive error messages** with relevant context.
* Let services throw; let controllers format responses.

```ts
throw new NotFoundException(`Purchase order ${id} not found`);
```

---

### Validation

* Use `class-validator` decorators in DTOs.
* Never manually validate data in services.

```ts
export class CreatePurchaseOrderDto {
  @IsString()
  @IsNotEmpty()
  supplierName: string;

  @IsNumber()
  amount: number;
}
```

---

### Logging

* Use consistent log levels: `debug`, `info`, `warn`, `error`.
* Include context (`userId`, `requestId`, etc.)
* **Never** log sensitive data (passwords, tokens, secrets).

```ts
this.logger.log(`Purchase order ${id} updated by user ${userId}`);
```

---

### Frontend Rules

#### Structure

* Avoid div soup—don’t over-nest or use meaningless wrappers.
* Use semantic HTML (`<main>`, `<section>`, `<button>`, etc.)

```tsx
// ✅ Better
<main>
  <p>Hello</p>
</main>
```

```tsx
// ❌ Too much
<div className="wrapper">
  <div className="container">
    <div className="content">
      <p>Hello</p>
    </div>
  </div>
</div>
```

#### Styling

* Use external styles (CSS modules, SCSS, Tailwind).
* Avoid inline styles unless dynamic.
* Don’t hardcode styling in JSX.

```tsx
// ✅ Tailwind
<div className="mt-4" />

// ❌ Inline
<div style={{ marginTop: '1rem' }} />
```

---

### Formatting

* Use **Prettier**.
* Max line length: 100–120 characters.
* One statement per line.
* Always use **semicolons**.
* Prefer `const`; avoid `var`.

---

### Comments

* Only comment on non-obvious logic or decisions.
* Use `TODO:` for future work, `FIXME:` for known issues.
* Don’t state the obvious.

```ts
// ✅ Good
// Validate supplier ID with external registry

// ❌ Redundant
const count = 1; // Set count to 1
```

---

### Testing

* Use **Jest** with descriptive test names.
* Group tests by feature and method.
* Mock external dependencies. Don’t test third-party libraries.

```ts
describe('PurchaseOrderService', () => {
  describe('create()', () => {
    it('creates a new purchase order and returns a DTO', async () => {
      const result = await service.create(payload);
      expect(result).toMatchObject({ amount: 1000 });
    });
  });
});
```

---

### Backend Logic & Security

> ✅ This rule is enforced by Windsurf. Always on.

* **Do not compute anything on the frontend.**

  * All business logic, rules, and calculations (e.g., totals, scoring, limits) must be done in the backend.
  * Frontend must only render what the backend returns.

* **Never trust user input.**

  * Validate and sanitize all inputs server-side.

* **Prevent SQL injection.**

  * Always use parameterized queries or safe ORM methods.
  * Never interpolate raw input into SQL strings.

* **Enforce separation of concerns.**

  * Backend owns all rules and decisions.
  * Frontend only handles UI, forms, and user interaction.

* **No critical logic or validation in the frontend alone.**

  * All security, role checks, and data restrictions must be enforced on the server.