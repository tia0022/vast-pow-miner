# vast-pow-miner — 交付物总览

## 文件清单

| 文件 | 说明 |
| --- | --- |
| `vast-pow-miner.zip` (26 KB) | 完整仓库压缩包，14 个文件，可直接推 GitHub |
| `vast-pow-miner/` | 解压后目录，和 zip 内容一致 |
| `hash256-vast-miner/` | 旧版本（HASH256 专用），保留作为参考 |

## 仓库结构

```
vast-pow-miner/
├── README.md                    # 主 README（中英说明 + 诚实经济分析）
├── LICENSE                      # MIT
├── .env.example                 # 环境变量模板
├── .gitignore                   # 保护私钥 / venv / logs
├── setup_vast.sh                # 一键 provisioning（profile-aware）
├── start_mining.sh              # 启动 miner + 监控（profile-aware）
├── profiles/
│   ├── hash256.yaml             # HASH256 参考 profile（已测试）
│   ├── template.yaml            # 空模板（添加新 token 用）
│   └── README.md                # 如何贡献 profile
├── bin/
│   ├── onboard.py               # 从合约地址自动生成 profile
│   ├── generic_monitor.py       # 通用盈亏监控 + 自动止损
│   └── scan_mints.py            # 全网 mint 活动扫描
└── docs/
    ├── QUICKSTART.md            # 10 步傻瓜式教程（中英）
    └── ADD_PROFILE.md           # 添加新 token 指南
```

## 核心改进（vs. 旧版）

1. **Profile 系统**：每个 token 一个 YAML，工具完全配置驱动
2. **自动 onboarding**：`python3 bin/onboard.py --contract 0x... --name mytoken`自动从 Etherscan 拉 ABI、猜 mine 函数、生成 profile
3. **多链支持**：Ethereum / Polygon / Base / Arbitrum
4. **真·傻瓜式**：`docs/QUICKSTART.md` 是 10 步教程，每一步都有具体命令
5. **诚实经济**：README 头部直接摆亏损数据，不包装成"被动收入"

## 使用流程（用户视角）

```bash
# --- 挖 HASH256（内置）---
git clone https://github.com/YOUR_USERNAME/vast-pow-miner
cd vast-pow-miner
bash setup_vast.sh            # 自动装 CUDA/Rust/Python/miner
nano .env                      # 填挖矿钱包私钥
VAST_COST_PER_HOUR_USD=0.343 STOP_LOSS_USD=-3 bash start_mining.sh

# --- 挖其他 keccak256 PoW ERC20 ---
python3 bin/onboard.py --contract 0xABCD... --name mytoken
PROFILE=mytoken bash start_mining.sh
```

## 推文（中英两版）

**中文版**（Twitter/X，中文圈）：

> 刚开源了 vast-pow-miner：
>
> - 租一台 vast.ai 的 GPU，一条命令就能挖 keccak256 PoW 的 ERC20（HASH256 内置、其他币丢个合约地址自动生成 profile）
> - 实时盯 DexScreener 币价 + 链上 gas，亏到阈值自动关机
> - 中英文傻瓜式教程，新手 10 步开挖
>
> 当前 HASH256 经济性不佳（5090 每天净亏 $5），代码主要价值在**不亏得不明不白**。币涨 3 倍立刻正收益。
>
> GitHub: github.com/YOUR_USERNAME/vast-pow-miner
>
> vast.ai 佣金注册：cloud.vast.ai/?ref_id=537207
>
> #挖矿 #PoW #GPU

**English version**（全球）：

> Open-sourced **vast-pow-miner**: rent a GPU on vast.ai, mine any keccak256 PoW ERC-20 with one command.
>
> - 📦 Profile-based: HASH256 built-in; drop in any contract address, `onboard.py` auto-generates the profile from Etherscan ABI
> - 📊 Real-time P&L: DexScreener price × on-chain gas, auto-stops on loss
> - 🛑 Three auto-stop triggers: net P&L, token crash, low gas
> - 🇨🇳🇬🇧 Bilingual idiot-proof quickstart (10 steps)
>
> Honest disclaimer in the README: HASH256 currently loses \~$5/day on a 5090. The value of this code is **you stop bleeding quickly** — and the moment the token 3x's, it flips green.
>
> GitHub: github.com/YOUR_USERNAME/vast-pow-miner
>
> vast.ai referral (supports me): cloud.vast.ai/?ref_id=537207
>
> #GPU #mining #crypto #PoW

> 把 `YOUR_USERNAME` 全局替换为你的 GitHub 用户名再发。

## 推 GitHub 的命令

```bash
# 1. github.com 新建空仓库 vast-pow-miner（不勾 "Initialize with README"）

# 2. 本地（解压你下载的 zip）：
cd vast-pow-miner
# 替换所有 YOUR_USERNAME
sed -i "s|YOUR_USERNAME|你的GitHub用户名|g" README.md docs/*.md

# 3. 初始化 git 并推送
git init
git add -A
git commit -m "Initial release: vast-pow-miner v0.1"
git branch -M main
git remote add origin https://github.com/你的用户名/vast-pow-miner.git
git push -u origin main
```

## 还剩一件事

**旧 repo** `outputs/hash256-vast-miner/` **是否保留？**

- 保留：可以双发（vast-pow-miner 是新框架，hash256-vast-miner 是"专用版"）
- 删除：统一用新框架（推荐）

等你决定。