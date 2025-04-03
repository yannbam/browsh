# Browsh Development Guide

## Build Commands
- Build webext (dev): `./ctl.sh build_webextension_watch`
- Build webext (prod): `./ctl.sh build_webextension_production`
- Build Go binary: `./ctl.sh build_browsh_binary /path/to/interfacer`

## Build Process Workarounds
The build process described in SETUP_DEV_LINUX.md is outdated. Here's the working process:

### Web Extension Build Process
1. Install dependencies: 
   ```
   cd ~/src/browsh/webext
   npm install
   ```

2. Build with webpack: 
   ```
   npx webpack
   ```

3. Run extension in Firefox:
   ```
   cd dist
   web-ext run --verbose
   ```

4. Build XPI file:
   ```
   cd dist
   web-ext build
   ```
   This creates the XPI in `web-ext-artifacts` directory.

### go-bindata Installation
The `get-gobindata` script mentioned in SETUP_DEV_LINUX.md was removed. Instead, use:
```
go install github.com/kevinburke/go-bindata@latest
```

### Embedding XPI in Go Binary
1. Copy XPI file to the right location:
   ```
   cp ~/src/browsh/webext/dist/web-ext-artifacts/*.xpi ~/src/browsh/interfacer/src/browsh/browsh.xpi
   ```

2. Convert XPI to Go code:
   ```
   go-bindata -o ~/src/browsh/interfacer/src/browsh/webextension.go -pkg browsh ~/src/browsh/interfacer/src/browsh/browsh.xpi
   ```

3. Build the Go binary:
   ```
   cd ~/src/browsh/interfacer
   go build -o browsh cmd/browsh/main.go
   ```

### Build Issues Identified
- The project uses ES modules (import/export syntax)
- The package.json has `"type": "module"` setting
- Firefox extensions traditionally expect CommonJS format
- Webpack bundles these modules for Firefox compatibility
- web-ext must be run from the dist directory, not the webext root

## Test Commands
- Go tests: `cd interfacer && go test ./src/...`
- Single Go test: `cd interfacer && go test -run TestName ./src/browsh`
- JS tests: `cd webext && npm test`

## Lint Commands
- Go lint check: `./ctl.sh golang_lint_check`
- Go lint fix: `./ctl.sh golang_lint_fix`
- JS lint: `cd webext && npm run lint`
- JS formatting fix: `./ctl.sh prettier_fix`

## Code Style Guidelines
- Go: Use camelCase for private variables/methods, PascalCase for exports
- JS: 2-space indentation, camelCase naming, prefix private members with underscore (_)
- Imports: Sort by standard library first, then third-party packages
- Error handling: Go uses `if err != nil` pattern, JS uses try/catch with logging
- Formatting: Go uses gofmt, JS uses Prettier
- Tests: Go uses Ginkgo/Gomega, JS uses Mocha/Chai