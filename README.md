# switz's lint-config

Shared [oxlint](https://oxc.rs/docs/guide/usage/linter) and [oxfmt](https://oxc.rs/docs/guide/usage/formatter) configs.

## Installation

```bash
pnpm install -D oxlint oxfmt @switz/lint-config
```

For Tailwind support, also install the native oxlint plugin:

```bash
pnpm install -D oxlint-tailwindcss
```

## Linting

Create an `oxlint.config.ts` in your project root. oxlint picks it up automatically, and it imports the configs straight from the package, so nothing is copied or hardcoded to a `node_modules` path:

```ts
import { defineConfig } from 'oxlint';
import base from '@switz/lint-config/oxlint' with { type: 'json' };

export default defineConfig({
  ...base,
});
```

Spread `base` rather than putting it in `extends`: oxlint only inherits `rules`, `plugins` and `overrides` from extended configs, so `ignorePatterns` would be lost.

For React, extend the react config:

```ts
import { defineConfig } from 'oxlint';
import base from '@switz/lint-config/oxlint' with { type: 'json' };
import react from '@switz/lint-config/oxlint/react' with { type: 'json' };

export default defineConfig({
  ...base,
  extends: [react],
});
```

For Tailwind, also extend the tailwind config and point it at your Tailwind CSS entry file (required; `settings` are not inherited from extended configs):

```ts
import { defineConfig } from 'oxlint';
import base from '@switz/lint-config/oxlint' with { type: 'json' };
import react from '@switz/lint-config/oxlint/react' with { type: 'json' };
import tailwind from '@switz/lint-config/oxlint/tailwind' with { type: 'json' };

export default defineConfig({
  ...base,
  extends: [react, tailwind],
  settings: {
    tailwindcss: {
      entryPoint: 'src/styles/globals.css',
    },
  },
});
```

Then run:

```bash
oxlint
```

<details>
<summary>Using a JSON config instead</summary>

oxlint's JSON `extends` only accepts file paths, not package names, so reference the files in `node_modules` directly. This breaks if your package manager hoists the package somewhere else (e.g. a monorepo root), and the base `ignorePatterns` are not inherited.

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "extends": [
    "./node_modules/@switz/lint-config/oxlint.json",
    "./node_modules/@switz/lint-config/oxlint.react.json"
  ],
  "ignorePatterns": ["node_modules", "dist", "build", ".next"]
}
```

</details>

## Formatting

Create an `oxfmt.config.ts` in your project root. oxfmt picks it up automatically:

```ts
import config from '@switz/lint-config/oxfmt' with { type: 'json' };

export default config;
```

To override an option, spread it: `export default { ...config, printWidth: 80 };`

Alternatively, skip the config file and pass the path directly: `oxfmt -c node_modules/@switz/lint-config/.oxfmtrc.json`. Symlinking `.oxfmtrc.json` does not work, because oxfmt ignores symlinked config files.

Add scripts to your `package.json`:

```json
{
  "scripts": {
    "lint": "oxlint",
    "fmt": "oxfmt",
    "fmt:check": "oxfmt --check"
  }
}
```

TypeScript config files require a Node version that can run `.ts` natively (22.18+). If your `tsconfig.json` type-checks these files, enable `resolveJsonModule`.

## Tailwind

The Tailwind config uses [oxlint-tailwindcss](https://github.com/sergioazoc/oxlint-tailwindcss), a native oxlint plugin. It requires oxlint >= 1.43.0 and Tailwind CSS v4. Set `settings.tailwindcss.entryPoint` in your own config as shown above.

## Migrating from @switz/eslint-config

This package replaces `@switz/eslint-config`, which is no longer maintained. Remove `eslint`, `prettier` and `@switz/eslint-config`, then follow the setup above. Known gaps vs. the old ESLint configs:

- **React**: oxlint covers ~20 of 80+ `eslint-plugin-react` rules. Core rules like `jsx-key`, `no-direct-mutation-state`, hooks rules, and `jsx-no-duplicate-props` are covered. Rules like `no-unstable-nested-components`, `no-array-index-key`, `jsx-handler-names` are not.
- **MDX**: oxfmt formats `.mdx` files, but does not lint embedded code blocks like `eslint-plugin-mdx` did.
- **TypeScript type-aware rules**: oxlint's type-aware checking is in alpha (~73% coverage of `typescript-eslint` recommended rules).

## License

MIT, have fun
