This is a demonstration of a bug in Next.js CLI that affects both Turborepo and Nx (and potentially other tooling). The `SIGTERM` received by `next build` results in exit code `0`, which is treated as successful by Turborepo/Nx, thus it is cached even though the build is incomplete.

Steps:

1. `npm run build:web`
2. `ps | grep node | grep next | awk '{print $1}' | xargs kill` (in a separate tab)
3. `npm run build:web` (again)

Expected:

The build will run again.

Actual:

The build is read from cache, and the `.next` folder is incomplete, thus `npm run start:web` fails.


## Implications

In some environments, such as CI or Docker, it is possible to receive `SIGTERM` when a process takes too long or reaches memory limit. Next.js CLI should respect this signal and propagate it to the other tools (e.g. Turborepo, Nx, etc.) so they can do the right thing (i.e. not cache).

