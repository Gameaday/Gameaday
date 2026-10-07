# Migration to the dedicated `home` repo

## Current situation

- Work lives on branch `home-lab-infrastructure` of `Gameaday/Gameaday`, under `home/`.
- A dedicated repo `Gameaday/home` exists (public) and contains one import commit prepared locally.
- **Push is blocked**: the sandbox's GitHub identity (`cline-cloud[bot]`) has no write permission
  on `Gameaday/home` (`push: false` via API).
- Decision: **continue work here; migrate later on a local instance.**

## Why `home/` is a subdirectory here

This repo is the owner's profile/README repo. The home-lab content is kept under `home/` to keep
the profile root clean, and because it will eventually be lifted to the root of the dedicated repo.

## What moves

Everything under `home/` becomes the **root** of the `home` repo (the `home/` prefix is dropped):

```
home/                       ->   (home repo root)
├── README.md               ->   README.md
├── as-is-analysis.md       ->   as-is-analysis.md
├── service-catalog.md      ->   service-catalog.md
├── to-be-architecture-headless.md
├── docs/                   ->   docs/
├── infra/                  ->   infra/
├── playbooks/              ->   playbooks/
├── scripts/                ->   scripts/
└── templates/              ->   templates/
```

Migration artifacts (`home-repo.bundle`, `patches/`) do **not** move; they are temporary.

## Migration options (when ready)

**Option A - restore from bundle (simplest):**
```bash
git clone /path/to/home-repo.bundle home
cd home
git remote set-url origin https://github.com/Gameaday/home.git
git push origin main
```

**Option B - apply the patch:**
```bash
git clone https://github.com/Gameaday/home.git
cd home
git am /path/to/0001-Import-home-lab-infrastructure-as-code-and-documenta.patch
git push origin main
```

**Option C - copy from this working tree:**
```bash
# from a clone of Gameaday/Gameaday on the home-lab-infrastructure branch
cp -r home/* /path/to/home-repo/
# then commit + push from the home repo
```

## After migration

1. Remove `home/` from this repo (leave a pointer in the root README).
2. Drop `home/home-repo.bundle` and `home/patches/`.
3. Point automation (Arcane/n8n) at the dedicated repo.
4. Consider making the `home` repo private once it holds real config (it is public now).
