# Denial-of-Service (DoS)

This example demonstrates DoS vulnerabilities and how they can be exploited.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Ignore if you have already done this once. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

    `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called __infodisclosure__. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

    `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

    ```
        http://localhost:3000/userinfo?id[$ne]=
    ```

4. Do you see the server crashing? - Yes

## For you to do

Answer the following:

### 1. **Briefly explain the potential vulnerabilities in `insecure.ts` that can lead to a DoS attack.**

The vulnerabilities in `insecure.ts` that can lead to a Denial-of-Service (DoS) attack include:

-   **NoSQL Injection**: The `/userinfo` route directly uses unsanitized user input (`id`) to query the MongoDB database. This allows attackers to craft malicious queries, such as `id[$ne]=`, which can cause the database to perform expensive operations or crash due to invalid query structures.
    
-   **Lack of Input Validation**: The server does not validate or sanitize the `id` parameter, enabling attackers to inject harmful payloads.
    
-   **No Rate Limiting**: The server does not limit the number of requests from a single IP, making it vulnerable to brute-force attacks or excessive traffic that can overwhelm server resources.
---
### 2. **Briefly explain how a malicious attacker can exploit them.**
Attackers can exploit these vulnerabilities in the following ways:

-   **Malicious Queries**: By sending a request like `http://localhost:3000/userinfo?id[$ne]=`, an attacker can trigger a NoSQL injection. This forces MongoDB to evaluate complex or invalid queries, which may lead to high CPU usage or crashes.
    
-   **Flooding the Server**: Without rate limiting, attackers can send repeated malicious requests in rapid succession, causing resource exhaustion and making the server unresponsive.
    
-   **Error Exploitation**: If the server lacks proper error handling, malformed queries can cause unhandled exceptions, leading to application crashes.
---
### 3. **Briefly explain the defensive techniques used in `secure.ts` to prevent the DoS vulnerability?**
The `secure.ts` implementation incorporates several defensive measures:

-   **Rate Limiting**: The use of `express-rate-limit` restricts each IP address to one request every 5 seconds. This prevents attackers from overwhelming the server with excessive traffic.
    
-   **Error Handling**: The route includes a `try-catch` block to handle errors gracefully. Invalid queries are logged, and the server responds with a 500 status code instead of crashing.
    
-   **Input Validation (Recommended)**: While not explicitly shown in the example, implementing input validation (e.g., ensuring `id` is a valid MongoDB ObjectID) would further mitigate NoSQL injection risks.