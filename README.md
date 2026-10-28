# React Webpack Config

A React 18 + TypeScript starter with a hand-written Webpack 5 config (no CRA, no Vite). The config is written in TypeScript and split into small builder functions under [config/build/](config/build/).

## Features

- **Babel** (`preset-env`, `preset-typescript`, `preset-react`) for TS/TSX
- **Type checking** in a separate process via `fork-ts-checker-webpack-plugin`
- **SCSS modules** — readable class names in dev, hashed in prod; CSS extracted into files in prod
- **SVG as React components** via `@svgr/webpack` (colors converted to `currentColor`)
- **Images** (`png`, `jpg`, `jpeg`, `gif`) as asset modules
- **Code splitting** with `React.lazy` + `Suspense` (see [src/pages/](src/pages/))
- **Routing** with `react-router-dom` v6, with `historyApiFallback` in the dev server
- **`@/` alias** pointing to `src/`
- **`__PLATFORM__` global** (`desktop` | `mobile`) injected with `DefinePlugin`
- **Custom Babel plugin** that strips `data-testid` attributes from production builds
- **Bundle analyzer** on demand

## Getting started

```bash
npm install
npm start          # dev server at http://localhost:3000
```

## Scripts

| Script | What it does |
| --- | --- |
| `npm start` | Dev server with HMR on port 3000 |
| `npm run build:dev` | Development build into `build/` |
| `npm run build:prod` | Production build into `build/` |
| `npm run analyzer` | Production build + bundle analyzer |
| `npm run build:mobile` | Production build with `__PLATFORM__ = "mobile"` |
| `npm run build:desktop` | Production build with `__PLATFORM__ = "desktop"` |

Env options can also be passed straight to webpack:

```bash
npx webpack --env mode=production --env platform=mobile --env analyzer=true
npx webpack serve --env port=4000
```

| Option | Values | Default |
| --- | --- | --- |
| `mode` | `development`, `production` | `development` |
| `port` | number | `3000` |
| `platform` | `desktop`, `mobile` | `desktop` |
| `analyzer` | `true` | off |

## Project structure

```
config/build/
  buildWebpack.ts        # assembles the final config
  buildLoaders.ts        # scss, assets, svg, babel rules
  buildPlugins.ts        # html, define, css extract, analyzer, copy
  buildResolvers.ts      # extensions + "@" alias
  buildDevServer.ts      # dev server settings
  babel/                 # babel-loader + removeDataTestid plugin
  types/types.ts         # BuildOptions, BuildMode, BuildPlatform
public/                  # index.html, favicon, locales (copied in prod)
src/
  index.tsx              # entry + router
  components/App.tsx
  pages/{about,shop}/    # lazy-loaded pages
  global.d.ts            # module declarations + __PLATFORM__
webpack.config.ts        # reads --env and calls buildWebpack
```

## Known quirks

- `npm run analyzer`, `build:mobile` and `build:desktop` pass `--env` through `npm run` without a `--` separator, so npm may swallow the flag. If the option doesn't take effect, call webpack directly as shown above.
- The Babel rule only matches `.tsx` files (`/\.m?tsx$/`); plain `.ts` modules are not transpiled by it.
- `ReactRefreshWebpackPlugin` is registered in production instead of development, so React Fast Refresh is not active in dev (plain HMR still is).
