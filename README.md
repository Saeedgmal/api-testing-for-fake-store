# api-testing-for-fake-store
1️⃣ Login with token 2️⃣ GET Products 3️⃣ POST Cart 4️⃣ DELETE Cart
# FakeStore API Testing

## Project: FakeStore API Testing
**Collection Name:** Full API Flow  
**Environment Name:** Full API Flow Env

---

## Environment Variables

| Variable Name       | Value / Description                                 |
|--------------------|----------------------------------------------------|
| `base_url`          | `https://fakestoreapi.com`                        |
| `token`             | Will be set dynamically from Login Request        |
| `cart_id`           | Will be set dynamically from POST Cart Request    |
| `first_product_id`  | Will be set dynamically from GET Products Request |

---

## Collection Requests

### 1️⃣ Login
- **Method:** POST  
- **Endpoint:** `{{base_url}}/auth/login`  
- **Body (raw JSON):**
```json
{
  "username": "mor_2314",
  "password": "83r5^_"
}
Tests Script:

let res = pm.response.json();
pm.environment.set("token", res.token);
pm.test("Login successful", function () {
    pm.response.to.have.status(200);
});
console.log("Token stored:", res.token);


Purpose: Authenticate and store the token for future requests.

2️⃣ GET Products

Method: GET

Endpoint: {{base_url}}/products

Headers:

Authorization: Bearer {{token}}


Tests Script:

let res = pm.response.json();
pm.environment.set("first_product_id", res[0].id);
pm.test("GET Products successful", function () {
    pm.response.to.have.status(200);
});


Purpose: Retrieve products and store first product ID for POST Cart.

3️⃣ POST Cart

Method: POST

Endpoint: {{base_url}}/carts

Headers:

Authorization: Bearer {{token}}
Content-Type: application/json


Body (raw JSON):

{
  "userId": 1,
  "date": "2026-01-28",
  "products": [
    {
      "productId": {{first_product_id}},
      "quantity": 2
    }
  ]
}


Tests Script:

let res = pm.response.json();
pm.environment.set("cart_id", res.id);
pm.test("Cart created successfully", function () {
    pm.response.to.have.status(201);
});


Purpose: Create cart dynamically using product ID from GET request.

4️⃣ DELETE Cart

Method: DELETE

Endpoint: {{base_url}}/carts/{{cart_id}}

Headers:

Authorization: Bearer {{token}}


Tests Script:

pm.test("Cart deleted successfully", function () {
    pm.response.to.have.status(200);
});


Purpose: Clean up by deleting the cart created.

Collection Runner / Flow

Open Collection → Full API Flow → Run

Select Environment: Full API Flow Env

Requests will execute in this order:

Login → gets token

GET Products → saves first product ID

POST Cart → saves cart ID

DELETE Cart → deletes cart

Optional: Can attach a Data File (CSV/JSON) to run the flow for multiple users.
