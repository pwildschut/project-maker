# BDD Testing

Here is where all the BDD testing for the entire project exists.

## Folder structure

-   [features](./features/) - the executable test files.
-   [step_definitions](./step_definitions/) - the code the feature files will
    run.
-   [fixtures](./fixtures/) - dependencies that can be used in any step
    definition.

## Installation

After installing the project, you will need to install the Playwright browsers
with: `playwright install` (Remember to have your virtual environment activated)

## Quick start

The recipe for creating BDD tests is:

1. Create a feature file (using existing step definitions, if possible)
2. Create any missing step definitions
3. Add any missing data-testid attributes to elements in your source code
4. Run using `pytest`.

Note: If you need to debug the site under test, run `PWDEBUG=1 pytest`, and you
can use Playwright's debugger.

Some reasonable defaults have been set in [pyproject.toml](../pyproject.toml),
however, any options can be set in the terminal when running pytest. See the
[docs](https://playwright.dev/python/docs/test-runners) for possible options, as
well as `pytest --help`.

## Usage

To run only certain tests, use `-m` and the list of tags to run (separated with
`and`).

```bash
pytest -m current
```

This will run any tests decorated with that tag. You can decorate at both the
Feature, or Scenario level.

```gherkin
@current
Feature: ...

@current
Scenario: ...
```

## Data creation

An important part of testing is having the right data to test on. Here, we can
take advantage of having Django as a test runner, and create data as we need it.

Data creation is going to be fairly project-specific, but generally speaking,
you will pick out the specific models that need to be created for your test.
Create them in a given step.

For example, `Given I am a manager in business` could be a step definition that
creates a business, fleet, and any other required models, and a user with a
manager role.

To make this easier, it's important to consider what we're testing. If we are
testing that a manager can see a business settings page, we can use a tool like
`model-bakery` to automatically create the data we want, and only specify that
the user is manager. ie. `baker.make("user", role="manager")`. The business,
fleet and all other required fields are automatically taken care of.

### Data creation recipe

1. Identify the data necessary for the test
2. Create the data in a given step (`model-bakery` for easy creation)
3. Use the given step in feature files

## FAQ

-   Why is the BDD tests inside api/ as opposed to outside the site under test
    if it's acting on both frontend and backend?
    -   While slightly unconventional, having Django as the test runner for the
        E2E tests, means access to the DB to read or update at our convenience.
-   Why use `data-testid` attributes rather than CSS selectors?

    -   CSS classes need to be changeable by devs, but a fixed `data-testid`
        attribute lends itself to stability.

-   What value should I give the `data-testid` attribute?

    -   A good rule of thumb is something human-readable to look good in a
        feature file. Then, you can use it as a variable as is, ie.
        `When I click the {value} button` would look much better with
        `view books` rather than `view_books`, `viewBooks` or
        `input__view_books`.

-   Can I have duplicate `data-testid` values on a page?

    -   Short answer - yes, but don't. Longer answer - try and find a way to
        better describe the element on the page, eg.`view books (header)`. If it
        can't be avoided, then you can always target inside another
        `data-testid`. ie. `When I click the view books button in the header`.

-   Am I allowed to use technical steps like "click the button", as opposed to
    business domain language?

    -   Short answer - yes. Longer answer - Balance the need for quick test
        writing (reusing a small number of technical steps) vs strong
        communication (writing more specific business steps). My advice would be
        to start with the technical steps which are already written, so you can
        quickly create feature files, and then when you have more experience,
        start to encapsulate functionality into more business domain language.
        The ideal is to have purely business domain language in the feature
        file, so that it is completely immune from development changes. ie.
        `When I view my books` vs
        `When I click "account" And I click the "view-my-books" button`.

-   Should unit tests be BDD?

    -   While there's nothing stopping you from doing so, the scope of unit
        tests is too small to be particularly useful. The prevailing theory in
        the BDD world about unit tests is to simply name them starting with
        "should" and you'll almost have BDD there anywhere. eg.
        `"should_raise_error_with_empty_array"`.

-   Should integration tests be BDD?

    -   If it helps. Occasionally, it's useful to have communication with a PM
        if a particular API end point needs to behave strangely. But generally,
        as the scope is smaller, it's not so beneficial.

-   How do I write non-BDD unit tests?

    -   API: Simply make a file beginning with test\_ or ending with \_test, and
        write your test as pytest recommends.

    ```python
    # test_maths.py

    import math

    def test_pow():
        assert math.pow(2, 2) == 4.0
    ```

    See [Getting Started](https://docs.pytest.org/en/8.0.x/getting-started.html)
    for more examples.

## Official Documentation

### Playwright Python

[Documentation](https://playwright.dev/python/docs/intro)

[API Reference](https://playwright.dev/python/docs/api/class-playwright)

### Pytest-BDD

[Documentation](https://pytest-bdd.readthedocs.io/en/stable)

### Pytest-Django

[Documentation](https://pytest-django.readthedocs.io/en/latest)

### Pytest

[Documentation](https://docs.pytest.org/en/8.0.x)