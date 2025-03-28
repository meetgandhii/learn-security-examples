# Tampering

This example demonstrates information disclosure by injecting malicious query objects to a NoSQL database.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

    `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called __infodisclosure__. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

    `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

    ```
        http://localhost:3000/userinfo?username[$ne]=
    ```

4. Do you see user information being displayed despite the malicious request not having a valid username in the request?

## For you to do

Answer the following:

## 1. **Briefly explain the potential vulnerabilities in `insecure.ts`**

The **insecure.ts** server contains a NoSQL injection vulnerability due to the following issues:

-   **Direct Use of User Input in Database Queries**: The `username` parameter from the HTTP request is directly used in the `User.findOne()` query without any validation or sanitization. This allows attackers to inject malicious query objects into the database query.
    
-   **Lack of Input Validation**: There is no check to ensure that the `username` parameter is a valid string or adheres to expected formats, leaving the system open to exploitation.
    

## 2. **Briefly explain how a malicious attacker can exploit them.**

An attacker can exploit this vulnerability by crafting a malicious query object. For example:

-   By sending a request like `http://localhost:3000/userinfo?username[$ne]=`, the attacker leverages MongoDB's `$ne` operator (which means "not equal") to bypass the `username` condition entirely.
    
-   This results in the query `{ username: { $ne: "" } }`, which matches any document where the `username` field is not an empty string. Consequently, sensitive user information is disclosed without proper authentication.
    

## 3. **Briefly explain the defensive techniques used in `secure.ts` to prevent the information disclosure vulnerability?**

The **secure.ts** server addresses the vulnerabilities by implementing the following defensive techniques:

-   **Input Validation**: It checks if the `username` parameter is a string using `typeof username !== 'string'`. If it is not, the server returns a `400 Bad Request` response, preventing invalid inputs from being processed.
    
-   **Input Sanitization**: The code sanitizes the `username` input using `.replace(/[^\w\s]/gi, '')`, which removes any non-alphanumeric characters. This prevents malicious query operators like `$ne` from being injected into database queries.
    
-   **Error Handling**: The server includes robust error handling to catch and log database errors, ensuring that sensitive information is not leaked through error messages.
    

These measures collectively mitigate NoSQL injection risks and prevent unauthorized access to sensitive data.