# camp-trip-client

For booking trips.

## Miscellaneous Notes

Used node v9.8.0 while developing.

npm script "lint" explicity specifies exit status 0 because eslint produces exit status 1 when it finds lint errors and the npm script runner detects this and reports that the command had an error.

Here is why I'm using Redux and not MobX
- I prefer things that make it easier to reason about state (Redux has one state store, not multiple)
- I prefer good tools and maintainabililty (immutable state, great debug tools)
- I don't care that my app might be pretty simple.  The extra boilerplate of redux doesn't bother me.  I rather keep things maintainable.
See:
- https://codeburst.io/mobx-vs-redux-with-react-a-noobs-comparison-and-questions-382ba340be09
- https://www.robinwieruch.de/redux-mobx-confusion/

## To-Do

- In server.js, require() paths are relative to where things sit in the dist directory, so make them work for env=dev as well
- Build system
  - Setup dev envr
    - watch original sources, not dist/ files
    - debug original sources, not dist/ files
  - Minify
  - clean dist/ dir
  - Could use code splitting to make clientBundle.js and ssrBundle.js share most of the same code.  Not necessar, however, because code splitting is usually for performance reasons.
  - npm uninstall webpack-dev-server

### Low Priority

- Build system
  - eslint
    - .eslintrc.json React recommended settings (see https://github.com/yannickcr/eslint-plugin-react)

### Done

- Build system
  - eslint
    - make it run on files in the root dir
    - no-console rule (make it warn, or maybe remove it altogether)
  - Setup dev envr
    - Hot reloading
    - Setup SSR for prod env
    - delete client/server/shared folder structure
    - Make webpack config support both prod and dev

## Environment and Build System Works Like This...

(`Code` below is pseudocode)

### Production Server Request Handling

- The node server (dist/server.js) handles all requests.
- `app.use(ssrMiddleware)` -- which comes from src/ssrIndex.js AKA dist/ssrBundle.js) -- renders the react for the requested page (react-router chooses what to render based on the URL path) and plugs it into html generated from a template, which it then returns to the client.
- When that html reaches the browser, it has element `<script src="/public/clientBundle.js"></script>` which makes the browser grab dist/public/clientBundle.js (which is generated by webpack.config.js; also which dist/server.js's express.static() is configured to return), which--upon getting to the browser--runs and calls ReactDOM.hydrate to hydrate the existing HTML that was originally put there by ssrBundle.js

### Dev Server Request Handling

- `app.use(webpack-hot-server-middleware)` REPLACES the functionality of app.use(ssrMiddleware)
