## ・最新版の Neovim をインストールする
```
brew install neovim --HEAD
```
更新時はこれだけで OK                       
```
brew reinstall neovim
```


## ・packer.nvim のインストール (deinの代替)
```
git clone https://github.com/wbthomason/packer.nvim \
  ~/.local/share/nvim/site/pack/packer/opt/packer.nvim
```

設定ファイルに追記
```
nvim ~/.config/nvim/init.lua
```

```lua
-- ~/.config/nvim/lua/plugins.luaを読み込む
require "plugins"

-- load the "vim" module
vim = vim or {}


vim.opt.fileencoding = "utf-8"                  -- the encoding written to a file
vim.opt.cursorline = true                       -- highlight the current line
vim.opt.number = true                           -- set numbered lines
vim.opt.shiftwidth = 2

vim.cmd("colorscheme lucario")

--ショートカット
vim.cmd 'nnoremap gr gT'
vim.cmd 'nnoremap <C-e> :NERDTreeToggle<CR>'       --ファイルツリー
vim.cmd 'nnoremap <C-p> :Telescope find_files<CR>' --ファイル検索
vim.cmd 'nnoremap <C-g> :Telescope live_grep<CR>'  --ライブgrep
--ショートカット



vim.api.nvim_create_autocmd("BufWritePost", {
  pattern = { "plugins.lua" },
  command = "PackerCompile",
})
```

```
vim ~/.config/nvim/lua/plugins.lua
```
```
vim.cmd.packadd "packer.nvim"

require("packer").startup(function()
  -- 起動時に読み込むプラグインは名前を書くだけです
  -- GitHubのリポジトリ名称
  use "nvim-lua/plenary.nvim"
  use "preservim/nerdtree"
  use "raphamorim/lucario"
  --use "tpope/vim-fugitive"
  --use "tpope/vim-repeat"
  use {
    "nvim-telescope/telescope.nvim",--ファジーファインダーのnvim用プラグイン
    requires = {
      { "nvim-telescope/telescope-live-grep-args.nvim" },
    },
    config = function()
      require("telescope").load_extension("live_grep_args")
    end
  }

  -- opt オプションを付けると遅延読み込みになります。
  -- この場合は opt だけで読み込む契機を指定していないため、
  -- packadd コマンドを叩かない限り読み込まれることはありません。
  use { "wbthomason/packer.nvim", opt = true }
  -- packer.nvim 自体を遅延読み込みにする理由はまた後ほど。

  -- コマンドを叩いたときに読み込む。
  use { "rhysd/git-messenger.vim", opt = true, cmd = { "GitMessenger" } }

  -- 特定のイベントで読み込む
  use { "tpope/vim-unimpaired", opt = true, event = { "FocusLost", "CursorHold" } }

  -- 特定のファイルタイプのファイルを開いたら読み込む
  use { "fatih/vim-go", opt = true, ft = { "go" } }

  -- 特定のキーを叩いたら読み込む
  -- この例ではノーマルモードの <CR> にマッピングしていますが、
  -- モードを指定する場合はテーブルを入れ子にします。
  -- keys = {
  --   { "n", "<CR>" },
  --   { "v", "<CR>" },
  -- }
  use {
    "arecarn/vim-fold-cycle",
    opt = true,
    keys = { "<CR>" },
  }

  -- 特定の VimL 関数を呼ぶと読み込む
  -- この例だと、任意の場所で Artify("hoge", "bold") のように呼び出された時に、
  -- このプラグインも読み込まれます。
  use { "sainnhe/artify.vim", opt = true, fn = { "Artify" } }

  -- 実は opt = true は省略できます。読み込む契機（この例では cmd）を指定すると、
  -- 自動的に遅延読み込みとみなされます。
  use {
    "npxbr/glow.nvim",
    cmd = { "Glow", "GlowInstall" },
    -- run オプションを指定すると、インストール時・更新時に
    -- 実行するコマンドを指定できます。
    run = [[:GlowInstall]],
    -- 先頭に : がついていないなら bash -c "..." で実行されます。
    -- run = [[npm install]],
    -- 関数も指定可能です。
    -- run = function()
    --   vim.cmd.GlowInstall()
    -- end,
  }

  -- 条件が真の時のみ読み込みます。条件は起動時に毎回判定されます。
  use {
    "thinca/vim-fontzoom",
    cond = [[vim.fn.has"gui" == 1]], -- GUI の時のみ読み込む。
    -- 関数も指定できます。
    -- conf = function()
    --   return vim.fn.has "gui" == 1
    -- end,
  }

  -- 依存関係も管理できます。vim-prettyprint は capture.vim に依存しています。
  use {
    "tyru/capture.vim",
    requires = {
      { "thinca/vim-prettyprint" },
    },
  }
end)
```

## コマンド一覧
packer.nvim をインストールすると以下のコマンドが使えるようになります。   

`PackerInstall`   
プラグインをインストールする。   
`PackerUpdate`    
追加されたプラグインをインストールし、既存のものは更新する。    
`PackerClean`   
必要なくなったプラグインを削除する。    
`PackerSync`    
PackerClean の後に PackerUpdate を行う。   
`PackerCompile`   
設定ファイルを「コンパイル」する。   
一度設定した後は PackerSync を定期的に叩くだけでプラグインは最新のものになります。










