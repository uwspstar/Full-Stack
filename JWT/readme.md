
# JWT-based Authentication
- Angular Security - Authentication With JSON Web Tokens (JWT): The Complete Guide by Angular University
https://blog.angular-university.io/angular-jwt-authentication/

### 1. JWT-based Authentication in a Nutshell
```
If JWTs are used for Authentication, they will contain at least 1) a user ID and 2) an expiration timestamp.
```
  - Bearer Token identifies the user that owns it, and defines a user session.
  - A bearer token is a signed temporary replacement for the username/password combination!
  - a separately hosted Login Page ```in our domain or in a third-party domain```
  - Sign-On solution
  ```
  hosted either in our domain or in a third-party domain. In an enterprise scenario, 
  the login page is often hosted on a separate server, 
  which is part of a company-wide Single Sign-On solution. 
  ```
  - third-party Authentication provider : 
  ```
  On the public Internet, the login page might also be:
    • hosted by a third-party Authentication provider such as Auth0
    • available directly in our single page application using a login screen route or a modal
  ```
  - A separately hosted login page is an improvement security-wise 
  ```
  because this way the password is never directly handled by our application code in the first place.
  ```
  - create a separate Authentication service
  ```
  for example later we need to change security providers or refactor our security logic, 
  we only have to change this class.
  ```
### 2. Creating a JWT-based user Session
  - using node-jsonwebtoken 
  https://github.com/auth0/node-jsonwebtoken
  - $ npm install jsonwebtoken
  ```
  import {Request, Response} from "express";
  import * as express from 'express';
  const bodyParser = require('body-parser');
  const cookieParser = require('cookie-parser');
  import * as jwt from 'jsonwebtoken';
  import * as fs from "fs";
  
  const app: Application = express();
  app.use(bodyParser.json()); 
  
  const RSA_PRIVATE_KEY = fs.readFileSync('./demos/private.key');  // *** private key ***
  
  app.route('/api/login').post(loginRoute);
  
  export function loginRoute(req: Request, res: Response) {
    const email = req.body.email;
    const password = req.body.password;
    
    if (validateEmailAndPassword()) {
    const userId = findUserIdForEmail(email);
    
    const jwtBearerToken = jwt.sign({}, RSA_PRIVATE_KEY, {
      algorithm: 'RS256', // *** Auth0 are now using RS256 by default ***
      expiresIn: 120,
      subject: userId
    }
    // send the JWT back to the user
    // TODO - multiple options available
  } else {
  // send status 401 Unauthorized
  res.sendStatus(401);
  }
 }
```
- RS256 Signatures and operational advantages
```
RS256 is a JWT signature type that is based on RSA, 
which is a widely used public key encryption technology.

RS256 signatures work in the following way:
  • a private key (like RSA_PRIVATE_KEY in our code) is used for signing JWTs
  • a public key is used to validate them
  • the two keys are not interchangeable: 
    they can either only sign tokens, or only validate them, 
    but neither key can do both things
  
RS256 security and operational advantages
  • we only have to deploy the private signing key in the Authentication Server, 
  and not on the multiple Application servers that use the same Authentication Server
  • We don't have to shut down the Authentication and the Application servers in a coordinated way, 
  in order to change a shared key everywhere at the same time
  • the public key can be published in a URL 
  and automatically read by the Application server at startup time and periodically

This last part is a great feature: 
being able to publish the validating key gives us built-in key rotation and revocation
```
### 3. Sending a JWT back to the client
  - sending the token back to the user several different ways
    ```
    • In a Cookie
    • In the Request Body
    • In a plain HTTP Header
    ```
  - JWTs vs Cookies
    ```
    two very different concepts. 
    Cookies are a browser data storage mechanism, a place where we can safely store a small amount of data.
    That data could be anything such as for example the user preferred language, 
    but it can also contain a user identification token such as for example a JWT.
    ```
  - store a JWT Session Token 
    - Cookies vs Local Storage
    ```
    // this is the session token we created above
    const jwtBearerToken = jwt.sign(...);
    // set it in an HTTP Only + Secure Cookie
    res.cookie("SESSIONID", jwtBearerToken, {httpOnly:true, secure:true});
    ```
    ```
    A unique aspect of cookies is that 
    the browser will automatically with each request append the cookies for a particular domain 
    or sub-domain to the headers of the HTTP request.
    This means that if we store the JWT in a cookie, 
    we will not need any further client logic for sending back the cookie 
    to the application server with each request, 
    assuming the login page and the application share the same root domain.
    
    Besides setting a cookie with the JWT value, we also set a couple of security properties 
    such as : Unique security properties of Cookies - HttpOnly and Secure Flags 
    ```
    -  A Cookie can be marked 1) HttpOnly and 2) Secure (*** used together for maximum security)
    ```
    A Cookie can be marked as Secure, meaning that the browser 
    will only append the cookie to the request if it's being made over an HTTPS connection.
   
    Cookie can also be marked as Http Only 
    meaning that it's not accessible by the Javascript code at all! 
    Note that the browser will still append the cookie to each request sent back to the server, 
    just like with any other cookie.
    
    The two flags Secure and Http Only can and are often used together for maximum security,
    ```
    - Advantages of HTTP Only cookies
      - avoid XSS ``` XSS : a script injection attack```
    - Disadvantages of Cookies - XSRF/ CSRF (Cross-Site Request Forgery)
    ```
    we choose to store our JWT in a cookie then we need to also put in place some defenses against XSRF.
    ```
    - Cookies and Third-Party Authentication providers
    ```
    A potential problem with receiving the session JWT in a cookie is
    that we would not be able to receive it from a third-party web domain, 
    that handles the authentication logic.
    
    an application running on app.example.com cannot access cookies 
    from another domain like security-provider.com.
    ```
### 4. Storing and using the JWT on the client side
  - Checking User Expiration
### 5. Sending The JWT back to the server on each request
  - build an Authentication HTTP Interceptor
### 6. Validating User Requests
 - Building a custom Express middleware for JWT validation
 - Configuring a JWT validation middleware using express-jwt
 - Validating JWT Signatures - RS256
### 7. JWKS (JSON Web Key Set) endpoints and key rotation
### RS256 vs HS256