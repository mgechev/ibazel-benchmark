# tsc-wrapped profiling

This repository provides a sample playground which can be used for profiling of ibazel with `tsc-wrapped`.

## Setup

```
git clone git@github.com:mgechev/ibazel-benchmark
cd ibazel-benchmark && yarn
npm i -g @bazel/ibazel
```

## Profiling

Generate a project with 1000 files using:

```
npm run generate-1000
```

This command will generate a single Bazel package with 1000 TypeScript modules. 999 of these modules will be imported by `src/index.ts`.

#### Profiling `tsc_wrapped`

You can find `tsc_wrapped.js` under: `bazel-out/host/bin/external/build_bazel_rules_typescript/@bazel/typescript/tsc_wrapped.runfiles/__main__/external/npm/node_modules/@bazel/typescript/internal/tsc_wrapped/`.

To enable debug mode in `tsc_wrapped`, set the `DEBUG` flag in `worker.js` to `true`. This way `tsc_wrapped` will print all the cache hits (both program and file).

The compilation of the package happens in `runFromOptions`. Using `const time = Date.now(); ... console.error(Date.now() - time);` you can profile how long the individual statements of the function take to execute.

**For better accuracy you can use `const { performance } = require('perf_hooks'); performance.now()`**. `perf_hooks` was introduced by Node v8.5.0.

Once you've added the necessary time markers, to start an incremental build run:

```
ibazel build --strategy=TypeScriptCompile=worker :app
```

Update any file in `src/**/*.ts` or `src/*.ts`.

The measurements from `tsc_wrapped.js` will be printed on the screen.

#### Profiling `tsc`

Run:

```
tsc -w
```

Update any file in `src/**/*.ts` or `src/*.ts`.

## License

MIT

