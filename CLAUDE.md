# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Vimscript-based Neovim configuration using vim-plug as the plugin manager. The configuration follows a monolithic structure with a single main `init.vim` file (1,676 lines) containing editor settings, plugin definitions, mappings, and configurations.

## Architecture

### Main Configuration Files

- **`init.vim`**: Primary configuration file containing all editor settings, plugin definitions, key mappings, and plugin configurations. This is where most changes will be made.
- **`coc-settings.json`**: Language server client configuration for coc.nvim, including LSP settings for Python, JavaScript/TypeScript, Go, C/C++, Lua, Swift, Bash, and more.
- **`_machine_specific.vim`**: Machine-specific overrides (auto-created from template if missing). Contains Python interpreter paths, browser settings, and IME configuration. **Do not modify directly** - users should edit their own copy.
- **`cursor_for_qwerty.vim`**: Custom cursor movement system for Qwerty keyboard layout (currently commented out in init.vim:198). Provides enhanced movement mappings.
- **`md-snippets.vim`**: Markdown-specific snippets and autocommands.

### Directory Structure

- **`plugged/`**: Plugin installation directory managed by vim-plug
- **`tmp/backup/`** and **`tmp/undo/`**: Backup and persistent undo files
- **`autoload/plug.vim`**: vim-plug plugin manager
- **`default_configs/`**: Template configurations for machine-specific settings
- **`sample_vimspector_json/`**: Debug configuration templates for Vimspector

## Plugin Management

**vim-plug** is used for plugin management with the following characteristics:

- Auto-downloads vim-plug on first use if missing
- Automatic plugin installation when plugins are missing (init.vim:19-35)
- **Update command**: `:PU` (mapped to `PlugUpdate | PlugUpgrade`)
- 100+ plugins configured across multiple categories including:
  - Language support (Python, JavaScript/TypeScript, Go, Java, C/C++, etc.)
  - Git integration (fugitive, gitgutter, lazygit, agit)
  - File navigation (fzf, fzf-lua, ranger/rnvimr)
  - UI enhancements (status line, colors, icons)
  - Development tools (Vimspector debugger, snippets, linting)

## Key Design Patterns

### Leader Key Convention
- **Space (` `)** is used as the leader key for most custom mappings (init.vim:132)
- Many shortcuts use `<LEADER>` prefix (e.g., `<LEADER>r` for compile/run)

### Machine-Specific Configuration Pattern
The config uses a template-based system for machine-specific settings:
1. Checks if `_machine_specific.vim` exists
2. If not, copies from `default_configs/machine_specific.vim`
3. Sources the file (init.vim:40-45)
4. Opens the file for first-time setup (init.vim:1672-1674)

**Important**: When suggesting changes to Python paths or browser settings, remind users to edit `_machine_specific.vim`, not the template.

### Hybrid Vimscript/Lua Architecture
- Primarily Vimscript-based configuration
- Integrates Lua for newer plugins (nvim-treesitter, fzf-lua, lightspeed, nvim-scrollbar)
- Mixed approach balances traditional Vim workflows with modern Neovim features

### Auto-Provisioning
The configuration automatically sets up missing components:
- Downloads vim-plug if not present
- Auto-installs plugins on first launch
- Creates machine-specific config from template

## Common Commands

### Plugin Management
- **Update plugins**: `:PU` (updates all plugins and vim-plug itself)
- **Install plugins**: `:PlugInstall` (usually automatic)
- **Clean plugins**: `:Clean` (remove unused plugins)

### File Navigation (FZF-based)
- **Files**: `<C-p>` (fzf-lua files)
- **Search content**: `<C-f>` (ripgrep search)
- **Recent files**: `<C-u>` (oldfiles)
- **Buffers**: `<C-w>` (buffer list)
- **Built-in commands**: `<C-q>` (fzf-lua builtin)

### Git Operations
- **LazyGit**: `<C-g>` (floating window git interface)
- **Git gutter**: `H` (preview hunk), `<LEADER>g-` (previous), `<LEADER>g=` (next)
- **Agit**: `<LEADER>gi` (interactive git tool)

### Language Server (coc.nvim)
- **Definition**: `gd` (go to definition), `gD` (in new tab)
- **Type definition**: `gy`
- **References**: `gr`
- **Rename**: `<LEADER>rn`
- **Diagnostics**: `<LEADER>dg` (list), `<LEADER>-` (prev), `<LEADER>=` (next)
- **Documentation**: `<LEADER>h`
- **Explorer**: `tt` (file explorer)
- **Fix current**: `<LEADER>qf` (apply AutoFix)
- **Actions**: `<LEADER>a` (code actions)

### Compilation and Execution
- **Compile & Run**: `<LEADER>r` (supports C/C++, Java, Python, JavaScript, Go, HTML, Markdown, LaTeX, Dart)
- The `CompileRunGcc()` function (init.vim:335-384) handles language-specific compilation and execution

### Debugging (Vimspector)
- **Launch**: `<LEADER>dd`
- **Quit**: `<LEADER>dq`
- **Toggle breakpoint**: `<LEADER>dt`
- **Conditional breakpoint**: `<LEADER>dT`
- **Step**: `=` (over), `+` (into), `-` (out)
- **Continue**: `<LEADER>dl`
- **Windows**: `<LEADER>dc` (code), `<LEADER>dv` (variables), `<LEADER>dw` (watches), `<LEADER>ds` (stack), `<LEADER>do` (output)

### Find & Replace
- **Spectre**: `<LEADER>f` (modern find & replace with preview)
- **Quick substitute**: `\s` (in visual or normal mode)

### Editing Enhancements
- **Visual Multi**: Leader key is `,` (comma) for multiple cursors
- **Surround**: `yskw'` to wrap, `cs'`` to change quotes
- **Subversive**: `"` for substitute
- **Comment**: `tcomment_vim` (check plugin docs for mappings)
- **Auto-pairs**: Automatic bracket/quote pairing
- **Wildfire**: Visual mode `k'` to select text in quotes/brackets

### Undo/Redo
- **Undo tree**: `L` (toggle undotree panel)

## Language-Specific Configuration

### Python (init.vim:499-504, coc-settings.json:142-175)
- **LSP**: coc-pyright, coc-jedi
- **Formatting**: black (line length 120)
- **Linting**: flake8, mypy
- **Import sorting**: isort (line length 120, 3-space indent)
- **Syntax highlighting**: semshi (enabled in augroup init.vim:772-775)
- **Important**: Users need to update `python.sortImports.args` in coc-settings.json:145-154 with their actual source paths

### JavaScript/TypeScript
- **LSP**: coc-tsserver (built into coc.nvim)
- **Extensions**: coc-eslint, coc-prettier, coc-import-cost
- **Syntax**: yats.vim, vim-jsx-pretty, vim-styled-components
- **Formatting**: prettier (printWidth: 100, useTabs: true)

### Go (coc-settings.json:219-228)
- **LSP**: gopls via coc.nvim languageserver configuration
- **Features**: diagnostics, code completion, lintTool

### C/C++ (coc-settings.json:235-244)
- **LSP**: ccls with cache directory at `/tmp/ccls`
- **Root patterns**: .ccls, .ccls-root, compile_commands.json, .git/

### Other Languages
- **Java**: coc-java
- **Lua**: lua-lsp
- **Swift**: sourcekit-lsp (Xcode toolchain)
- **Bash**: bash-language-server
- **Docker**: coc-docker
- **YAML/JSON**: coc-yaml, coc-json

## Code Organization

### Plugin Settings Section
Plugin configurations are located in the "Plugin Settings" section (init.vim:626-1642) and are organized alphabetically by plugin name. Each plugin has its own configuration block with clear headers.

### Lua Configuration Blocks
Lua plugins (nvim-treesitter, fzf-lua, lightspeed, nvim-scrollbar) are wrapped in conditional checks (init.vim:1408, 1425, 1476, 1596):
```vim
if g:nvim_plugins_installation_completed == 1
lua <<EOF
-- Lua configuration here
EOF
endif
```

This prevents errors during first-time installation.

### Key Mapping Patterns
- Most custom mappings use `<LEADER>` (Space) prefix
- Terminal mode mappings: `<C-N>` for normal mode, `<C-O>` for normal mode + one command
- Insert mode escape: `kj` (init.vim:223-224)
- Quick escape in command mode: also `kj` (init.vim:224)

## Important File Locations

When making changes:
1. **Global settings**: Edit `init.vim`
2. **LSP settings**: Edit `coc-settings.json`
3. **Machine-specific**: Edit `_machine_specific.vim` (not the template)
4. **Markdown snippets**: Edit `md-snippets.vim`
5. **Cursor mappings**: Uncomment and customize `cursor_for_qwerty.vim` in init.vim:198
6. **Debug templates**: Add to `sample_vimspector_json/`

## Special Features

### Compile Function (`CompileRunGcc`)
Located at init.vim:335-384, supports:
- **C/C++**: GCC/G++ compilation with execution
- **Python**: Direct execution with python3
- **Java**: javac + java execution
- **JavaScript**: Node with DEBUG flags
- **Go**: go run
- **HTML**: Browser preview via `g:mkdp_browser`
- **Markdown**: Instant Markdown Preview
- **LaTeX**: Vimtex compilation
- **Dart**: Flutter run commands

### Session Management
- **Auto-setup**: Creates backup and undo directories automatically (init.vim:104-112)
- **Persistent undo**: Enabled with undofile
- **Cursor restoration**: Returns to last edit position on file open (init.vim:117)

### Custom Cursor Movement System
The `cursor_for_qwerty.vim` file provides an alternative movement system (currently disabled). If enabling this, be aware it extensively redefines movement keys using a home row-based system.

### Explorer (coc-explorer)
Custom keybindings defined in coc-settings.json:96-141:
- Navigation: `hjkl` for movement
- Actions: `i` to expand/open, `<cr>` to cd/open, `o` to toggle expand
- File operations: `a` (add), `cw` (rename), `d` (delete), `yy` (copy), `pp` (paste)
- Git: `<<` (stage), `>>` (unstage), `[c`/`]c` (navigate conflicts)
- Modified mapping: `gp` for gotoParent (instead of default `l` at line:110)

## Modification Guidelines

### Adding New Plugins
1. Add `Plug 'author/plugin'` between `call plug#begin()` and `call plug#end()` (init.vim:395-592)
2. Add plugin configuration in the "Plugin Settings" section
3. Run `:PlugInstall` to install
4. For coc extensions, add to `g:coc_global_extensions` list (init.vim:657-680)

### Adding Key Mappings
- Follow existing patterns with `<LEADER>` prefix
- Add comments explaining the mapping
- Consider conflicts with existing plugins (especially vim-visual-multi, lightspeed, etc.)

### Language Support
To add support for a new language:
1. Add syntax plugin in Plugins section
2. Configure LSP in `coc-settings.json` under `languageserver` or install coc extension
3. Add compilation logic to `CompileRunGcc()` if needed
4. Add formatter/linter configuration to coc-settings.json

### Git Workflow
When creating git commits:
- **DO NOT** include `Co-Authored-By` or any author attribution tags in commit messages
- Keep commit messages clean and focused on what changed and why
- This repository maintains a clean commit history without external attribution

### Troubleshooting Common Issues
- **Plugins not loading**: Run `:PlugInstall` and restart Neovim
- **coc.nvim issues**: Run `:CocInfo`, check extension is in `g:coc_global_extensions`
- **Python errors**: Check Python paths in `_machine_specific.vim`
- **LSP not working**: Run `:CocCommand coc.restart` after modifying coc-settings.json
- **First setup**: The config will auto-create `_machine_specific.vim` - fill in the required paths

## Performance Notes

- Lazy loading is used where appropriate
- Persistent undo history enabled
- Virtual text diagnostics available (toggle via coc settings)
- Fast search with fzf and ripgrep integration
- Treesitter syntax highlighting for Java, C, Bash, Python (init.vim:1412)
