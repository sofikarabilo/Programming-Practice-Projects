# `ember` — starter skeleton (Lab 1)

Copy this folder to a repository of your own, `git init`, and start from **M1** of
[Lab 01](../lab-01-a-box-of-bytes.md). It builds and runs as-is.

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build
./build/ember
```

## Why a skeleton exists

Lab 1 is about **bytes and types**. It is not about `while` loops, splitting a
string into words, or `std::setw` — you meet those properly in Labs 4, 5 and 7.
So the parts that need them are given to you, fully written and commented. You
read those. You write the four small things that *are* Lab 1.

## Given — read it, don't rewrite it

| File | What it does |
|---|---|
| `CMakeLists.txt` | C++17, `-Wall -Wextra -Werror`, ASan + UBSan on Debug |
| `src/main.cpp` | the prompt: read a line, split it into words, call your functions |
| `src/memory.hpp` | `Byte`, `MEM_SIZE`, `struct Memory` — the box |
| `src/dump.hpp` | the two declarations |
| `src/dump.cpp` → `dump()` | the hex dump loop |

## Yours — four `TODO(lab-01)` markers

```bash
grep -rn "TODO(lab-01)" src/
```

| # | Where | The job |
|---|---|---|
| 1 | `memory.cpp` → `mem_get` | return the byte, or 0 if the address is outside the box |
| 2 | `memory.cpp` → `mem_set` | write the byte, or return `false` if the address is outside |
| 3 | `dump.cpp` → the ASCII gutter | print the character when the byte is printable |
| 4 | `dump.cpp` → `show_byte` | one byte, four views |

When all four are done:

```txt
ember> set 0 65
ember> set 1 66
ember> get 0
65  0x41  0b01000001  'A'
ember> dump
0000  41 42 00 00 00 00 00 00 00 00 00 00 00 00 00 00  |AB..............|
```

That is M2 and M3 of Lab 1. M4 (the three deliberate breakages) is in the lab.

Until you implement `mem_set`, `set` accepts everything and stores nothing, and
`get` prints `show_byte: not implemented yet`. That is the starting state, not a
bug.

## Later labs

You keep this repository for all eight labs. Every lab adds one `else if` branch
to the dispatcher in `main.cpp` and one or two new files next to these.

---

## Українською

Скопіюйте цю теку у свій репозиторій — вона вже збирається й запускається.

Lab 1 — про **байти й типи**, а не про цикли, розбір рядка на слова чи
форматування виводу (це Labs 4, 5, 7). Тому все, що потребує ще не пройденого,
вам **дано** — з коментарями, щоб читати. Ви пишете чотири маленькі речі, які й
є Lab 1: `mem_get`, `mem_set`, ASCII-колонку в дампі та `show_byte`.

Знайти свою роботу: `grep -rn "TODO(lab-01)" src/`.

Якщо C++ бачите вперше — спочатку
[C++ за годину](../cpp-survival-kit.notes.md), потім
[інструменти й git](../setup.notes.md).
## Lab 01 — Results
### 1. Environment
- macOS 13.4
- Apple Silicon (arm64)
- Apple Clang 14.0.3
- CMake 4.4.3
- C++17
#### `sizeof` on my machine

| Type | Size (bytes) |
|---|---:|
| `char` | 1 |
| `short` | 2 |
| `int` | 4 |
| `long` | 8 |
| `long long` | 8 |
| `float` | 4 |
| `double` | 8 |
| `std::uint8_t` | 1 |
| `std::size_t` | 8 |
### 2. Basic checks

- `dump` prints 4096 bytes in hexadecimal and shows an ASCII gutter.
- Printable bytes (`0x20..0x7E`) are shown as characters; other bytes are shown as `.`.
- `get <addr>` shows decimal, hexadecimal, binary, and character representations.
- Addresses outside `0..4095` are rejected.
- `inc 255` wraps to `0` because `Byte` is `std::uint8_t`.
### 3. Experiments

#### Experiment 1 — `uint8_t` overflow

Commands:

```text
set 0 255
inc 0
get 0
Result:

```text
0  0x0  0b00000000  '.'
#### Experiment 2 — floating-point precision

Command:

```text
python3 -c "print(0.1 + 0.2 == 0.3)"
Result:
False
Additional check:
python3 -c "print(0.1 + 0.2)"
Result:
0.30000000000000004
#### Experiment 3 — C string in memory

Commands:

```text
set 0 65
set 1 66
set 2 0
dump
Result:
0000  41 42 00 00 00 00 00 00 00 00 00 00 00 00 00 00  |AB..............|
#### Experiment 4 — uninitialized memory

I temporarily changed:

`Byte data[MEM_SIZE]{};`

to:

`Byte data[MEM_SIZE];`

After rebuilding, the first line of `dump` contained non-zero and seemingly random bytes:

`0000  00 80 c6 00 5f 5f 67 63 63 5f 65 78 63 65 70 74  |....__gcc_except|`

After restoring `{}`, the first line became:

`0000  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  |................|`

Explanation: without `{}`, the local array has indeterminate values. Reading such values is undefined behavior. With `{}`, the array is value-initialized to zero.
### 4. Build and run

The project was configured and built successfully with:

```text
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build
The program was started with:
./build/ember
The build completed successfully with:
[100%] Built target ember