# RSI × SParC 完整工程

这个包合并了 RSI 实验界面、自建 Python 后端，以及 SParC 基线和单、双 Agent 对照实验。打开 RSI 后可以选择数据库，切换直接生成、工具 Agent 或双 Agent 核查，并查看 SQL 与核查轨迹。

目录结构：

```text
frontend/               RSI 前端，包含 npm 锁文件和前端本地依赖
outputs/sparc_api_lab/  Python 后端、Agent 与配置模板
work/sparc-report/      评估与离线测试
results/                已核验的结果和指标
run_dev.py              完整 Dev 对照入口
start_rsi.py            同时启动前端与后端
```

## 本地启动

使用 Python 3.12、Git 和支持 Next.js 16 的 Node.js（建议 22 或 24）及 npm。以下命令从仓库根目录执行。

```powershell
python -m pip install -r outputs/sparc_api_lab/requirements.txt
npm --prefix frontend ci --install-links
```

从 [SParC 官方主页](https://yale-lily.github.io/sparc/) 下载数据，把解压的 `sparc/` 放在 `work/sparc/data/` 下。目录内应有 `dev.json`、`train.json`、`tables.json` 和 `database/`。在 `outputs/sparc_api_lab/` 里复制 `.env.example` 为 `.env`，填入自己的 `DASHSCOPE_API_KEY`。启动器首次运行会从千问示例生成 `config.local.json`。

```powershell
python start_rsi.py
```

默认界面地址是 [http://127.0.0.1:3000/rsi](http://127.0.0.1:3000/rsi)，后端端口是 8765。保持启动终端打开，用 Ctrl+C 停止。端口被占用时可用 `python start_rsi.py --frontend-port 3100 --backend-port 8865`。启动器通过环境变量传递后端地址和令牌文件路径，令牌由本地后端自动生成。

RSI 的实验入口依赖本包的自建后端。原前端中保留的其他页面若需 HorseCoder 原平台服务，可自行配置 `RSI_DIALOGUE_UPSTREAM`、`RSI_IM_UPSTREAM` 和 `RSI_STORAGE_UPSTREAM`。实验入口无需配置这三个服务。

## 复现实验

安装官方 Test Suite 数据库与指定版本的评估器后，在 RSI 服务运行期间另开一个终端执行：

```powershell
python run_dev.py --comparison single-dual --run-prefix dev_single_dual
```

详细的数据下载、评分口径和复现说明见 [实验 README](README-benchmark.md)。结果见 [单、双 Agent 对照表](results/single-dual.md)。两组各跑 1,203 轮。通过 CLI 运行全量实验时，后端使用默认端口 8765。

## 公开发布前

此包包含桌面 RSI 的原前端源码和本地 `@horsecoder/flow` 依赖。打包时未在原前端根目录发现许可证文件。请先确认前端代码、素材和随包依赖的再分发授权，再把完整包推到公开仓库。需要 GitHub 许可证时，由权利人确定适用的许可证。数据集与评估器通过各自上游获取。
