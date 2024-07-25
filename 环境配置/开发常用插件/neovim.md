# 一、Centos7 install nvim

- yum install gettext
- make CMAKE_BUILD_TYPE=RelWithDebInfo CMAKE_INSTALL_PREFIX=/usr/local/nvim_install -j
- ln -s /usr/local/nvim_install/bin/nvim /bin/nvim



# 二、插件安装

- coc 需要安装 nodjs

  ```shell
  rpm -Uvh https://rpm.nodesource.com/pub_16.x/el/7/x86_64/nodesource-release-el7-1.noarch.rpm
  yum -y install nodejs
  ```

  

  - [install nodejs](https://github.com/nodesource/distributions/issues/1774)
  - 最终还是有问题

- cmake lsp 配置

  ```shell
  # 1、下载neovim 客户端
  Plug 'neovim/nvim-lspconfig'
  
  # 2、配置cmake 服务器
  pip3 install --trusted-host pypi.org --trusted-host pypi.python.org --trusted-host files.pythonhosted.org cmake-language-server
  
  # 3、配置脚本
  
  ```

  

- centos7 更新 git

  ```
  yum install epel-release
  yum remove git
  rpm -U https://centos7.iuscommunity.org/ius-release.rpm
  yum install git2u
  ```

  

# 三、nvim 配置文件

```shell
" 呆子树
Plug 'preservim/nerdtree'

"lsp 客户端
"Plug 'neovim/nvim-lspconfig'


Plug 'prabirshrestha/vim-lsp'


Plug 'neoclide/coc.nvim', {'commit': '5b392c5'}


call plug#end()


" NERDTree 相关设置 ---------
" 打开和关闭 NERDTree 的快捷键
nnoremap <C-r> :NERDTreeToggle<CR>
" 查找当前文件在 NERDTree 中的位置
nnoremap <C-f> :NERDTreeFind<CR>


" Vim 自带相关配置
" 启用语法高亮
syntax on

" 设置行号
set number

" 设置缩进
set tabstop=4
set shiftwidth=4
set expandtab

" 启用行高亮
set cursorline
```



# 四、快捷键概念

### Vim 脚本语法 (`init.vim`)

使用 `:map`、`:nmap`、`:imap` 等命令来设置不同模式下的快捷键：

- `:map`：适用于所有模式。
- `:nmap`：适用于普通模式。
- `:imap`：适用于插入模式。
- `:vmap`：适用于可视模式。

```
vim复制代码" 普通模式下将 `jj` 映射为 `Esc`
nmap jj <Esc>

" 插入模式下将 `jk` 映射为 `Esc`
imap jk <Esc>

" 将 `<leader>w` 映射为保存文件命令
nmap <leader>w :w<CR>
```

### Lua 脚本语法 (`init.lua`)

Neovim 0.5+ 支持使用 Lua 进行配置，可以通过 `vim.api.nvim_set_keymap` 函数来设置快捷键：

```
lua复制代码-- 普通模式下将 `jj` 映射为 `Esc`
vim.api.nvim_set_keymap('n', 'jj', '<Esc>', { noremap = true, silent = true })

-- 插入模式下将 `jk` 映射为 `Esc`
vim.api.nvim_set_keymap('i', 'jk', '<Esc>', { noremap = true, silent = true })

-- 将 `<leader>w` 映射为保存文件命令
vim.api.nvim_set_keymap('n', '<leader>w', ':w<CR>', { noremap = true, silent = true })
```

## 设置 `leader` 键

`leader` 键是一个可以自定义的前缀键，常用于组合快捷键。默认情况下，`leader` 键是 `\`，但你可以将其设置为其他键，如空格键：

### Vim 脚本语法

```
vim复制代码" 将 leader 键设置为空格
let mapleader = " "
```

### Lua 脚本语法

```
lua复制代码-- 将 leader 键设置为空格
vim.g.mapleader = ' '
```

## 常见快捷键示例

### 文件操作

```
vim复制代码" 保存文件
nmap <leader>w :w<CR>

" 关闭文件
nmap <leader>q :q<CR>

" 保存并退出
nmap <leader>x :wq<CR>
```

### 窗口操作

```
vim复制代码" 水平分割窗口
nmap <leader>sh :split<CR>

" 垂直分割窗口
nmap <leader>sv :vsplit<CR>

" 切换到上一个窗口
nmap <leader>k <C-w>k

" 切换到下一个窗口
nmap <leader>j <C-w>j

" 切换到左边的窗口
nmap <leader>h <C-w>h

" 切换到右边的窗口
nmap <leader>l <C-w>l
```

### 缓冲区操作

```
vim复制代码" 切换到下一个缓冲区
nmap <leader>bn :bnext<CR>

" 切换到上一个缓冲区
nmap <leader>bp :bprevious<CR>

" 关闭当前缓冲区
nmap <leader>bd :bdelete<CR>
```

### 代码编辑

```
vim复制代码" 在普通模式下复制到系统剪贴板
nmap <leader>y "+y

" 在可视模式下复制到系统剪贴板
vmap <leader>y "+y

" 在普通模式下粘贴来自系统剪贴板的内容
nmap <leader>p "+p
```

## 高级快捷键设置

### 配合插件使用

Neovim 有许多强大的插件，配合快捷键可以提升工作效率。以下是一些示例：

#### 使用 `telescope.nvim` 进行模糊查找

```
lua复制代码-- 安装插件后，可以使用如下快捷键设置
vim.api.nvim_set_keymap('n', '<leader>ff', ':Telescope find_files<CR>', { noremap = true, silent = true })
vim.api.nvim_set_keymap('n', '<leader>fg', ':Telescope live_grep<CR>', { noremap = true, silent = true })
```

#### 使用 `nvim-tree.lua` 管理文件树

```
lua复制代码-- 安装插件后，可以使用如下快捷键设置
vim.api.nvim_set_keymap('n', '<leader>e', ':NvimTreeToggle<CR>', { noremap = true, silent = true })
```

完成上述配置后，保存并重新启动 Neovim，你的快捷键设置就会生效。如果你使用的是 `init.vim`，请将其放置在 `~/.config/nvim/` 目录下。如果你使用的是 `init.lua`，也应放置在同一目录下。

通过自定义快捷键，你可以极大地提升 Neovim 的使用效率，使其更加适合你的个人习惯和工作流程。
