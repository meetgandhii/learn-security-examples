# Privilege Escalation

The example demonstrates a privilege escalation vulnerability and how to exploit it.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Start the **insecure.ts** server

    `$ npx ts-node insecure.ts`

3. In the browser, send a GET request

    ```
        http://localhost:3000/send-form
    ```

4. Try different UserIds and see which one gives you authorized access to change the role of that user.

## For you to do

Answer the following:

## 1. **Briefly explain the potential vulnerabilities in `insecure.ts`**

The `insecure.ts` implementation has several vulnerabilities that make it susceptible to privilege escalation:

-   **Lack of Authentication**: The code does not verify whether the user making the request is authenticated. It assumes that the `userId` provided in the request body corresponds to the user making the request.
    
-   **Insecure Authorization**: The authorization check (`if (user.role !== 'admin')`) is flawed because it relies on the `userId` provided in the request body. An attacker can easily spoof this value to impersonate an admin.
    
-   **No Session Management**: There is no mechanism to associate a session with a logged-in user, making it impossible to verify the identity of the requester securely.
    

## 2. **Briefly explain how a malicious attacker can exploit them.**

A malicious attacker can exploit these vulnerabilities as follows:

-   By sending a crafted request with a `userId` of an admin (e.g., `1`), the attacker can bypass authorization checks and gain admin privileges.
    
-   Once impersonating an admin, the attacker can change roles for other users, potentially escalating privileges for themselves or others.
    
-   Since there is no session-based authentication, there is no way to verify whether the request originates from an authorized source.
    

## 3. **Briefly explain the defensive techniques used in `secure.ts` to prevent the privilege escalation vulnerability?**

The `secure.ts` implementation addresses these vulnerabilities by incorporating robust security measures:

-   **Session-Based Authentication**: The code uses session management (`express-session`) to securely associate a logged-in user with their session. This ensures that only authenticated users can make requests.
    
-   **Role-Based Authorization**: The authorization check is based on the role of the logged-in user (`loggedInUser.role === 'admin`). This prevents attackers from spoofing a `userId` in the request body.
    
-   **Secure Cookies**: The session cookie is configured with security flags (`httpOnly`, `sameSite: 'strict'`) to prevent attacks like session hijacking and cross-site request forgery (CSRF).
    
-   **Validation of Input**: While not explicitly shown, input validation should be implemented to ensure that malicious data cannot be injected into requests.