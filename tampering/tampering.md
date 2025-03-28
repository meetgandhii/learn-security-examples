# Tampering

This example demonstrates tampering through script injection.

## Steps to reproduce

1. Install all dependencies

    `npm install`

2. Start the **insecure.ts** server

    `npx ts-node insecure.ts`

3. In the browser, type a potentially malicious script in the name field of the form

    ```
        <script> document.body.innerHTML = "<a href='https://google.com'> Gotcha </a>"</script>
    ```

4. Do you see the potentially malicious hyperlink being injected into the form?

## For you to do

Answer the following:

## 1. Briefly explain the potential vulnerabilities in `insecure.ts`

The **insecure.ts** file has several vulnerabilities that expose it to security risks, particularly **Cross-Site Scripting (XSS)** attacks:

-   **Lack of Input Sanitization**: User input (`req.body.name`) is directly inserted into the HTML response without sanitization, allowing attackers to inject malicious scripts.
    
-   **Session Mismanagement**: While sessions are implemented, there is no validation or mechanism to prevent unauthorized tampering with session data.
    
-   **Dynamic Session Secret**: The session secret is passed via `process.argv`, which could lead to weak or predictable secrets if not properly managed.
    

## 2. Briefly explain how a malicious attacker can exploit them.

A malicious attacker can exploit these vulnerabilities in the following ways:

-   **XSS Attack**: By entering a malicious script (e.g., `<script>document.body.innerHTML = "Gotcha"</script>`) into the name field, the script is executed when the page is rendered. This allows attackers to manipulate the DOM, steal cookies, or redirect users to malicious sites.
    
-   **Session Hijacking**: If the session secret is weak or predictable, attackers could forge session cookies and impersonate legitimate users.
    
-   **Privilege Escalation**: Since there is no robust mechanism to verify user roles beyond checking `req.session.user === 'Admin'`, an attacker could manipulate session data to gain unauthorized access.
    

## 3. Briefly explain why `secure.ts` does not have the same vulnerabilties?

The **secure.ts** file addresses these vulnerabilities through improved security practices:

## Key Improvements:

1.  **Input Sanitization**:
    
    -   The `escapeHTML` function ensures that user input is sanitized before being used in HTML responses. This prevents XSS attacks by escaping special characters (`<`, `>`, `&`, `"`, `'`).
        
2.  **Stronger Session Management**:
    
    -   The session secret remains dynamic but should ideally be securely generated and stored (e.g., using environment variables or a secret management system).
        
    -   Cookies are configured with stricter security options (`httpOnly` and `sameSite: 'strict'`) to mitigate risks like Cross-Site Request Forgery (CSRF).
        
3.  **Improved Code Practices**:
    
    -   By sanitizing input and adhering to secure coding principles, the risk of tampering or exploitation is significantly reduced.