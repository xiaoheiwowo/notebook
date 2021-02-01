# vim

- 与缩进相关的参数有shiftwidth、tabstop、softtabstop、expandtab。
```
shiftwidth reindent 操作（<<和>>）时缩进的列数（这里的一列相当于一个空格）
tabstop 一个tab键所占的列数，linux 内核代码建议每个tab占用8列
softtabstop 敲入tab键时实际占有的列数。
expandtab 输入tab时自动将其转化为空格
```

## vimrc

```
call plug#begin('~/.vim/plugged')
Plug 'itchyny/lightline.vim'
Plug 'junegunn/vim-easy-align'
Plug 'tpope/vim-commentary'
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
Plug 'tpope/vim-surround'
" Plug 'vim-airline/vim-airline'
" Plug 'vim-airline/vim-airline-themes'
" 对齐线
" Plug 'Yggdroot/indentLine'
" if has('nvim')
"   Plug 'Shougo/defx.nvim', { 'do': ':UpdateRemotePlugins' }
" else
"   Plug 'Shougo/defx.nvim'
"   Plug 'roxma/nvim-yarp'
"   Plug 'roxma/vim-hug-neovim-rpc'
" endif
call plug#end()


""-------------------------------------------NERDTree插件配置开始------------------------------------------------------
" NerdTree才插件的配置信息
""将F2设置为开关NERDTree的快捷键
map <f2> :NERDTreeToggle<cr>
""修改树的显示图标
let g:NERDTreeDirArrowExpandable = ' ▶'
let g:NERDTreeDirArrowCollapsible = ' △'
""窗口位置
let g:NERDTreeWinPos='right'
""窗口尺寸
let g:NERDTreeSize=30
""窗口是否显示行号
let g:NERDTreeShowLineNumbers=0
""不显示隐藏文件
let g:NERDTreeHidden=0
""-------------------------------------------nerdtree插件配置结束------------------------------------------------------

"vim-commentary
"为python和shell等添加注释
autocmd FileType python,shell,coffee set commentstring=#\ %s
"修改注释风格
autocmd FileType java,c,cpp set commentstring=//\ %s

""---------------------------------------------------------------------------------------------------------------------
" vim-airline
" 是否显示状态栏。0 表示不显示，1 表示只在多窗口时显示，2 表示显示
" set laststatus=2  "永远显示状态栏
" let g:airline_powerline_fonts = 1  " 支持 powerline 字体
" let g:airline#extensions#tabline#enabled = 1 " 显示窗口tab和buffer
""---------------------------------------------------------------------------------------------------------------------
set nu
set ruler
syntax on
colo desert
set expandtab
" %ret! 4
set tabstop=4 "设定tab宽度为4个字符
set shiftwidth=4 "设定自动缩进为4个字符
set expandtab "用space替代tab的输入

" 光标
" set cc=120
let g:indent_guides_guide_size=1
" 光标所在的当前行高亮
set cursorline
highlight CursorLine   cterm=NONE ctermbg=black ctermfg=green guibg=NONE guifg=NONE
" 显示光标所在行的行号，其它行都为相对改行的行号
" set relativenumber
" 设置编码
set encoding=utf-8
set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
set termencoding=utf-8
" 支持使用鼠标
set mouse=a
" 在底部显示当前模式 
" set showmode 
" 命令模式下显示键入的指令
" set showcmd
" 开启文件类型检查，并且载入与该类型对应的缩进规则(如.py 文件会去找~/.vim/indent/python.vim)
" filetype indent on
" #在状态栏显示光标的当前位置（位于哪一行哪一列）
" set ruler
" 光标遇到圆括号、方括号、大括号时，自动高亮对应的另一个圆括号、方括号和大括号
set showmatch
" 搜索时，高亮显示匹配结果
" set hlsearch
" 输入搜索模式时，每输入一个字符，就自动跳到第一个匹配的结果
set incsearch
" 搜索时忽略大小写
set smartcase
" set paste


```

# vim 基本使用快捷键

## 移动光标

$ 行尾 ^ 行首
\# 上个相同单词 * 下个相同单词
% 在括号中移动
w 下一个单词词尾 e 下个单词词首
b 前一个单词
X 退格
ctrl + r 重做
ｓ覆盖
gg H M L G 首行　中间　末尾

- + 上一行下一行
    { } 前一个后一个空行
    ( ) 当前句子的行首行尾

## 查找

f 查找 F 反向查找
df(

## 搜索

/ ? 前后查找
n N# 下一个上一个

* 直接匹配到当前光标的字符串

## 高级

<< >> 缩进
~ 切换大小写 title r 替换一个字符
ZZ ZQ
zt zb
ctrl + F/B 翻页

整页翻页 ctrl-f ctrl-b
f就是forword b就是backward

翻半页
ctrl-d ctlr-u
d=down u=up

滚一行
ctrl-e ctrl-y

替换

```shell
:% s/old/new/gc
```

覆盖插入
s

%在配对的括号之间移动

## 多文件
```
:e file_name # 打开文件
:sp/vsp/:split :vsplit # 分割窗口
ctrl 6/:bn/bN # 多文件跳转
:ls # 查看打开的文件
:b num
# 窗口间跳转
ctrl w hjkl
ctrl w HJKL
ctrl w r
ctrl ww
ctrl -+<>
ctrl C
:tabnew :tabc :tabo :tabs :tabp :tabn gt 标签页操作

```
