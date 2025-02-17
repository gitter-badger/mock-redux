# 🎭 mock-redux

> 🚧 This repository is still under evaluation!
> We're not sure yet whether this is the approach we want to take.
> If you have opinions, let us know!

[![Code Style: Prettier](https://img.shields.io/badge/code_style-prettier-brightgreen.svg)](https://prettier.io)
![TypeScript: Strict](https://img.shields.io/badge/typescript-strict-brightgreen.svg)
[![NPM version](https://badge.fury.io/js/mock-redux.svg)](http://badge.fury.io/js/mock-redux)
[![Circle CI](https://img.shields.io/circleci/build/github/Codecademy/mock-redux.svg)](https://circleci.com/gh/Codecademy/mock-redux)

Mocks out Redux action and selector hooks for clean Jest unit tests.

Use this package if you'd like your React compnent unit tests to not take dependencies on your Redux states.
Separating your unit tests in this way can be particularly useful if your Redux state is complex and/or shared across components.

## Usage

```js
import { mockRedux } from "mock-redux";
```

`mock-redux` stubs out the [two common Redux hooks](https://react-redux.js.org/api/hooks) used with functional React components.
Call `mockRedux()` before your render/mount logic in each unit test.

> Only function components that call `useDispatch` and/or `useSelector` are supported.
> `connect()` components and `Provider` contexts will throw errors during tests.

### `useSelector`

```tsx
mockRedux()
  .give(simpleSelector, "Always!")
  .giveMock(fancySelector, jest.fn().mockReturnValueOnce("Just the once."));
```

Provide results to the [`useSelector`](https://react-redux.js.org/api/hooks#useselector) function for individual selectors passed to it.
These work similarly to Jest mocks: `.give` takes in the return value that will always be passed to the selector.

```tsx
it("displays the title when there is a title", () => {
  mockRedux().give(selectTicle, "Hooray!");

  const view = render(<Results />);

  view.getByText("Hooray!");
});
```

If you'd like more control over the return values, use `.giveMock` to provide a Jest mock:

```tsx
it("displays a message when the clear title button is clicked", () => {
  mockRedux().giveMock(selectResult, jest.fn().mockReturnValueOnce("First!"));

  const view = render(<Results />);

  act(() => {
    fireEvent.click(view.getByLabelText("Clear Title"));
  });

  view.getByText("No more title.");
});
```

See [Selectors](./docs/Selectors.md) for more documentation or [Heading](./docs/examples/Heading/Heading.test.tsx) for a code example.

### `useDispatch`

```tsx
const { dispatch } = mockRedux();
```

The `dispatch` function provided by [`useDispatch`](https://react-redux.js.org/api/hooks#usedispatch) will be a `jest.fn()` spy.
You can then assert against it as with any Jest mock in your unit tests:

```tsx
it("dispatches the pageLoaded action when rendered", () => {
  const { dispatch } = mockRedux();

  render(<MyComponent />);

  expect(dispatch).toHaveBeenCalledWith(pageLoaded());
});
```

See [Dispatches](./docs/Dispatches.md) for more documentation or [Clicker](./docs/examples/Clicker/Clicker.test.tsx) for a code example.

## Gotchas

- If a selector is passed to `useSelector` without having a `.give` or `.giveMock` previuosly declared for it, `mock-redux` will throw an error.
- The first `mock-redux` import _must_ come before the first `react-redux` import in your test files.

### Hybrid Usage

You don't have to use `mock-redux` in _every_ unit test file in your repository.
Only the test files that import `mock-redux` will have Redux actions stubbed out.

### TypeScript Usage

`mock-redux` is written in TypeScript and generally type safe.

- `.give` return values must match the return types of their selectors.
- `.giveMock` mocks must match the return types of their selectors.

_Heck yes._

## Development

Requires:

- [Node.js](https://nodejs.org) >10 (LTS)
- [Yarn](https://yarnpkg.com/en)

After [forking the repo from GitHub](https://help.github.com/articles/fork-a-repo):

```
git clone https://github.com/<your-name-here>/mock-redux
cd mock-redux
yarn
```

### Contribution Guidelines

We'd love to have you contribute!
Check the [issue tracker](https://github.com/Codecademy/mock-redux/issues) for issues labeled [`accepting prs`](https://github.com/Codecademy/mock-redux/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22accepting+prs%22) to find bug fixes and feature requests the community can work on.
If this is your first time working with this code, the [`good first issue`](https://github.com/Codecademy/guidelines/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22+) label indicates good introductory issues.

Please note that this project is released with a [Contributor Covenant](https://www.contributor-covenant.org).
By participating in this project you agree to abide by its terms.
See [CODE_OF_CONDUCT.md](./CODE_OF_CONDUCT.md).
