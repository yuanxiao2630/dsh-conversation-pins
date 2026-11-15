# Publishing to npm

The package is published as **`dsh-conversation-pins`** on the public npm registry.

## One-time setup

```sh
npm login --registry=https://registry.npmjs.org/
npm whoami --registry=https://registry.npmjs.org/
```

The account must have publish rights for the unscoped `dsh-conversation-pins` name. The package uses 2FA; npm will ask for a one-time password or security key during publishing.

> The machine's default registry may be a read-only mirror. Always pass `--registry=https://registry.npmjs.org/` when publishing.

## Publish a version

```sh
cd /Users/johnathanwang/Desktop/dsh-conversation-pins

# Make sure the committed browser bundle is current.
pnpm install
pnpm check
pnpm build
git diff --exit-code -- lib/client.js

# Bump package.json and CHANGELOG.md first when needed.
npm publish --registry=https://registry.npmjs.org/ --access public
```

`npm publish` asks for the 2FA code interactively. If you have a code ready, you can pass it once:

```sh
npm publish --registry=https://registry.npmjs.org/ --access public --otp=123456
```

Do not commit `.npmrc` files containing tokens.

## Verify

```sh
npm view dsh-conversation-pins version --registry=https://registry.npmjs.org/
```

Install it in DSH:

```sh
dsh plugin --profile web add dsh-conversation-pins@0.1.4
```

After the first npm release, `docs/index.json` uses the npm version source:

```json
"source": {
  "type": "npm",
  "registry": "https://registry.npmjs.org/"
}
```

Keep the index version and the published version in sync.

## Automated releases

For future releases, a GitHub Actions job can publish with an npm automation token stored as the `NPM_TOKEN` repository secret:

```yaml
- run: npm publish --registry=https://registry.npmjs.org/ --access public
  env:
    NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

Alternatively, configure npm trusted publishing for this repository after the first manual release and use an OIDC workflow. Never put the token in the repository.
