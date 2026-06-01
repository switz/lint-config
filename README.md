# switz's eslint-config

## Installation

```
$ yarn add -D eslint @switz/eslint-config
# or
$ pnpm install -D eslint @switz/eslint-config
```

Update your eslint config (`eslint.config.mjs`)

```js
import main from '@switz/eslint-config';

export default [...main];
```

If you need React support, use

```js
import main from '@switz/eslint-config';
import react from '@switz/eslint-config/react.mjs';

export default [...main, ...react];
```

If you need mdx support, use

```js
import main from '@switz/eslint-config';
import mdx from '@switz/eslint-config/mdx.mjs';

export default [...main, ...mdx];
```

If you need tailwind support, use:

```js
import main from '@switz/eslint-config/main.mjs';
import react from '@switz/eslint-config/react.mjs';
import tailwind from '@switz/eslint-config/tailwind.mjs';

export default [...main, ...react, ...tailwind];
```

You should be able to combine configs just by spreading more into the array.

## Oxlint + Oxfmt (Alternative)

This package also ships oxlint and oxfmt configs as a faster alternative to ESLint + Prettier.

### Setup

Install oxlint and oxfmt, then reference the configs:

```bash
pnpm install -D oxlint oxfmt
```

For Tailwind support, also install the native oxlint plugin:

```bash
pnpm install -D oxlint-tailwindcss
```

Create an `oxlint.json` in your project root that extends the base config:

```json
{
  "$schema": "https://raw.githubusercontent.com/oxc-project/oxc/main/npm/oxlint/configuration_schema.json",
  "extends": ["@switz/eslint-config/oxlint"]
}
```

For React, add the react config:

```json
{
  "$schema": "https://raw.githubusercontent.com/oxc-project/oxc/main/npm/oxlint/configuration_schema.json",
  "extends": [
    "@switz/eslint-config/oxlint",
    "@switz/eslint-config/oxlint/react"
  ]
}
```

For Tailwind, add the tailwind config:

```json
{
  "$schema": "https://raw.githubusercontent.com/oxc-project/oxc/main/npm/oxlint/configuration_schema.json",
  "extends": [
    "@switz/eslint-config/oxlint",
    "@switz/eslint-config/oxlint/react",
    "@switz/eslint-config/oxlint/tailwind"
  ]
}
```

Then run:

```bash
oxlint -c oxlint.json
```

**Formatting** — copy `.oxfmtrc.json` to your project root:

```bash
cp node_modules/@switz/eslint-config/.oxfmtrc.json .oxfmtrc.json
oxfmt .
```

Add scripts to your `package.json`:

```json
{
  "scripts": {
    "lint:ox": "oxlint -c oxlint.json",
    "fmt:ox": "oxfmt ."
  }
}
```

### Tailwind

The Tailwind oxlint config uses [oxlint-tailwindcss](https://github.com/sergioazoc/oxlint-tailwindcss), a native oxlint plugin. It requires oxlint >= 1.43.0 and Tailwind CSS v4. The plugin auto-detects your Tailwind entry point, but you can override it in your `oxlint.json`:

```json
{
  "settings": {
    "tailwindcss": {
      "entryPoint": "src/styles/globals.css"
    }
  }
}
```

### Gaps vs ESLint configs

- **React**: oxlint covers ~20 of 80+ `eslint-plugin-react` rules. Core rules like `jsx-key`, `no-direct-mutation-state`, hooks rules, and `jsx-no-duplicate-props` are covered. Rules like `no-unstable-nested-components`, `no-array-index-key`, `jsx-handler-names` are not.
- **Tailwind**: The oxlint config uses `oxlint-tailwindcss` (native plugin) while the ESLint config uses `eslint-plugin-better-tailwindcss`. Both provide equivalent coverage for class sorting, duplicates, conflicts, and whitespace.
- **MDX**: oxfmt formats `.mdx` files, but does not lint embedded code blocks like `eslint-plugin-mdx` does.
- **TypeScript type-aware rules**: oxlint's type-aware checking is in alpha (~73% coverage of `typescript-eslint` recommended rules).

## Reference

https://eslint.org/docs/developer-guide/shareable-configs

## License

MIT, have fun
