# ESLint Guide

The frontend is linted with ESLint 9 (flat config) and formatted with Prettier. Configuration lives at [frontend/eslint.config.mjs](https://github.com/hackforla/CivicTechJobs/blob/main/frontend/eslint.config.mjs).

## Stack

| Layer | Tool |
|-------|------|
| Linter | ESLint 9 (flat config) |
| Formatter | Prettier |
| Next.js rules | `eslint-config-next` (Core Web Vitals preset) |
| TypeScript rules | `typescript-eslint` |
| React Hooks rules | `eslint-plugin-react-hooks` |
| Accessibility rules | `eslint-plugin-jsx-a11y` |
| Tailwind rules | `eslint-plugin-tailwindcss` (Tailwind 4 compatible) **[Q12, Q16]** |

## Rule highlights

The full rule list is in `eslint.config.mjs`. Notable rules:

**General**

- `no-unused-vars` — warn (TypeScript catches the type-level cases as errors)
- `no-console` — warn (production code shouldn't ship logs)
- `indent: 2` — enforced
- `no-irregular-whitespace` — error

**Prettier**

- `prettier/prettier` — formatting failures are lint errors. Run `npm run format` to fix.

**React / Hooks** **[Q18]**

- `react/no-unescaped-entities` — disabled (too noisy for civic-content text)
- `react-hooks/rules-of-hooks` — error (hooks must run in the right context)
- `react-hooks/exhaustive-deps` — warn (missing dependency arrays)

**TypeScript**

- `@typescript-eslint/no-unused-vars` — error (stricter than the plain JS version)

**Tailwind**

- `tailwindcss/no-contradicting-classname` — error (e.g., `text-left text-right`)
- `tailwindcss/no-unnecessary-arbitrary-value` — error (use theme tokens instead of `[16px]`)
- `tailwindcss/classnames-order` — error (consistent class ordering)

**Accessibility (jsx-a11y)**

- `jsx-a11y/alt-text` — error (`<img>` and `<Image>` must have alt text)
- Plus the rest of `eslint-plugin-jsx-a11y`'s recommended set, inherited from `eslint-config-next`.

## Running

```sh
cd frontend
npm run lint     # Lint all .js/.jsx/.ts/.tsx files; auto-fixes what it can
npm run format   # Format all JS/TS/JSON files via Prettier
```

CI runs `npm run lint` and fails the build on lint errors. **[Q17]**

## Disabling rules

Don't disable rules wholesale in the config — disable at the file or line level when you have a real reason:

```ts
// eslint-disable-next-line @typescript-eslint/no-unused-vars
const _intentionallyUnused = setup();
```

If you find yourself disabling the same rule across many files, that's a signal to revisit the rule itself or the code pattern, not to keep papering over it.

## Recommended VS Code extensions

- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) — surfaces lint errors inline as you type.
- [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) — format-on-save support.

Both should pick up the project config automatically when the editor is opened at the repo root.

## Resources

- [ESLint](https://eslint.org/docs/latest/)
- [Prettier](https://prettier.io/docs/)
- [eslint-config-next](https://nextjs.org/docs/app/api-reference/config/eslint)
- [typescript-eslint](https://typescript-eslint.io/)
