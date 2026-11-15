# Grouped Spaces

Herdr's spaces list, with manual groups: fold a group, drag spaces in and out,
rename it, ungroup it, or close the group together with every space in it.
Groups can sit above, between, or below loose spaces.

![Grouped Spaces](preview.png)

## What you see

- The sidebar is Herdr's own spaces list (31px "Spaces" header, a 26px `+`,
  git and worktree rows), with the group changes:
  - a group is a 26px header: an 11px chevron (down open, right folded), the
    name in 11px `muted`, and the number of spaces in 10.5px `dim`;
  - its spaces are Herdr's rows, indented 18px, keeping their letter avatars,
    git tail and agent state;
  - loose spaces stay where they are in Herdr's order; a group is drawn where
    its anchor space is, so a group can be above, between, or below loose
    spaces.
- Right-click a space: **Rename**, **Group…**, **Remove from group** (only when
  the space is grouped), the worktree items, and **Close**.
- **Group…** opens a small picker: `No group`, each existing group with a check
  on the current one, a divider, and **New group…**.
- Right-click a group header: **Rename group…**, **Ungroup all**, and a divider
  before **Delete group and spaces…**.
- The group name dialog is 420×230; the delete confirmation is 400×200 and
  shows the group name and `Close N spaces and their terminals?`, with Cancel
  and a red-outlined Delete. There is no extra warning block.

## How it behaves

- **Assign.** Pick a group from the space menu. A new member is moved after the
  group's last member, so it always joins at the end of the group. Creating a
  new group with one space does not move it.
- **Drag in.** Press a space row and release over a group header: the space
  joins that group (at the end). Releasing on a member row of another group
  moves it to that group; releasing on a member row of the same group just
  reorders spaces.
- **Drag out.** Release a member on a loose space row: it leaves the group and
  lands there. Release it on the empty area below the list: it leaves the group
  and stays where it is. `Remove from group` in the space menu does the same
  without moving it.
- **Groups.** Click a header to fold/unfold. Drag a header over a row to move the
  whole group (the anchor space moves); drop it on another header to place it
  after that group; drop it on the empty area below the list to send it to the
  end. Dropping a header on its own members does nothing.
- **Auto-unfold.** Moving to a space inside a folded group opens that group on
  its own, so the shown space is never hidden.
- **Rename.** Renaming a group updates every member. Renaming to an existing
  group name merges the members. The folded state is per name.
- **Ungroup all.** Removes the group; its spaces become loose at their current
  positions.
- **Delete group and spaces…** asks first, then removes the group from shared
  state and runs `space.close {confirmed: true}` for every member. Terminals and
  agents in those spaces close. There is no undo.
- Group state lives in `shared.json` (`space.group` = `{spaceId: groupName}`,
  `space.groupAnchor` = `{groupName: spaceId}`); folded groups are kept in the
  mod's `localStorage`.

## Built from

- **Layout** `layouts/grouped-spaces.json`: Herdr's shipped `main` layout (0.1.15)
  with the `spaces` block's mod set to `grouped-spaces`. The terminal right-click
  menu stays Herdr's shipped `menu`.
- **Mod** `mods/grouped-spaces/`: a clone of Herdr's `spaces` 0.1.15 (git,
  worktree, machine and drag code) with groups added: `assign`, anchor-order
  rendering, `groupHead`, the picker, the name dialog, delete confirmation,
  and the group-aware row drag handlers. It opens `grouped-spaces-menu` for the space
  and group context menus.
- **Mod** `mods/grouped-spaces-menu/`: a clone of Herdr's shipped `menu` 0.1.15 with
  `Group…`, `Remove from group`, `Rename group…`, `Ungroup all` and
  `Delete group and spaces…`; it opens `grouped-spaces` for the picker and
  dialogs.
- **herdr APIs**: `spaces`, `panes`, `run` (`space.move`, `space.close`,
  `space.new`), `shared` / `setShared`, `openOverlay` / `closeOverlay`,
  `InputState` / `Input`, `localStorage`. Programs: `git` and
  `hc machine ls` (inherited from the spaces/menu forks). No network.
  Icons: Herdr's `mods/workspace-chrome/` (check, chevron, folder, minus, plus,
  square-pen, x).
- **Art**: `layouts/grouped-spaces.svg` (layout preview); `preview.png` is the
  same drawing rendered for the gallery.

## Watch out

- This is a layout. Herdr's shipped spaces list cannot be replaced without a
  layout pointing its `spaces` block at `grouped-spaces`.
- The spaces and menu mods are forks of the shipped 0.1.15 versions; rebase
  them on the shipped `spaces`/`menu` when Herdr changes them.
- Group state is local to one Herdr app home. It does not sync across machines,
  clients, or the phone. Remote spaces show under their machine and are grouped
  per machine; `space.move` stays inside a machine.
- Herdr's worktree auto-nesting can override a moved position, but the group
  header still follows its anchor space.
- `Delete group and spaces…` is destructive: it closes terminals and agents
  with no undo.
- Dragging is press-and-release (no native drag and drop between mods); a
  release outside the list does nothing.
- No colors, icons, path rules, keyboard group navigation, or rename-in-place;
  spaces are grouped by name.
- Space ids for closed spaces stay in `shared.json`; they are not drawn.

## Done when

- `herdr-app check` passes for `grouped-spaces` and `grouped-spaces-menu`, and
  `herdr-app do layout.switch '{"name":"grouped-spaces"}'` shows the sidebar with
  the group features.
- Right-click a space → Group… → pick a group: the space joins at the end and
  the header count grows.
- Drag a space onto a header: it joins. Drag a member onto a loose row: it
  leaves. Drag it onto the empty area below the list: it leaves.
- Drag a header down across a loose space: the group moves. Click it: it folds
  and unfolds, and the state survives a restart.
- Right-click a header → Rename group…: every member moves to the new name.
- Right-click a header → Ungroup all: the group disappears and its spaces stay.
- With two scratch spaces and a scratch group, Delete group and spaces… closes
  both spaces and removes the group; the user's other groups are untouched.
- After a restart, groups, members and folded headers are still there.
