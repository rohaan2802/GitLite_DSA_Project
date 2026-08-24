# GitLite_DSA_Project

Console **mini-Git** for a Data Structures course: a CSV is loaded into an **AVL** or **Red-Black** tree keyed on a chosen column. Each key is persisted as a **`.txt` node file** under a branch folder so the full tree need not stay in RAM. Branches are ordinary directories; history is `commit_history.txt` per folder.

This is **not** Git object hashing (`blob`/`tree`/`commit` SHAs). “Content” is the CSV row plus parent/child metadata written by `Node::create_node_text_file` and `modify_node_txt_file`.

**Team:** Mohammad Rohaan (22I-2327), Ali Bin Salman (22I-0894), M. Bilal Baig (22I-0819)  
**Spec PDF:** `GITLite- Data Structure.pdf`  
**Solution:** `DSA_Project.sln`  
[rohaan2802](https://github.com/rohaan2802)

---

## Table of contents

1. [What this program does](#what-this-program-does)
2. [Architecture](#architecture)
3. [Startup flow (`main.cpp`)](#startup-flow-maincpp)
4. [Commands (`get_command`)](#commands-get_command)
5. [File-by-file](#file-by-file)
6. [Node files and CSV](#node-files-and-csv)
7. [AVL vs Red-Black](#avl-vs-red-black)
8. [Branching, merge, save/load](#branching-merge-saveload)
9. [Data files on disk](#data-files-on-disk)
10. [Build and run](#build-and-run)
11. [Limitations](#limitations)
12. [Author](#author)

---

## What this program does

1. Prompt `init <csv>` (`input_file_name`). Missing `.csv` is appended.
2. Choose tree: `init AVL`, `init B`, or `init RED-BLACK` (`tree_menu`).
3. Pick the **key column** from the CSV header (`show_menu`).
4. Insert every data row into AVL or RB; print the tree; write `Main_Folder/<key>.txt`.
5. Enter Git-like commands: branch, checkout, commit add/update/delete, merge, log, save, load, help.

Default branch name is **`Main_Folder`**. Folders `DSA_Project`, `Branches`, `x64`, `Main_Folder`, and `saved_Repo` cannot be created or deleted as user branches (`compare_char_arrays_ascii` guards).

---

## Architecture

```text
main.cpp
  input_file_name() → tree_menu() → show_menu() → AVL_Main_Implementation
                                               or RB_Main_Implementation
  loop: get_command()
          branch / checkout / commit * / merge / log / save / load / help

Node.h           key + row_data + left/right/parent + color + height
AVL_TREE.h       AVL insert/delete/rotations + print_tree
Red_Black.h      RB insert + case_handle_insertion; delete uses height/balance helpers
File_Reader.h    CLI, filesystem, CSV split, branch copy, save/load
```

C++17 `std::filesystem` (`namespace fs`). Win32: `max_win_size`, `font(24)` (Consolas). Custom `str_len` / `str_cpy` / `i_to_s` / `compare_char_arrays` (no `std::string` for commands).

---

## Startup flow (`main.cpp`)

Comments at top: **22I-2327 / 22I-0894 / 22I-0819**.

```text
label_9:   max_win_size(); font(24); input_file_name();
label_13:  select_tree = tree_menu();          // 0 → back to label_9
label_24:  show_menu(select_col, file_name);   // 0 → back to label_13
           if select_tree == 1 → AVL_Main_Implementation(...)
           if select_tree == 3 → RB_Main_Implementation(...)
           // select_tree == 2 (init B) is not dispatched
           while get_command() != 0: handle 1–14
```

`command_get == 0` returns to column selection (`label_24`). CSV upload `"0"` closes the console (`PostMessage(..., WM_CLOSE)`).

---

## Commands (`get_command`)

Parser in `File_Reader.h`; help text from `display_commands_syntax()`. Return codes as used in `main.cpp`:

| Return | Typed command | `main.cpp` action |
|--------|---------------|-------------------|
| 0 | `0` | Back to column menu |
| 1 | `branch <name>` | `add_branch_to_array` + `txt_file_copies_function` |
| 2 | `checkout <name>` | `str_cpy(current_branch, branch_file)` if folder exists |
| 3 | `log` | `commit_history_display_function(current_branch)` |
| 4 | `commit add` | `add_node_in_AVL_TREE` or `add_node_in_RED_BLACK_TREE` then rebuild/print |
| 5 | `commit update` | `update_node_in_*` then add + rebuild |
| 6 | `branches` | `read_branches_from_file("Branches", ...)` |
| 7 | `delete-branch <name>` | Refuses if name equals **active** branch; else `delete_branch` |
| 8 | `merge <src> <dst>` | `merge_branches` inside `get_command` (copies `.txt`, skips `commit_history.txt`) |
| 9 | `visualize-tree <name>` | Rebuild AVL or RB from CSV and `help_print` |
| 10 | `current-branch` | `show_current_branch` |
| 11 | `save` | `save_repository("Branches", "branches.txt", ...)` |
| 12 | `load <folder>` | `load_repo` (print each node file) |
| 13 | `commit delete` | `delete_node_in_AVL_TREE` / `delete_node_in_RED_BLACK_TREE` |
| 14 | `help` | `display_commands_syntax` |

Every successful command also `add_command_history` (append `- Commit #<n>-> "..."`).

`init B` returns **2** from `tree_menu` but `main` never builds a B-tree.

---

## File-by-file

| File | Role |
|------|------|
| `main.cpp` | Labels, tree dispatch, command `switch` |
| `Node.h` | `enum color_of_nodes { R, B }`; `Node`; `create_node_text_file`; destructor `remove(file_name)` |
| `AVL_TREE.h` | class `AVL`: `insert`, `delete_node`, `left_rotation_function`, `right_rotation_function`, `get_height`, `get_balance_function`, `get_sucessor`, `modify_node_txt_file`, `print_tree` |
| `Red_Black.h` | class `red_black_tree`: `insert`, `case_handle_insertion`, `cmp_str`, `delete_node_in_RB`, `helper_fun_left` / `helper_fun_right`, `print_tree` |
| `File_Reader.h` | All CLI/FS/CSV helpers listed above |
| `DSA_Project.sln` / `.vcxproj` | Visual Studio |
| `GITLite- Data Structure.pdf` | Course specification |
| `book1.csv` | Sample table |

---

## Node files and CSV

`book1.csv` header:

```text
Name,Roll Number,Subject,Marks,Status
```

Example row persisted as `Main_Folder/45.txt` after AVL print (`modify_node_txt_file`):

```text
NODE_DATA--->    45
ROW_DATA--->    Owais,21i-0072,Algo,45,Failed
PARENT_NODE--->    55
LEFT_CHILD--->    nullptr
RIGHT_CHILD--->    nullptr
```

`Node::create_node_text_file` builds `folder\key.txt` (backslash) and, if the file does not yet exist, writes the **raw CSV row** only. After `print_tree` / `help_print`, AVL overwrites with `NODE_DATA` / `ROW_DATA` / `PARENT_NODE` / `LEFT_CHILD` / `RIGHT_CHILD`. RB files also store **R/B** color next to each pointer.

`string_array_function` splits a row on commas into `char**` for the key `columns[select_col - 1]`.

`commit add` prompts one value per header field and **appends a CSV line**; empty fields stay empty. Comments in `add_node_in_AVL_TREE` note that **duplicate keys may land in the CSV but not in the tree**, so no new `.txt` is written.

`commit delete` / `update` ask for the key in the selected column, rewrite the CSV via `temp.csv`, and `fs::remove` `folder/key.txt`.

---

## AVL vs Red-Black

**AVL (`AVL_TREE.h`)**  
BST insert by `strcmp` on the key. Balance = left height − right height. Four rotation cases (LL, RR, LR, RL). Delete uses inorder successor (`get_sucessor`) and rebalances. Height is recomputed recursively in `get_height`.

**Red-Black (`Red_Black.h`)**  
New nodes start **red** (`Node` ctor `c1 = R`); root forced **black** after `case_handle_insertion` (uncle red recolor vs uncle black rotate). Insert is iterative. `delete_node_in_RB` then applies **AVL-style** `get_balance` / `helper_fun_left` / `helper_fun_right` rather than classic RB delete fixup. `cmp_str` exists but insert uses `strcmp`.

Both `print_tree` preorder-walk and refresh node `.txt` files.

---

## Branching, merge, save/load

- `branch <name>`: append name to `Branches/branches.txt`; `txt_file_copies_function` copies all `.txt` from the **current** folder except `commit_history.txt`.
- `checkout`: requires an existing directory; copies the name into `current_branch`.
- `delete-branch`: `fs::remove_all`; rewrite `branches.txt` through `temp_branches.txt`. Cannot delete protected names or a missing folder.
- `merge A B`: copy A’s node files onto B (overwrite).
- `log`: read `current_branch/commit_history.txt` (`/` in the path string).
- `save`: write `saved_Repo/repo_data.txt` with `# Date-->` (`__DATE__`), `# Format Version--> 1.0`, branch list, `Active Branch`, then the **key column** of every CSV row.
- `load <folder>`: iterate `.txt` files, skip `commit_history`, print up to five lines each.

Sample history in `Main_Folder/commit_history.txt`: `branch f1` … `branch f4`.

---

## Data files on disk

```text
GitLite_DSA_Project/
├── main.cpp  Node.h  AVL_TREE.h  Red_Black.h  File_Reader.h
├── book1.csv
├── Main_Folder/          # default branch (keys 45, 55, … 94 + commit_history.txt)
├── Branches/branches.txt # f1 f2 f3 f4
├── f1/ f2/ f3/ f4/       # copied node files (same keys as Main_Folder)
├── saved_Repo/repo_data.txt
├── GITLite- Data Structure.pdf
└── DSA_Project.sln
```

Committed `saved_Repo/repo_data.txt` example: Date **Jan 5 2025**, active **Main_Folder**, keys 85, 90, 78, …

---

## Build and run

**Windows** (console maximize + `CONSOLE_FONT_INFOEX`). C++17 filesystem.

1. Open `DSA_Project.sln`. Set the working directory to the **repo root** (`book1.csv`, `Main_Folder`).
2. Run. Type `init book1` (or `init book1.csv`).
3. Type `init AVL` or `init RED-BLACK`.
4. Choose a column (e.g. Marks). Inspect printed nodes and `Main_Folder/*.txt`.
5. `help`, then `branch f5`, `checkout f5`, `commit add`, `log`, `save`.

```text
init book1.csv
init AVL
# select column
help
branch demo
checkout demo
commit add
log
save
```

---

## Limitations

- **`init B` is a menu stub** — no B-tree class is called from `main`.
- No Git hashes, diffs, or index; merge is **file copy**, not 3-way merge.
- `commit add` updates CSV immediately; tree rebuild uses `folder_name` / `current_branch` inconsistently in a few `main.cpp` calls (e.g. AVL add rebuilds `folder_name` which stays `"Main_Folder"`).
- RB delete is not a textbook RB delete (height/balance helpers).
- Path mix of `\` (node create) and `/` (history).
- `input_file_name` buffer is 15/20 chars; long CSV names truncate.
- Console helpers are Win32-only.

**Extend:** implement B-tree; hash file contents if the PDF requires it; merge conflicts; portable UI.

---

## Author

**Mohammad Rohaan** — 22I-2327  
Ali Bin Salman (22I-0894) · M. Bilal Baig (22I-0819)  
[https://github.com/rohaan2802](https://github.com/rohaan2802)
