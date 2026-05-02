# Minishell

A sophisticated, feature-rich shell implementation written in C. This project demonstrates deep understanding of Unix shell architecture, process management, and system-level programming concepts.

## 🎯 Overview

**Minishell** is a custom shell that replicates core functionality of `bash` with a focus on clean parsing, robust execution, and comprehensive built-in commands. It handles complex command-line scenarios including pipes, redirections, heredocs, variable expansion, and signal handling.

### What Makes It Unique

- **Token-Based Parser**: Advanced lexical analysis that builds a token tree structure, enabling complex command chains and proper operator precedence
- **Dual List Architecture**: Separate environment and variable lists for proper variable scoping and management
- **Comprehensive Shell Features**: Complete implementation of pipes, input/output redirection, heredocs, command substitution, and wildcard expansion
- **Built-in Command Suite**: 11 custom implementations of essential shell commands with full bash compatibility
- **Signal Management**: Proper handling of interrupt signals (Ctrl-C, Ctrl-D, Ctrl-\) with context-aware behavior
- **Variable Expansion**: Intelligent expansion of environment variables with proper quoting and escaping
- **Error Resilience**: Comprehensive error handling with appropriate exit codes and error messages

## ✨ Features

### Core Functionality

- **Command Execution**: Execute external programs with full argument passing
- **Piping**: Chain commands with pipes (`|`) to pass output as input
- **Redirection**:
  - Input redirection (`<`)
  - Output redirection (`>`)
  - Append redirection (`>>`)
  - Heredoc redirection (`<<`)
- **Command Separation**: Sequential execution with `;` separator
- **Logical Operators**: Conditional execution with `&&` and `||`
- **Variable Expansion**: `$VARIABLE` and `$?` (exit status) expansion
- **Quote Handling**: Single quotes, double quotes, and escape sequences

### Built-in Commands

1. **`cd [directory]`** - Change directory (supports `~`, `-`, and relative paths)
2. **`echo [options] [text]`** - Print text with `-n` flag support
3. **`ls`** - List directory contents
4. **`pwd`** - Print working directory
5. **`env`** - Display environment variables
6. **`export [var=value]`** - Export environment variables
7. **`unset [variable]`** - Remove environment variables
8. **`exit [code]`** - Exit the shell with optional status code
9. **`exec [command]`** - Replace shell process with command
10. **`source [file]`** - Execute commands from a file
11. **`doc [command]`** - Display command documentation

### Advanced Features

- **Wildcard Expansion**: `*` pattern matching for filenames
- **Command Substitution**: Nested command execution support
- **Subshell Execution**: Parenthesized command groups
- **History Management**: Command history with readline integration
- **Signal Handlers**: Context-aware handling of SIGINT, SIGQUIT, EOF

## 🏗️ Architecture

### Parsing Pipeline

The shell uses a multi-stage parsing approach:

```
Input Stream
    ↓
[Input Validation] - Check for syntax errors, quote matching
    ↓
[Input Splitting] - Split into tokens by delimiters
    ↓
[Token Parsing] - Create token objects with types (cmd, arg, pipe, redirect, etc.)
    ↓
[Token Expansion] - Variable expansion, wildcard resolution, quote removal
    ↓
[Token Validation] - Verify command structure and argument validity
    ↓
[Execution] - Process tokens and execute commands
```

### Token System

The core of the parser is the **Token** structure (`t_token`), which represents atomic command units:

- **Token Types**:
  - `tk_cmd` - Command name
  - `tk_arg` - Command argument
  - `tk_red_in` - Input redirection
  - `tk_red_out` - Output redirection
  - `tk_red_app` - Append redirection
  - `tk_hered` - Heredoc delimiter
  - `tk_pipe` - Pipe operator
  - `tk_logical` - Logical operators (`&&`, `||`)
  - `tk_wildcard` - Wildcard patterns
  - `tk_cmdsep` - Command separator

### Key Components

#### **Input Processing** (`c_files/input/`)

- `input.c` - Main input loop with readline integration
- `input_check.c` - Syntax validation and quote matching
- `input_split.c` - Raw tokenization from input string
- `input_substitute.c` - Variable and command substitution
- `input_execute.c` - Execute parsed command chain
- `subshell.c` - Handle subshell execution (parenthesized commands)
- `subshell_solve.c` - Subshell resolution logic

#### **Token Processing** (`c_files/tokens/`)

- `token_parse.c` - Token creation and type determination
- `token_expand.c` - Variable expansion and quote removal
- `token_validate.c` - Syntax validation and structure checking
- `token_exec.c` - Direct token execution logic
- `token_wildcard.c` - Glob pattern matching and expansion
- `tokens.c` - Token list management

#### **Redirection & Piping** (`c_files/redirection/`)

- `pipe.c` - Pipe creation and connection between processes
- `redir.c` - Input/output redirection file descriptor setup
- `heredoc.c` - Here-document handling and temporary file management
- `fds.c` - File descriptor management and duplication
- `parse_heredoc.c` - Heredoc delimiter parsing

#### **Built-in Commands** (`c_files/builtins/`)

Each built-in command has dedicated implementation:

- `cd.c`, `pwd.c` - Directory navigation
- `echo.c` - Text output with flag parsing
- `env.c`, `export.c`, `unset.c` - Environment variable management
- `exit.c` - Shell termination
- `exec.c`, `exec_utils.c` - Command execution
- `ls.c` - Directory listing
- `source.c` - Script execution
- `doc.c` - Documentation display

#### **Utilities & Tools** (`c_files/tools/`)

- `signal.c` - Signal handlers (SIGINT, SIGQUIT)
- `cwd.c` - Current working directory tracking
- `debug.c` - Debug printing utilities
- `var/` - Variable and environment management
- `str_tools/` - String manipulation utilities

#### **Initialization** (`c_files/init/`)

- `init.c` - Main data structure initialization
- `init_bltn.c` - Built-in command function pointers setup

### Data Structures

#### Main Program Data (`t_data`)

- `env_list` - Environment variables (doubly-linked list)
- `var_list` - Shell variables
- `oldt` - Terminal settings (for signal handling)
- `last_exit` - Exit status of last command
- `cwd` - Current working directory
- `prv_input` - Previous command (for history)

#### Token Structure (`t_token`)

```c
struct s_token {
    char          *name;          // Token string content
    t_tktype      type;           // Token classification
    struct s_token *next;         // Linked list pointers
    struct s_token *prv;
    struct s_token *pipe_out;     // Pipe connections
    struct s_token *redir;        // Redirection pointers
    int           rd_fd;          // Redirection file descriptor
    int           is_cmd_subst;   // Command substitution flag
    // ... additional fields
};
```

## 🛠️ Building & Running

### Prerequisites

- GCC/Clang compiler with C99 standard
- Readline development library (macOS: `brew install readline`)
- Unix-like environment (Linux, macOS)

### Build Instructions

```bash
# Standard build
make

# Debug build with AddressSanitizer
make debug

# Run memory leak detection (macOS)
make leaks

# Run Valgrind checks (Linux)
make valgrind

# Clean build artifacts
make clean
```

### Running the Shell

```bash
# Interactive mode
./minishell

# Script mode (execute file)
./minishell script.sh [args...]

# Debug mode
./minishell -d
```

## 📚 Project Structure

```
minishell_42/
├── c_files/                    # Main source code
│   ├── minishell.c            # Entry point
│   ├── init/                  # Initialization routines
│   ├── input/                 # Input parsing and processing
│   ├── tokens/                # Token system
│   ├── redirection/           # Pipes and redirection
│   ├── builtins/              # Built-in commands
│   └── tools/                 # Utilities and helpers
├── libft/                      # Custom standard library
├── gnl/                        # Get_next_line (file reading)
├── dprintf/                    # Custom printf implementation
├── lists/                      # List data structure library
├── makefile                    # Build configuration
├── msh.h                       # Main header file
└── msh_style.h                # Style/color definitions
```

## 🔍 Parsing Example

For input: `cat file.txt | grep "pattern" > output.txt`

1. **Splitting**: `["cat", "file.txt", "|", "grep", "\"pattern\"", ">", "output.txt"]`
2. **Tokenizing**:
   - `cat` → `tk_cmd`
   - `file.txt` → `tk_arg`
   - `|` → `tk_pipe`
   - `grep` → `tk_cmd`
   - `"pattern"` → `tk_arg`
   - `>` → `tk_red_out`
   - `output.txt` → `tk_arg`
3. **Expanding**: Quote removal, variable substitution
4. **Validation**: Check command structure
5. **Execution**: Create pipes, redirect I/O, fork/exec processes

## 🎓 Key Algorithms

### Variable Expansion

Recursively scans input for `$VARIABLE` patterns and replaces with environment values. Handles special cases like `$?` for exit status.

### Wildcard Matching

Uses `opendir`/`readdir` to glob patterns and expand to matching filenames. Preserves non-matching patterns as literal arguments.

### Pipe Chain Execution

1. For each command in pipe chain:
   - Create pipe (if not last command)
   - Fork process
   - Redirect stdout to pipe write-end (if not last)
   - Redirect stdin from previous pipe (if not first)
   - Execute command
2. Parent waits for all children, collects exit status

### Heredoc Handling

1. Create temporary file
2. Read lines from input until delimiter
3. Redirect stdin from temporary file to command
4. Clean up temporary file

## 🐛 Debugging & Testing

### Enable Debug Output

Define `DEBUG_MODE` to enable detailed parsing output:

```c
d->debug_mode = 1;  // Enable in minishell.c
```

### Memory Leak Detection

```bash
# AddressSanitizer
make debug
./minishell

# Valgrind (with suppression file)
make valgrind

# macOS leaks command
make leaks
```

### Test Complex Commands

```bash
# Pipes
echo "hello world" | wc -w

# Redirections
ls > filelist.txt
cat < filelist.txt

# Heredocs
cat << EOF
multi-line
input here
EOF

# Variable expansion
export USER=john
echo "Hello $USER"

# Logical operators
true && echo "yes" || echo "no"

# Command substitution
echo "Current: $(pwd)"
```

## 📝 Coding Standards

- **42 Norm Compliant**: Follows strict 42 School coding standards
- **Header Format**: All files include 42 header with creator info
- **Function Length**: Max 25 lines per function (enforced by norm)
- **Variable Names**: Clear, descriptive naming conventions
- **Comments**: Strategic comments for complex logic

## 🚀 Performance Considerations

- **Process Management**: Efficient forking with minimal overhead
- **Memory**: Custom allocators prevent fragmentation
- **String Operations**: Optimized string manipulation in `libft`
- **File I/O**: Buffered reading with `get_next_line`

## 🔐 Error Handling

Comprehensive error management with:

- Proper exit codes (2 = not found, 1 = not executable, 126-127 = bash standard codes)
- Error messages to stderr
- Signal-safe signal handlers
- Memory cleanup on all exit paths

## 📖 Additional Resources

- `ressources/doc/` - Command documentation files
- `Tasks.md` - Development task tracking
- Signal handling patterns compatible with POSIX standards
- Comments throughout codebase for complex sections

## 👨‍💻 Author

Created by **Giulio Valente (gvalente95)** and **Pierre-loïc** at 42 School

## 📄 License

This project is part of the 42 curriculum and follows school guidelines.

---

**Note**: This is an educational implementation. While feature-complete for most common use cases, it may not handle every edge case that a production shell handles. Always use actual `bash` or `zsh` for critical work.
