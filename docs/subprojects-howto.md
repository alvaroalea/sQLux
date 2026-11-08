# Working with the subproject branches

# English

The work is split in three topic branches, all of them started from `master`:

- `mouse`: mouse emulation (`MOUSE = QIMSI`, `QIMI`, `ICE`, `THOR`).
- `AY`: the QLIONT AY sound chip (`QLIONT_AY`) and the relocation of QSound above RAMTOP.
- `LEVEL2`: level 2 sub-directories on disk images and QXL.WIN, and the related fixes.

`devel` is an integration branch: it only receives merges of the three topic
branches. The topic branches are the source of truth, and nothing is edited
directly in `devel`.

## Creating `devel`

```bash
git checkout -b devel master
git merge --no-ff mouse
git merge --no-ff AY
git merge --no-ff LEVEL2
git config rerere.enabled true   # git remembers how each conflict was solved
```

The expected conflicts are in `memaccess.c`, `src/emulator_init.c`,
`src/emulator_options.c`, `CHANGELOG.md` and `docs/sqlux.md`. They are solved
once in `devel`; with `rerere` they do not have to be solved again in later
merges.

## Changing one of the three and propagating it

```bash
git checkout mouse
# edit, commit
git push
git checkout devel
git merge mouse          # only brings the new commits of that branch
git push
```

Each merge into `devel` only brings what is new in that branch, and
`git log devel --first-parent` shows what came in from each subproject.

## Following what changes in each subproject

- `git log master..mouse` lists the commits that belong to the branch.
- `git diff master...mouse` shows the accumulated diff of the branch.
- `git diff devel mouse` shows if `devel` is behind a branch.

## If you fix something while on `devel`

Do it in the topic branch it belongs to. If it is already committed in
`devel`, `git cherry-pick <commit>` it into that branch and then merge that
branch into `devel` (git sees the change as already applied).

## When master moves

Run `git merge master` in each topic branch (or rebase them if you do not want
merge commits, but that rewrites history and needs `push --force-with-lease`),
and then merge them into `devel` again. Merge is recommended over rebase for
the topic branches, because they are already published.

---

# Español

El trabajo está dividido en tres ramas temáticas, todas creadas desde `master`:

- `mouse`: emulación del ratón (`MOUSE = QIMSI`, `QIMI`, `ICE`, `THOR`).
- `AY`: el chip de sonido AY del QLIONT (`QLIONT_AY`) y el rehubicado de QSound por encima de RAMTOP.
- `LEVEL2`: subdirectorios level 2 en imágenes de disco y QXL.WIN, y los arreglos relacionados.

`devel` es una rama de integración: solo recibe merges de las tres ramas
temáticas. Las ramas temáticas son la fuente de verdad y en `devel` no se
edita nada directamente.

## Crear `devel`

```bash
git checkout -b devel master
git merge --no-ff mouse
git merge --no-ff AY
git merge --no-ff LEVEL2
git config rerere.enabled true   # git recuerda cómo resolviste cada conflicto
```

Los conflictos esperables están en `memaccess.c`, `src/emulator_init.c`,
`src/emulator_options.c`, `CHANGELOG.md` y `docs/sqlux.md`. Se resuelven una
vez en `devel`; con `rerere` no hay que repetirlos en merges posteriores.

## Cambiar una de las tres y propagarlo

```bash
git checkout mouse
# editar, commit
git push
git checkout devel
git merge mouse          # solo trae los commits nuevos de esa rama
git push
```

Cada merge a `devel` solo trae lo nuevo de esa rama, y
`git log devel --first-parent` muestra qué entró de cada subproyecto.

## Controlar qué cambia en cada subproyecto

- `git log master..mouse` lista los commits propios de la rama.
- `git diff master...mouse` muestra el diff acumulado de la rama.
- `git diff devel mouse` te dice si `devel` va por detrás de una rama.

## Si arreglas algo estando en `devel`

Hazlo en la rama temática que le corresponda. Si ya lo has commiteado en
`devel`, haz `git cherry-pick <commit>` en esa rama y luego `git merge` de esa
rama en `devel` (git lo detecta como ya aplicado).

## Cuando master avance

Haz `git merge master` en cada rama temática (o rebase si no quieres commits de
merge, pero eso reescribe la historia y exige `push --force-with-lease`), y
después vuelve a fusionarlas en `devel`. Se recomienda merge y no rebase en las
ramas temáticas, porque ya están publicadas.
