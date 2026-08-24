# GitLite_DSA_Project

Mini **Git-inspired** console tool: repository data in an **AVL** or **Red-Black** tree, each node persisted as a **`.txt` file** on disk so large trees need not stay fully in RAM.

**Team:** Mohammad Rohaan (22I-2327), Ali Bin Salman (22I-0894), M. Bilal Baig (22I-0819)  
**Spec:** `GITLite- Data Structure.pdf` · Solution: `DSA_Project.sln`  
[rohaan2802](https://github.com/rohaan2802)

---

## Table of contents

1. [Idea](#idea)
2. [Headers](#headers)
3. [Commands](#commands)
4. [Folders on disk](#folders-on-disk)
5. [File_Reader helpers](#file_reader-helpers)
6. [Build and usage](#build-and-usage)

---

## Idea

1. Read a tabular file (e.g. `book1.csv`) and a **key column**.  
2. `init AVL` or `init RED-BLACK`.  
3. **Branches** = folders under `Branches/` / `Main_Folder/`.  
4. Each tree node → `folder\key.txt` with row payload + parent/child metadata (`Node::create_node_text_file`).  
5. Git-like commands: branch, checkout, commit add/update/delete, merge, log, save, load.

`init B` (B-tree) may be stubbed — see spec vs `File_Reader.h`.

---

## Headers

| File | Role |
|------|------|
| `Node.h` | Node + per-node text files |
| `AVL_TREE.h` | AVL insert/delete/balance |
| `Red_Black.h` | RB insert/delete/colors |
| `File_Reader.h` | CLI, FS walks (`std::filesystem`), menus, save/load, merge |
| `main.cpp` | Command loop |

Custom `str_len` / `str_cpy` / `i_to_s` in `File_Reader.h` (C-string helpers). Windows console: maximize, font size helpers (`max_win_size`).

---

## Commands

(`display_commands_syntax()` / `help`)

| Command | Action |
|---------|--------|
| `init AVL` / `init RED-BLACK` | Tree backend |
| `branch <name>` | New branch folder + copy |
| `checkout <name>` | Switch active branch |
| `commit add` | Insert node |
| `commit update` | Update node |
| `commit delete` | Delete node |
| `branches` | List |
| `delete-branch <name>` | Remove (not if active) |
| `merge <src> <dst>` | Merge branch folders |
| `visualize-tree <name>` | Rebuild/print view |
| `log` | `commit_history.txt` (`commit_history_display_function` concatenates `folder/commit_history.txt`) |
| `current-branch` | Print active |
| `save` | `saved_Repo/repo_data.txt` |
| `load <name>` | Restore |
| `help` | Syntax |

---

## Folders on disk

```text
GitLite_DSA_Project/
├── main.cpp  Node.h  AVL_TREE.h  Red_Black.h  File_Reader.h
├── book1.csv
├── Main_Folder/          # default branch node files
├── Branches/             # branches.txt + per-branch dirs
├── saved_Repo/
├── f1 … f4               # extra samples
├── GITLite- Data Structure.pdf
└── DSA_Project.sln
```

---

## File_Reader helpers

- `get_command`, `tree_menu`, `save_repository`, `load_repo`  
- Merge + history  
- `commit_history_display_function(folder_name)` reads `/commit_history.txt` under that folder  

C++17 filesystem recommended.

---

## Build and usage

1. Open `DSA_Project.sln` (VS).  
2. Working directory = project root (`Main_Folder`, CSV paths).  
3. Enter CSV name → `init AVL` or `init RED-BLACK` → pick key column.  
4. Issue commands; inspect `.txt` nodes and `commit_history.txt`.

**Extend:** finish B-tree; better merge conflicts; hashes/diffs closer to Git; portable console (drop Win32 font hacks).

---

## Author

**Mohammad Rohaan (22I-2327)** · Ali Bin Salman (22I-0894) · M. Bilal Baig (22I-0819)  
[rohaan2802](https://github.com/rohaan2802)
