# Repudiation

The example demonstrates a vulnerability that can lead to repudiation by malicious users attempting to access the services provided by a server.

## Steps to reproduce

1. Install all dependencies

    `$ npm install`

2. Run the server __insecure.ts__.

3. Pretend to be a malicous user and interact with the services by sending requests from the browser.

4. Do you think your actions can be repudiated?

Yes, my actions can be repudiated in the insecure.ts implementation. Here's why:

 - No Authentication: The server does not require authentication for sensitive endpoints like /send-message or /get-messages. This means anyone can perform actions without proving their identity.

 - No Logging: The server does not log user actions or IP addresses. As a result, there is no audit trail to associate your actions with you as a user.

 - No Accountability: Since there is no mechanism to verify or record who sent a message or retrieved messages, you can deny performing these actions later.

For example, if we send an inappropriate message using the /send-message endpoint, there is no way for the server to prove that it was me who sent it. Similarly, if you retrieve sensitive messages from /get-messages, the server cannot trace back the access to my identity or IP address.

## For you to do

## 1. **Briefly explain the vulnerability.**

The `insecure.ts` server is vulnerable to **repudiation** attacks. Repudiation refers to the inability of a system to hold users accountable for their actions, allowing malicious users to deny performing certain actions. The vulnerability in this code arises due to the lack of proper logging or auditing mechanisms that track and associate user actions with their identities. For example:

1.  **No Logging**: The `insecure.ts` server does not log any details about who performed specific actions (e.g., sending or retrieving messages). This makes it impossible to trace back actions to a specific user.
    
2.  **Unauthenticated Access**: The `/get-messages` endpoint allows anyone to retrieve all stored messages without authentication, making it impossible to identify who accessed the data.
    

Without these mechanisms, malicious users can perform actions (e.g., sending inappropriate messages or deleting sensitive information) and later deny their involvement.

## 2. **Briefly explain why the vulnerability is addressed in __`secure.ts`__.**

The `secure.ts` server addresses repudiation vulnerabilities by implementing the following measures:

1.  **Request Logging**: A middleware logs every incoming request with details such as the HTTP method, URL, timestamp, and IP address. This ensures that all user actions are recorded systematically in a log file (`server.log`), making it possible to trace actions back to specific users or IPs.
    
2.  **Authentication**: The `/get-messages` endpoint requires authentication (though simulated here). Unauthorized users are denied access with a `401 Unauthorized` response, ensuring that only authenticated users can retrieve messages.
    
3.  **Error Logging**: Errors are logged with timestamps and detailed messages, providing additional accountability in case of system failures or malicious activities.
    

These measures enhance non-repudiation by creating an audit trail that ties user actions to their identities or IP addresses.

## 3. **Which design pattern is used in the secure version to address the vulnerability? Briefly explain how it works?**

The design pattern used in `secure.ts` is **Logging and Auditing**. Here's how it works:

1.  **Logging Middleware**: A middleware intercepts every request and logs relevant details (e.g., method, URL, timestamp, IP). This creates a comprehensive record of all interactions with the server.
    
2.  **Error Handling Middleware**: Errors are logged with detailed information about what went wrong and when it occurred. This helps identify malicious activities or system vulnerabilities.
    
3.  **Authentication Checks**: By simulating authentication for sensitive endpoints like `/get-messages`, the server ensures that only authorized users can perform certain actions.
    

This pattern addresses repudiation by creating an immutable record of user actions, which can be used for auditing purposes and holding users accountable.
