# Express Validator

## Introduction

While building a backend application using Node.js and Express, we receive a lot of data from users. For example, in a blog application, a user may enter a title, content, email, username, or password. We cannot always trust that the user will enter the correct data.

This is where **Express Validator** is useful. Express Validator is a library used with Express.js to validate and sanitize user input. It helps us check whether the data received from the client is correct before processing or storing it in the database.

## What is Express Validator?

Express Validator is a set of middleware that provides validation and sanitization functions for Express applications.

It allows us to check things like:

* Whether a field is empty or not
* Whether an email is valid
* Minimum or maximum length of a string
* Whether a value is an integer
* Whether two fields have the same value
* Whether the input matches a particular format

For example, if a blog website requires a title, we can check that the title is not empty before creating the blog.

## Installing Express Validator

First, we need to install the package in our Node.js project.

```bash
pnpm add express-validator
```

After installing it, we can import the required functions from the package.

```javascript
import { body, validationResult } from "express-validator";
```

Here, `body()` is used to validate data coming from `req.body`, and `validationResult()` is used to get the validation errors.

## Basic Example

Suppose we have a blog API where the user has to provide a title and content.

```javascript
import { body, validationResult } from "express-validator";

app.post(
  "/blog",
  body("title")
    .notEmpty()
    .withMessage("Title is required"),

  (req, res) => {
    const errors = validationResult(req);

    if (!errors.isEmpty()) {
      return res.status(400).json({
        errors: errors.array()
      });
    }

    res.json({
      message: "Blog created successfully"
    });
  }
);
```

In this example, `.notEmpty()` checks whether the title has been provided. If the user does not provide a title, an error is generated.

The `validationResult(req)` function collects the errors, and `errors.array()` converts them into an array so that we can send them in the response.

## Different Validation Methods

Express Validator provides many methods for validating data.

### 1. notEmpty()

It checks whether a field is empty.

```javascript
body("title")
  .notEmpty()
  .withMessage("Title is required")
```

If the title is empty, the validation will fail.

### 2. isEmail()

It checks whether the value is a valid email address.

```javascript
body("email")
  .isEmail()
  .withMessage("Enter a valid email")
```

For example:

```text
abc@gmail.com
```

is considered a valid email format.

### 3. isLength()

It can be used to check the length of a value.

```javascript
body("title")
  .isLength({ min: 5 })
  .withMessage("Title must contain at least 5 characters")
```

This makes sure that the title has at least 5 characters.

### 4. isInt()

It checks whether a value is an integer.

```javascript
body("age")
  .isInt()
  .withMessage("Age must be a number")
```

### 5. isURL()

It checks whether a value is a valid URL.

```javascript
body("website")
  .isURL()
  .withMessage("Enter a valid URL")
```

## Using Express Validator in a Blog Application

Express Validator can be very useful in a blog application.

For example, when creating a blog, we can validate the title and content.

```javascript
const validateBlog = [
  body("title")
    .notEmpty()
    .withMessage("Title is required")
    .isLength({ min: 5 })
    .withMessage("Title should have at least 5 characters"),

  body("content")
    .notEmpty()
    .withMessage("Content is required")
    .isLength({ min: 20 })
    .withMessage("Content should have at least 20 characters"),

  (req, res, next) => {
    const errors = validationResult(req);

    if (!errors.isEmpty()) {
      return res.status(400).json({
        errors: errors.array()
      });
    }

    next();
  }
];
```

We can then use this middleware in our route:

```javascript
app.post("/blog", validateBlog, (req, res) => {
  const { title, content } = req.body;

  res.json({
    message: "Blog created successfully",
    title,
    content
  });
});
```

Here, the request first goes through `validateBlog`. If there are errors, the request stops and an error response is returned. If everything is valid, `next()` is called and the request moves to the route handler.

## Sanitization

Express Validator can also be used for sanitizing user input. Sanitization means changing or cleaning the input into a suitable format.

For example, we can trim unnecessary spaces:

```javascript
body("title").trim()
```

We can also convert a value to lowercase:

```javascript
body("email").normalizeEmail()
```

This can help keep the data stored in our database more consistent.

## Why is Express Validator Important?

Express Validator is important because users can send unexpected or incorrect data to our server.

For example, imagine a blog API receives:

```json
{
  "title": "",
  "content": ""
}
```

Without validation, our application might try to save this data to the database.

With Express Validator, we can check the data before it reaches the database and return an appropriate error to the user.

Some advantages are:

1. It helps prevent invalid data from entering the database.
2. It makes APIs more reliable.
3. It provides useful error messages.
4. It can improve the security of an application.
5. It makes input validation easier to manage.
6. It can also sanitize user input.

## Conclusion

Express Validator is a useful library for Express.js applications. It allows developers to validate and sanitize data received from users.

In a blog application, it can be used to validate blog titles, content, user registration details, login information, comments, and other fields.

Instead of directly trusting the data sent by the client, we can validate it first and only continue with the request when the data is in the expected format. Therefore, Express Validator is an important tool for building better and more reliable Express.js applications.
