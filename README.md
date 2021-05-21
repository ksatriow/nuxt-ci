# NuxCIGitAc (Nuxt Continuous Integration Github Action) 

## Task

- First we need to setup Node.js.
- Then let's install our npm dependencies
- Check ESLint and Unit tests

## Step

1. Event Github Action
   ``` yaml
    # YAML
      on:
      push:
         branches: [ master ]
      pull_request:
         branches: [ master, develop ]
   ```
2. Integration
   ``` yaml
    # YAML
      integration:
         strategy:
            matrix:
            os: [ubuntu-latest, macos-latest, windows-latest]
            node: [12]
         runs-on: ${{ matrix.os }}
   ```
3. Setup Node.js Environment
   ``` yaml
    # YAML
      - name: Setup Node.js 
        uses: actions/setup-node@v1.4.3
        with:
          node-version: ${{ matrix.node }}
   ```
4. Cache node_modules
   ``` yaml
    # YAML
      - name: Cache node_modules
            uses: actions/cache@v2.1.0
            with:
               # A list of files, directories, and wildcard patterns to cache and restore
               path: node_modules
               # An explicit key for restoring and saving the cache
               key: ${{ matrix.os }}-node-v${{ matrix.node }}-deps-${{ hashFiles(format('{0}{1}', github.workspace, '/package-lock.json')) }}

   ```
5. Install dependencies
   ``` yaml
    # YAML
      - name: Install dependencies
        if: steps.cache.outputs.cache-hit != 'true'
        run: npm install
   ```
6. Run ESLint
   ``` yaml
    # YAML
      - name: Run ESLint
        run: npm run lint
   ```
7. Run Unit Tests
   ``` yaml
    # YAML
      - name: Run unit tests
        run: npm run test:unit
   ```
8. Code Coverage
   ``` yaml
    # YAML
      - name: Code coverage
        uses: codecov/codecov-action@v1.0.12
   ```
