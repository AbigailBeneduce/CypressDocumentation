
# Cypress Documentation
Click on any of the subjects to expand.
<details>
<summary> Installing Cypress </summary>


To install Cypress, follow these steps:

1.  **Install Node.js**: Cypress requires Node.js version 12 or higher. You can download and install Node.js from [here](https://nodejs.org/).

2.  **Initialize Your Project**: If you haven't already initialized your project with npm (Node Package Manager), run the following command in your project's root directory:

```bash
npm init -y
```

This should create a `package.json` file if one doesn't currently exist

3.  **Install Cypress**: In your project's root directory run the command:

```bash
npm install cypress --save-dev
```

This command installs Cypress and adds it to your package.json file under `devDependencies`.

4.  **Verifying Installation**: To verify that `Cypress` has been installed correctly run the command:

```bash
npx cypress open
```

This should open the `Cypress Test Runner`

</details>
  
<details>
<summary> Writing tests </summary>

*Cypress uses Mocha's syntax for organizing tests which involves defining a `test suite` (a collection of tests to test a certain feature/functionality) composed of `test cases` (single units of testing). Here is an example:

```javascript
//Login Suite is defined by using the `describe` function
describe('Login Functionality', () => {

// Define a test case using the `it` function
    it('should display the login page', () => {

        // Visit the login page
        cy.visit('/login');

        // Assert that the login form is visible
        cy.get('form').should('be.visible');
    });

    it('should display an error for invalid login', () => {

        // Visit the login page
        cy.visit('/login');

        // Enter incorrect creds
        cy.get('input[name="username"]').type('invalid_user');
        cy.get('input[name="password"]').type('wrong_password');
        cy.get('button[type="submit"]').click();

        // Assert that an error message was displayed
        cy.contains('Invalid username or password').should('be.visible');
    });

    it('should successfully log in with valid credentials', () => {

        // Visit the login page
        cy.visit('/login');

        // Enter correct creds
        cy.get('input[name="username"]').type('valid_user');
        cy.get('input[name="password"]').type('correct_password');
        cy.get('button[type="submit"]').click();

        // Assert that the user is redirected to the dashboard
        cy.url().should('include', '/dashboard');
        cy.contains('Welcome, valid_user').should('be.visible');
    });
});
```
</details>

<details>
<summary> Assertions </summary>

### Assertions

Assertions are used in Cypress to verify that the app is in a state that is expected. Here are some basic assertions:

* Check if it an element (the `alert`) contains a certain piece of text (`Success`). This is case-sensitive:

```javascript
cy.get('.alert').should('contain', 'Success');
```

* Check if an element (`nav`) is visible:
```javascript
cy.get('.nav').should('be.visible');
```

* Check if an element is is enabled or disabled:
```javascript
cy.get('input').should('be.enabled');
cy.get('input').should('be.disabled');
```

For more information visit the [documentation](#documentation)

</details>

<details>
<summary> Fixtures and Data </summary>

## Fixtures and Data

Fixtures are external pieces of static data that can be used in tests. These are typically stored within a JSON file and can do things like 
- Initialize the state of the application
- Populate form fields
- Stub (mock) network responses

### Adding Fixtures

To add a fixture to Cypress:

1. **Create a fixtures folder**: If it doesn't exist, create a folder called `fixtures` inside the `cypress directory`

2. **Add a JSON File**: Create a JSON file in the `fixtures` folder. This will contain all the data that you want to use in the tests. For example:
```javascript
{
    username: "user",
    password: "password"
}
```

3. **Load the fixture data**: First use `cy.fixture()` to load the data:
```javascript
beforeEach(() => {
  cy.fixture('user').as('userData');
});
```

Because of the `beforeEach` this will run before each test case and load the fixture data from `user.json` aliased as `userData`.

4. **Run the test case**: Now that your fixture is loaded, you can use it in your test. Example:
```javascript
it('should log in with valid credentials from fixture', function() {
    // Test code that would go above this

    cy.get('input[name="username"]').type(this.userData.username); // Enter username from fixture
    cy.get('input[name="password"]').type(this.userData.password); // Enter password from fixture
    cy.get('button[type="submit"]').click(); /

    //Test code below this
})
```
</details>

<details>
<summary> Custom Commands </summary>

## Custom Commands

Commands in Cypress let you create functions that can be reused in multiple tests.
This is ideal for a few reasons:
- Avoid repetitively writing code
- Allows you to update something across ALL tests when updates occur
- Makes tests more readable
- Easier to debug
- Etc.

Use them!

### Creating Custom Commands

Customs commands imported by your `e2e.js` file located in the `cypress/support`. This is done like so:
```javascript
import './commands'
import './loginCommands'
```
This will import the files `commands.js` and `loginCommands.js` into your files.
To access the methods within these commands you can add `import '@testing-library/cypress/add-commands'` to the top of your test file.

To actually add a custom command you can use the `Cypress.Commands.add()` command like so:
```javascript
Cypress.Commands.add('login', (username, password) => {
  cy.visit('/login');
  cy.get('input[name="username"]').type(username);
  cy.get('input[name="password"]').type(password);
  cy.get('button[type="submit"]').click();
});
```

### Using Custom Commands

Once you've defined a custom command you can use it in any test file like so:
```javascript
it('should log in and access dashboard', () => {
  cy.login('testuser', 'password123');
  cy.url().should('include', '/dashboard');
});
```

This `cy.login()` call will access the Cypress command we added earlier and run that code.

### Overwriting Commands

Cypress has many built-in commands that you can overwrite to use your own logic using `overwrite()`. A good example is here:
```javascript
Cypress.Commands.overwrite('visit', (originalFn, url, options) => {
  console.log(`Visiting page: ${url}`);
  // Call the original function with the original arguments
  return originalFn(url, options);
});
```

By doing this, instead of using Cypress' normal `overwrite` logic when called, it will use the logic in this command instead!

### Best Practices for Custom Commands

1. **Keep Commands Simple**: Commands should only do one thing and do it well.

2. **Use Parameters**: Make commands flexible by adding parameters. An example is here:
```javascript
Cypress.Commands.add('verifyErrorMessage', (errorMessage) => {
    cy.get('.error-container')
        .should('be.visible')
        .and('contain', errorMessage);;
});
```
This will allow us to use it for many different error messages.

3. **Return the Subject**: If a command is being chained, please return the subject like so:
```javascript
Cypress.Commands.add('clickButton', (buttonText) => {
    return cy.contains('button', buttonText).click();
});
```

4. **Add Type Definitions**: Since we're using TypeScript include the type definition for easier debugging, please!

</details>

## Best Practices

This article, from Cypress, explains it far better than I can. You read it [here](https://docs.cypress.io/guides/references/best-practices). 

It's very concise and I suggest reading it before writing any tests.

## Documentation

Documentation for Cypress can be found [here](https://docs.cypress.io/guides/overview/why-cypress).

They cover FAQ [here](https://docs.cypress.io/faq/questions/using-cypress-faq).

## Additional Resources

Test Automation University has a small course with videos on Cypress [here](https://testautomationu.applitools.com/cypress-getting-started/chapter8.html).

Cypress blog and community can be found [here](https://www.cypress.io/blog/category/Community).

Cypress on Github can be found [here](https://github.com/cypress-io/cypress).
