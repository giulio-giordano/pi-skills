# TypeScript / JavaScript reference (dev-workflow)

Read this before writing or modifying TS/JS code.

## Toolchain

Look at: `package.json` scripts, `tsconfig.json`, eslint/prettier/biome config,
and the **lockfile** to know the package manager (`pnpm-lock.yaml`,
`package-lock.json`, `yarn.lock`, `bun.lockb`). Use the project's scripts.

- Typical check: `npx tsc --noEmit`, then the project's `lint` / `format` / `test`.
- If eslint/prettier/tsc are absent, propose adding them **asking first**.

## Types

- `strict: true` where you own the config. **No `any`** - use `unknown` and narrow
  it; no `@ts-ignore` (at most `@ts-expect-error` with a written reason); no `!`
  non-null assertion - prove the value exists with a guard or a fallback.
- Explicit return types on exported and non-obvious functions; let locals infer.
- `interface` for object shapes and contracts, `type` for unions, intersections,
  tuples and aliases.
- Model variants with **discriminated unions**, not several optional flags that
  can contradict each other. No `enum` (literal unions or `as const` maps
  instead), no `namespace`.
- Prefer `readonly`, `as const`, `satisfies`; `?.` and `??` over manual checks;
  validate untrusted data at the boundary with the schema library already in the
  dependencies (do not add one).

## Code style

- ESM only: named exports for utilities (easier to grep and refactor), `import
  type` for type-only imports. `const` by default, never `var`, always `===`.
- Async: never leave a floating promise (await it, return it, or `void` it
  explicitly), never pass an `async` callback to `Array#forEach`, wrap `try/catch`
  and handle `unknown` by narrowing before using it.
- Immutability: `map`/`filter`/spread over in-place mutation; never mutate a
  function argument or a shared array; no `delete` on objects you do not own.
- JSDoc `/** */` on exported functions: summary, `@param`, `@returns`, `@throws`
  when not self-evident. Plain `.js` files: add `// @ts-check` with JSDoc types,
  or ask before migrating the file to TypeScript.
- Split a file past ~300-400 lines, or when it mixes responsibilities.

## Test

- The project's runner (`npm run test`, `npx vitest run`, `npx jest`), added for
  every new behaviour. Test behaviour, not implementation details.
