# GitLite_DSA_Project

**GitLite** — a mini Git-inspired console tool that stores repository data in **AVL** or **Red-Black** trees, with each node backed by a text file on disk. Course DSA project by Mohammad Rohaan (22I-2327), Ali Bin Salman (22I-0894), and M. Bilal Baig (22I-0819). Spec PDF: `GITLite- Data Structure.pdf`.

## Overview

Instead of Git objects, GitLite:

1. Reads a tabular input file (e.g. `book1.csv`) and a selected key column  
2. Builds an **AVL** or **Red-Black** tree (`init AVL` / `init RED-BLACK`)  
3. Maps **branches** to folders under `Branches/` / `Main_Folder/`  
4. Persists each tree node as `folder\key.txt` with row + parent/child metadata  
5. Accepts Git-like commands: `branch`, `checkout`, `commit add|update|delete`, `merge`, `log`, `save`, `load`, etc.

This keeps large trees out of RAM by loading node files on demand (`Node::create_node_text_file`).

## Features

- Tree backends: **AVL** (`AVL_TREE.h`) and **Red-Black** (`Red_Black.h`) sharing `Node.h`  
- File helpers & CLI parsing in `File_Reader.h` (`get_command`, `tree_menu`, `save_repository`, `load_repo`, merge, history)  
- Commands (see `display_commands_syntax()` / `help`):

| Command | Action |
|---------|--------|
| `init AVL` / `init RED-BLACK` | Choose tree type |
| `branch <name>` | Create branch folder + copy |
| `checkout <name>` | Switch active branch |
| `commit add` | Insert node |
| `commit update` | Update node |
| `commit delete` | Delete node |
| `branches` | List branches |
| `delete-branch <name>` | Remove branch (not if active) |
| `merge <src> <dst>` | Merge branch folders |
| `visualize-tree <name>` | Rebuild/print tree view |
| `log` | Show `commit_history.txt` |
| `current-branch` | Print active branch |
| `save` | Write `saved_Repo/repo_data.txt` |
| `load <name>` | Load saved repo data |
| `help` | Print syntax |

- Sample data: `book1.csv`, folders `f1`–`f4`, `Main_Folder`, `Branches`, `saved_Repo`  
- Console UX helpers: maximized window, custom font size  

## Tech stack

| Component | Technology |
|-----------|------------|
| Language | C++ |
| Structures | AVL, Red-Black trees |
| FS | `<filesystem>`-style directory walks in `File_Reader.h` |
| IDE | `DSA_Project.sln` |

## Project structure

```
GitLite_DSA_Project/
├── main.cpp                 # Command loop
├── Node.h                   # Tree node + per-node .txt files
├── AVL_TREE.h
├── Red_Black.h
├── File_Reader.h            # I/O, menus, GitLite commands
├── book1.csv
├── Main_Folder/             # Default branch content (*.txt nodes)
├── Branches/                # branches.txt + branch folders
├── saved_Repo/              # Saved repository snapshot
├── f1 … f4                  # Extra sample folders
├── GITLite- Data Structure.pdf
└── DSA_Project.sln / .vcxproj
```

## How to build / run

1. Open `DSA_Project.sln` in Visual Studio.  
2. Build (C++17 recommended for filesystem APIs used in headers).  
3. Run with working directory = project root so `Main_Folder`, `Branches`, and CSV paths resolve.

## Usage

1. Start the app → enter/select input file name (CSV).  
2. Choose tree via `init AVL` or `init RED-BLACK` (menu-driven).  
3. Select the column used as the tree key.  
4. Issue commands at the prompt (`commit add`, `branch feature`, `checkout feature`, `visualize-tree Main_Folder`, `log`, `save`, …).  
5. Inspect generated `.txt` node files under the active branch folder and `commit_history.txt`.

## How to extend / modify

- Complete B-tree path if `init B` stub should be fully supported.  
- Improve merge conflict handling between node files.  
- Add hashing or diff output for “commit” semantics closer to real Git.  
- Cross-platform: replace Windows console font/`max_win_size` helpers as needed.

## Author

**Mohammad Rohaan (22I-2327)** · Ali Bin Salman (22I-0894) · M. Bilal Baig (22I-0819)  
GitHub: [rohaan2802](https://github.com/rohaan2802)
