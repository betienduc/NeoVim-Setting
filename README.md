# ***NeoVim-Setting***
>Just <strong><em>init.lua</em></strong>

<br>
<strong>How can I quit Vim?</strong>
<br>
<img src="https://logosrated.net/wp-content/uploads/parser/Neovim-Logo-1.png" width="132" height="150">  

<pre>
  <code>
-- Set leader key
vim.g.mapleader = " "

-- Load lazy.nvim
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not vim.loop.fs_stat(lazypath) then
  vim.fn.system({
    "git",
    "clone",
    "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)

-- 🛠️ F5 để build & run C++, mở hoàn toàn terminal
vim.api.nvim_set_keymap("n", "<F5>", ":w<CR>:term g++ \"%\" -o \"%<.exe\" && cmd /c start cmd /k \"%<.exe\"<CR>", { noremap = true, silent = true })

-- Plugin setup
require("lazy").setup({

  -- 🌈 Giao diện đẹp
  {
    "catppuccin/nvim",
    name = "catppuccin",
    priority = 1000,
    config = function()
      vim.cmd.colorscheme("catppuccin-mocha")
    end,
  },

  -- 💡 Thanh trạng thái
  {
    "nvim-lualine/lualine.nvim",
    dependencies = { "nvim-tree/nvim-web-devicons" },
    config = function()
      require("lualine").setup({
        options = { theme = "catppuccin" },
      })
    end,
  },

  -- 📁 File Explorer
  {
    "nvim-neo-tree/neo-tree.nvim",
    branch = "v3.x",
    dependencies = {
      "nvim-lua/plenary.nvim",
      "nvim-tree/nvim-web-devicons",
      "MunifTanjim/nui.nvim",
    },
    config = function()
      require("neo-tree").setup({
        filesystem = {
          filtered_items = { visible = true },
          follow_current_file = true,
          use_libuv_file_watcher = true,
        },
        window = {
          width = 30,
          position = "left",
          mappings = {
            ["<space>"] = "toggle_node",
            ["<CR>"] = "open",
            ["o"] = "open",
            ["O"] = "system_open",
            ["<leader>r"] = "rename_file",
            ["<leader>n"] = "create",
            ["<leader>d"] = "delete",
          },
        },
        default_component_configs = {
          icon = {
            enabled = true,
            name = false,
            use_nerd_font = true,
          },
        },
      })

      vim.keymap.set("n", "<leader>e", ":Neotree toggle<CR>", { desc = "Toggle NeoTree" })
    end,
  },

  -- ⚡ Autocomplete
  {
    "hrsh7th/nvim-cmp",
    event = "InsertEnter",
    dependencies = {
      "hrsh7th/cmp-nvim-lsp",
      "hrsh7th/cmp-buffer",
      "hrsh7th/cmp-path",
      "saadparwaiz1/cmp_luasnip",
      "L3MON4D3/LuaSnip",
      "rafamadriz/friendly-snippets",
    },
    config = function()
      local cmp = require("cmp")
      local luasnip = require("luasnip")
      require("luasnip.loaders.from_vscode").lazy_load()

      cmp.setup({
        snippet = {
          expand = function(args)
            luasnip.lsp_expand(args.body)
          end,
        },
        mapping = cmp.mapping.preset.insert({
          ["<Tab>"] = cmp.mapping.select_next_item(),
          ["<S-Tab>"] = cmp.mapping.select_prev_item(),
          ["<CR>"] = cmp.mapping.confirm({ select = true }),
          ["<C-Space>"] = cmp.mapping.complete(),
        }),
        sources = cmp.config.sources({
          { name = "nvim_lsp" },
          { name = "luasnip" },
          { name = "buffer" },
          { name = "path" },
        }),
      })
    end,
  },
 
 -- ✨ Formatter
 {
  "stevearc/conform.nvim",
  config = function()
    require("conform").setup({
      formatters_by_ft = {
        lua = { "stylua" },
        javascript = { "prettier" },
        typescript = { "prettier" },
        cpp = { "clang-format" },
        c = { "clang-format" },
        html = { "prettier" },
        css = { "prettier" },
        json = { "prettier" },
      },
      format_on_save = {
        timeout_ms = 1000,
        lsp_fallback = true,
      },
    })
  end,
},


  -- 🔧 LSP + Mason
  {
    "neovim/nvim-lspconfig",
    dependencies = {
      "williamboman/mason.nvim",
      "williamboman/mason-lspconfig.nvim",
    },
    config = function()
      require("mason").setup()
      require("mason-lspconfig").setup({
        ensure_installed = { "lua_ls", "tsserver", "clangd" },
        automatic_installation = true,
      })

      local capabilities = require("cmp_nvim_lsp").default_capabilities()
      local lspconfig = require("lspconfig")

      require("mason-lspconfig").setup_handlers({
        function(server_name)
          lspconfig[server_name].setup({
            capabilities = capabilities,
          })
        end,
      })
    end,
  },

})


  </code>
</pre>
