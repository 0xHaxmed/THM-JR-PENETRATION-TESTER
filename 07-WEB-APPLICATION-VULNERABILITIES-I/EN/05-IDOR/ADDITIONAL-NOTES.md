# Advanced Engineering Notes & Deep Dive: Insecure Direct Object References (IDOR) & BOLA

---

## 1. Access Control Models & The Anatomy of BOLA

In secure software engineering, access control models define how subjects (users, services) interact with objects (files, database records, API resources). IDOR—designated in API security as **Broken Object Level Authorization (BOLA)**—is the breakdown of the authorization check at the specific object tier.

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           ACCESS CONTROL PARADIGM COMPARISON                            |
|                                                                                         |
|  1. Role-Based Access Control (RBAC):                                                   |
|     - Verifies user's broad role (e.g., "Is user an Admin or Customer?").               |
|     - Fails to prevent IDOR: Both Alice and Bob are "Customers," so RBAC passes,        |
|       yet Alice should NOT view Bob's account!                                          |
|                                                                                         |
|  2. Attribute-Based Access Control (ABAC):                                              |
|     - Evaluates contextual attributes: user ID, record owner ID, department, time.      |
|     - Successfully prevents IDOR by evaluating: `user.id == record.owner_id`.           |
|                                                                                         |
|  3. Relationship-Based Access Control (ReBAC):                                          |
|     - Evaluates graph relationships (e.g., Google Zanzibar model).                      |
|     - Checks whether a relation path exists: `Subject -> MemberOf -> Organization`.    |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## 2. Horizontal vs. Vertical vs. Contextual IDOR

IDOR vulnerabilities manifest across three distinct dimensional vectors:

1. **Horizontal IDOR (Peer-to-Peer Privilege Violation):**
   - An attacker accesses or mutates resources belonging to a peer at the exact same privilege tier.
   - *Example:* User A changing `invoice_id=105` to `invoice_id=106` to inspect User B's billing records.
2. **Vertical IDOR (Privilege Escalation):**
   - A low-privilege user modifies an object reference to access or modify resources reserved for higher-privilege administrative accounts.
   - *Example:* Standard employee modifying `role_id=2` to `role_id=1` on an update request to elevate their account to Super-Administrator.
3. **Contextual / Multi-Tenant IDOR:**
   - In Software-as-a-Service (SaaS) multi-tenant platforms, tenant boundary isolation fails. An employee belonging to Organization X accesses records belonging to Organization Y simply by supplying Organization Y's tenant ID or project identifier.

---

## 3. Defensive Engineering: Indirect Reference Maps vs. Session Context

Developers have multiple architectural patterns to completely eliminate IDOR vulnerabilities:

### Pattern A: The Indirect Reference Map (Session-Scoped Keys)
Instead of exposing database primary keys (`1`, `2`, `3`) or public UUIDs to the client, the server dynamically generates an ephemeral, session-bound mapping table upon rendering:

```
[ Server-Side Session Cache for User 105 ]
Client Key "alpha"   ==> Database ID 105 (Own Record)
Client Key "beta"    ==> Database ID 482 (Shared Document)
```
- In the HTML/API response, the client receives: `/download?file=alpha`.
- If an attacker tampers the parameter to `/download?file=gamma`, the lookup fails in their session map, and the server returns HTTP 404/403.
- The client never learns or transmits real database identifiers.

### Pattern B: Implicit Session Derivation (The Gold Standard)
For single-user profile endpoints, the client should **never** provide the user identifier. The server must derive the user identity strictly from the cryptographically verified session token:

```php
// VULNERABLE PATTERN:
$user_id = $_GET['id']; // Untrusted input
$profile = db_fetch_user($user_id);

// HARDENED PATTERN:
$user_id = $_SESSION['authenticated_user_id']; // Trusted server session
$profile = db_fetch_user($user_id);
```

### Pattern C: Query-Level Ownership Scoping
When retrieving objects that belong to a user, the database query itself must enforce ownership in the SQL `WHERE` clause:

```sql
-- VULNERABLE QUERY:
SELECT * FROM orders WHERE order_id = :input_order_id;

-- HARDENED QUERY (Scoped to authenticated user):
SELECT * FROM orders WHERE order_id = :input_order_id AND user_id = :session_user_id;
```
If the requested `order_id` belongs to another customer, the query returns zero rows, preventing data leakage automatically.

---

## 4. Modern Enterprise Framework Authorization Blueprints

### A. Laravel (PHP) Policies & Gateways:
```php
// In CustomerController.php:
public function show(Customer $customer)
{
    // Evaluates CustomerPolicy: does auth()->user()->id === $customer->user_id?
    $this->authorize('view', $customer);
    return response()->json($customer);
}
```

### B. Spring Boot (Java) Method Security:
```java
@GetMapping("/api/orders/{orderId}")
@PreAuthorize("@orderSecurity.isOwner(authentication, #orderId)")
public OrderDTO getOrder(@PathVariable Long orderId) {
    return orderService.getOrderById(orderId);
}
```

### C. Django (Python) Object-Level Guards:
```python
from rest_framework import permissions

class IsOwnerPermission(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user

# In API ViewSet:
permission_classes = [permissions.IsAuthenticated, IsOwnerPermission]
```

---

## 5. Automated IDOR Auditing: The Burp Suite Autorize Pipeline

In professional penetration testing, auditing large-scale web applications manually for IDOR across hundreds of endpoints is time-prohibitive. Security consultants utilize the **Autorize** extension in Burp Suite:

```
[ Tester navigates app as High-Privilege User A ]
                      │
                      ▼
             [ Burp Suite Proxy ]
                      │
                      ▼ Intercepts Request
              [ Autorize Plugin ]
                      │
        ┌─────────────┴─────────────┐
        ▼                           ▼
[ Request Clone 1 ]         [ Request Clone 2 ]
Replaces Cookie with        Strips Cookies Entirely
Low-Privilege User B        (Unauthenticated Check)
        │                           │
        ▼                           ▼
[ Dispatches to Server ]    [ Dispatches to Server ]
        │                           │
        └─────────────┬─────────────┘
                      ▼
[ Compares Status Codes, Headers & Response Body Lengths ]
  - Both 200 OK & identical lengths? ➔ [ RED FLAG: CRITICAL IDOR! ]
  - Clone returns 403 Forbidden?     ➔ [ GREEN: SECURE ]
```
