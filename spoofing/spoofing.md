# Spoofing

This example demonstrates spoofind through two ways -- Stealing cookies programmatically and cross site request forgery (CSRF).

## Steps to reproduce the vulnerability

1. Install dependencies

    `$ npx install`

2. Start the **insecure.ts** server

    `npx ts-node insecure.ts`

3. Start the malicious server **mal.ts**

    `npx ts-node mal.ts`

4. Open __http://localhost:8000__ in a browser, type a name and Submit.

5. Open the __Application__ tab in the Browser's inspect pane. Find the __Cookies__ under __Storage__. You should see a __connect.sid__ cookie being set.

6. Open the HTML file __mal-steal-cookie.html__ file in the same browser (different tab). Open inspect and view the console.

7. Click the link in the HTML file. Do you see the cookie being stolen in the console?

8. Open the HTML file __mal-csrf.html__ file in the same browser (different tab). What do you see if the user has not logged out of **insecure.ts**? What do you see if the user has logged out? 


If the User Has Not Logged Out of insecure.ts:

The CSRF attack will succeed because the user's session is still active, and the browser automatically sends the session cookie (connect.sid) with the malicious request to /sensitive.

As a result, if the user is logged in as "Admin," the server processes the request and performs the sensitive operation. You will see a response indicating that the operation was successful (e.g., { message: 'Operation successful' }).

If the user is not an "Admin," the server will reject the request with { message: 'Unauthorized Access' }, but this still demonstrates that the malicious request was processed.

If the User Has Logged Out of insecure.ts:

The CSRF attack will fail because logging out destroys the session on the server and invalidates the session cookie.

When mal-csrf.html submits its request, no valid session cookie is sent, and the server will likely treat it as an unauthenticated request, returning an error or redirecting to a login page.

## For you to answer

Here are the answers to your questions regarding the spoofing vulnerability:

## 1. Briefly Explain the Spoofing Vulnerability in **insecure.ts**

The **insecure.ts** server is vulnerable to spoofing attacks due to improper session management and insecure cookie configurations. Specifically:

-   **Session Hijacking**: The session ID (`connect.sid`) is stored in a cookie that is not marked as `httpOnly`. This allows malicious scripts running on the client side (e.g., via XSS) to access and potentially steal the session cookie.
    
-   **Cross-Site Request Forgery (CSRF)**: The server does not implement protections against CSRF attacks, allowing attackers to perform unauthorized actions on behalf of authenticated users by tricking them into submitting malicious requests.
    

## 2. Briefly explain different ways in which vulnerability can be exploited.

### a. **Stealing Cookies Programmatically**

-   Attackers can use malicious scripts (as demonstrated in `mal-stealcookie.html`) to extract session cookies from the browser if `httpOnly` is not set.
    
-   Once the session cookie is stolen, attackers can impersonate legitimate users by sending requests with the stolen cookie.
    

### b. **Cross-Site Request Forgery (CSRF)**

-   Attackers can craft malicious HTML forms or JavaScript (as shown in `mal-csrf.html`) that automatically submit requests to sensitive endpoints (e.g., `/sensitive`) using an authenticated user's session.
    
-   Since the server does not validate the origin of requests or require additional CSRF tokens, it cannot distinguish between legitimate and malicious requests.
    

## 3. Briefly explain why `secure.ts` does not have the spoofing vulnerability in `insecure.ts`.

The **secure.ts** server addresses both vulnerabilities by implementing secure configurations:

### a. **Mitigation Against Session Hijacking**

-   The `cookie` configuration includes `httpOnly: true`, preventing client-side scripts from accessing cookies.
    
-   The `sameSite: true` flag ensures cookies are only sent with requests originating from the same site, reducing exposure to CSRF attacks.
    

### b. **Mitigation Against CSRF**

-   While not explicitly shown in the code, secure implementations typically include CSRF tokens for sensitive operations. The `sameSite` flag alone provides basic protection by ensuring cookies are not sent with cross-origin requests.
    

By enforcing these secure cookie options and adopting best practices for session management, **secure.ts** significantly reduces the risk of spoofing attacks.

