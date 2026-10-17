# interactive-hardware-interfacing.github.io

The hub page for the organization, served at
https://interactive-hardware-interfacing.github.io/

One static file. To add a tool, add an entry to the `TOOLS` array at the
bottom of `index.html`:

```js
{
  name: 'Tool name',
  repo: 'repo-name',          // becomes the URL: …github.io/repo-name/
  blurb: 'One or two sentences.',
  hardware: 'What you plug in',
  api: 'Web Serial' | 'WebUSB' | 'Pure JS',
  state: 'ready' | 'soon' | 'planned',
}
```

`ready` tools get an **Open tool** button pointing at their project site;
`soon` tools link to their source only; `planned` tools are listed without links.

## Publishing

Settings → Pages → Source: *Deploy from a branch*, branch `main`, folder `/ (root)`.

## Conventions for tool repos

- One tool per repository, one `index.html` at the root (or under `docs/`).
- Relative asset paths only — project sites are served under a subpath.
- A plain-text serial protocol documented in a `PROTOCOL.md`.
- A demo mode that works with no hardware attached.
- Say on the page that Chrome or Edge is required for the hardware link.
