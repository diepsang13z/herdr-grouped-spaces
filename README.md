# Grouped Spaces

Group Herdr spaces like folders, right in the sidebar, while the rest of
Herdr's window stays as shipped.

![Grouped Spaces](preview.png)

Herdr's spaces list, with manual groups: fold a group, drag spaces in and out,
rename it, ungroup it, or close the group together with every space in it.
Groups can sit above, between, or below loose spaces.

## Features

- **Manual groups:** assign a space from its right-click menu; pick an existing
  group or create a new one. No rules or path matching required.
- **Add by drag:** drop a space on a group header to add it; new members join
  at the end of the group.
- **Remove by drag or menu:** drop a member on a loose space row or on the
  empty area below the list, or use `Remove from group`.
- **Foldable headers:** click a group header to fold or unfold it. The folded
  state survives restarts.
- **Auto-unfold:** moving to a space inside a folded group opens that group.
- **Group order:** a group sits where its anchor space sits, so loose spaces
  and groups can be interleaved. Drag the header to move the group.
- **Manage groups:** right-click a header to rename the group, ungroup all its
  spaces, or delete the group and close every space in it (with a confirmation).

## Install

Copy these into `~/.config/herdr-app`:

```
layouts/grouped-spaces.json
layouts/grouped-spaces.svg
mods/grouped-spaces/
mods/grouped-spaces-menu/
```

Then switch to it:

```sh
herdr-app do layout.switch '{"name":"grouped-spaces"}'
```

Everything else in the layout is Herdr's shipped UI. No keys are required.
The mods use Herdr's `exec` for `git` and `hc machine ls` (inherited from
Herdr's spaces and menu mods) to show git and machine state; they make no
network requests of their own.

Tested with Herdr 0.1.15.

## Use

- Right-click a space -> **Group…** -> choose `No group`, an existing group, or
  **New group…**.
- Right-click a group header -> **Rename group…**, **Ungroup all**, or
  **Delete group and spaces…**.
- Click a group header to fold it.
- Drag a space onto a group header to add it; drag it onto a loose space or the
  empty space below the list to remove it.
- Drag a group header to move the whole group.

## State and limits

- Groups live in `shared.json` under `space.group` and `space.groupAnchor`.
  Folded groups are kept in the mod's `localStorage`.
- State is local to this Herdr app home: it is not synced to other machines,
  other Herdr clients, or the phone.
- Group membership is by space id. Closing a space leaves a stale id in
  `shared.json`; it is simply not drawn.
- Herdr's worktree auto-nesting can move a space after a group reorder; the
  group remains where its anchor space is.
- **Delete group and spaces** closes the terminals and agents in those spaces.
  There is no undo.
- This is a layout, not a standalone mod: Herdr's shipped spaces list cannot be
  replaced without pointing a layout's `spaces` block at this mod.

## Development

```sh
herdr-app check mods/grouped-spaces
herdr-app check mods/grouped-spaces-menu
```

Development notes are kept outside the repository.

## Credits

Derived from Herdr's shipped `spaces` and `menu` mods (0.1.15 baseline), part of
[herdrdev/herdr-app](https://github.com/herdrdev/herdr-app), Apache-2.0.
See [NOTICE](NOTICE). Original group design and implementation: this package.
