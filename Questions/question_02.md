- What is middleware ?
  - Middleware is a code block or set of instructions that runs before a request reaches the final request handler.
  - It can modify the request/response, perform authentication, logging, validation, or handle errors.
  - Middleware can be chained, meaning multiple middleware functions can run sequentially before the request reaches the route handler.

- Middleware and modification of request/response.
  - Request (req) object
    - Middleware can modify the request before it reaches the route handler.
    - Example: add properties, parse JSON, attach user info after authentication.

```
app.use((req, res, next) => {
  req.user = { id: 1, name: 'John' };
  next();
});

```

- Response (res) object
  - Middleware usually does not send the final response, but it can modify or prepare the response object.
  - Example: set headers, cookies, or status before the route handler sends the response.

```
app.use((req, res, next) => {
  res.setHeader('X-Powered-By', 'Node.js');
  next();
});

```

- Sending the response
  Some middleware can terminate the request and send a response (like authentication failure, error handling).
  Example:

```
app.use((req, res, next) => {
  if (!req.headers['auth-token']) {
    return res.status(401).send('Unauthorized');
  }
  next();
});

```

- Directory structure in node js project

```

my-express-app/
│
├── node_modules/ # Installed packages
├── package.json # Project metadata, dependencies, scripts
├── package-lock.json
├── .env # Environment variables
├── server.js / app.js # Entry point of the application
│
├── config/ # Configuration files
│ └── db.js # Database configuration
│ └── config.js # Other app-wide config
│
├── controllers/ # Route handler logic
│ └── userController.js
│
├── routes/ # Route definitions
│ └── userRoutes.js
│
├── models/ # Database models / schemas
│ └── userModel.js
│
├── middleware/ # Custom middleware
│ └── authMiddleware.js
│
├── utils/ # Helper functions / utilities
│ └── logger.js
│
├── services/ # Business logic, external API calls
│ └── userService.js
│
├── public/ # Static files (images, CSS, JS)
│
├── views/ # Template files (if using EJS, Pug, etc.)
│
└── tests/ # Unit / integration tests
└── user.test.js
```

- Tips for scalability:
  - Separate services from controllers → keeps route handlers clean.
  - Group related files in feature folders (optional for very large apps):
    ```
    features/
    user/
    userController.js
    userRoutes.js
    userModel.js
    userService.js
    ```
  - Use middleware consistently for authentication, error handling, logging.
  - Keep config and environment variables separate for easier deployment.

- How many types of middleware in node js ?
  - Middleware can be classified based on where and how it’s applied.
  - Application-level (Global) Middleware
    - Applied to the entire application using app.use() or app.METHOD().
    - Runs for all routes (or all routes matching a path).
    - Example:

    ```
        const express = require('express');
        const app = express();

        // Global middleware
        app.use((req, res, next) => {
        console.log('Time:', Date.now());
        next();
        });

        app.get('/', (req, res) => {
        res.send('Home Page');
        });

    ```

  - Applied to specific routes or route groups using express.Router().
    - Useful for grouping routes with common logic.
    - Example:

    ```
        const express = require('express');
        const router = express.Router();

        // Router-level middleware
        router.use((req, res, next) => {
        console.log('Router middleware');
        next();
        });

        router.get('/user', (req, res) => {
        res.send('User Page');
        });

        app.use('/api', router); // Apply router
    ```

  - Additional Middleware Types (Optional / Common in Express)
    - Error-handling middleware → Defined with 4 parameters (err, req, res, next); catches errors.
    - Built-in middleware → Provided by Express (express.json(), express.static()).
    - Third-party middleware → Provided by npm packages (e.g., cors, helmet, morgan).
  - Key Points
    - All route handlers (app.get, app.post, router.get, etc.) are middleware.
    - They can be chained with other middleware functions.
    - The difference between route handler and middleware:
    - Middleware may or may not send a response
    - Route handler usually sends the final response
