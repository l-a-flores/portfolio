
# Account-Based Commerce & POS Platform

## Overview
This system is a production-ready, account-based commerce platform designed to support real business operations.
It includes automated client account creation, a client profile portal, accounting workflows, and point-of-sale (POS) functionality.

The system was built with long-term maintainability in mind and structured to reflect real business domains rather than technical shortcuts.

---

## Core Responsibilities
- Client registration and profile management
- Automated account number generation
- Accounting and transaction workflows
- POS transaction handling
- Backend services supporting operational use

---

## System Architecture

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/f77ffdef-b729-461e-8920-da307738583a" />

This is a simplified representation focused on business flow rather than implementation detail.

---

## Key Engineering Decisions

### 1. Deterministic Account Number Generation
Account numbers are generated automatically in a predictable, collision-safe format that aligns with business expectations and auditing needs.

```php
class AccountNumberService {
    public function generate(int $clientId): string {
        return sprintf(
            'ACCT-%s-%06d',
            date('Y'),
            $clientId
        );
    }
}
```

### 2. Clear Domain Separation
Business logic is kept out of controllers and isolated within service classes.

```php
class ClientRegistrationService {
    public function register(ClientRequest $request): Client {
        $this->validator->validate($request);
        $client = $this->repository->create($request);
        $client->assignAccount(
            $this->accountNumberService->generate($client->id)
        );
        return $client;
    }
}
```
---

## Refactoring Example (Legacy → Maintainable)
Before
```php
$client = $_POST;
$account = "ACCT" . $client['id'] . date("Y");
saveClient($client, $account);
```

After
```php
$request = ClientRequest::fromHttp($_POST);
$client = $this->registrationService->register($request);
```

Impact:
  - Reduced coupling
  - Improved testability
  - Safer future changes

---

## Production Considerations
  - Input validation at service boundaries
  - Guard clauses to prevent invalid state
  - Incremental refactoring without breaking live operations
  - Business-driven naming and structure

```php
if (!$user->isAuthorized()) {
    throw new AuthorizationException();
}
```

---

### 3. Guard Clauses and Validation
```php
if (empty($request->email)) {
    throw new ValidationException('Email is required');
}

if (!$this->emailVerifier->isValid($request->email)) {
    throw new ValidationException('Invalid email format');
}
```

---

### 4. Repository Abstraction
```php
interface ClientRepository {
    public function create(ClientRequest $request): Client;
    public function findByAccount(string $accountNumber): ?Client;
}
```

---

### 5. Transaction Boundary
```php
$this->db->transaction(function () use ($client, $account) {
    $this->clientRepository->save($client);
    $this->accountRepository->save($account);
});
```

