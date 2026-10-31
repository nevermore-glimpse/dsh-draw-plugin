# dsh-draw-plugin

DeepSeek Harness 画图插件：侧边栏整页面板，写提示词即出图，Q版/正常两档画风，五个出图引擎可选（免费 Pollinations / 硅基流动 Kolors / 任意 OpenAI 兼容接口 / 本地 SD.cpp / 本地 ComfyUI），Key 由使用者自己填。

本仓库是 [muze63096/dsh-draw-plugin](https://github.com/muze63096/dsh-draw-plugin) v0.5.6 轻量版的适配版，针对 DSH **0.2.1-rc.2**（Electron 桌面版）做了三处修复：

1. **shell 服务兼容层** — DSH 0.2.1-rc.2 的 `ctx.shell` 只有 `resolve()` + `execute()`，本版补上了这条执行路径（保留旧版 `run`/`start` 回退，兼容老宿主）；
2. **bash 启动语法** — 本机执行器是 bash，引擎启动命令不再需要 PowerShell 的 `&` 调用运算符；
3. **LoRA 走结构化字段** — 新版 [stable-diffusion.cpp](https://github.com/leejet/stable-diffusion.cpp) sd-server 明确废弃了提示词里的 `<lora:...>` 标签，本版改用 `sdapi /sdapi/v1/txt2img` 的结构化 `lora: [{path, multiplier}]` 字段挂载，面板提供「读取 LoRA」按钮（走 `/sdapi/v1/loras`）、LoRA 下拉框与权重输入。

## 安装

DSH 桌面版（desktop profile 由 Electron 管理，CLI 会拒装），手动方式与 `dsh-pollinations-free` 同款：

**① 克隆到任意目录**（放哪都行，装完就可以删）：

```sh
git clone https://github.com/nevermore-glimpse/dsh-draw-plugin.git
```

**② 编辑 `%DSH_HOME%\profiles\desktop\package.json`**（默认 `%USERPROFILE%\.dsh\profiles\desktop\`）：

在 `dependencies` 里加（把路径换成你克隆的位置）：

```json
"dsh-draw-plugin": "link:C:/path/to/dsh-draw-plugin"
```

在 `dsh.profile.bundles` 数组里加：

```json
"dsh-draw-plugin"
```

**③ 在 profile 目录跑 pnpm install**：

```sh
cd %USERPROFILE%\.dsh\profiles\desktop
pnpm install
```

**④ 重启 DSH 进程**（只刷新浏览器页面不够），再刷新页面。

> 装好后 `E:\` 或克隆目录可以删除/移动——pnpm 会把包内容物化进 profile 的 `node_modules`（link: 在 install 时解析，之后独立存在）。但**改插件代码时**要保留源目录并重新 `pnpm install`。

或者用 **github: 协议**直接把仓库作为依赖（无需本地克隆）：

```json
"dsh-draw-plugin": "github:nevermore-glimpse/dsh-draw-plugin"
```

## 使用（本地 SD.cpp 引擎）

1. 面板引擎选「本地 SD.cpp（轻量）」；
2. 引擎目录填 `C:\Users\13122\.dsh\sdcpp`（或留空用默认），点「启动」——插件读取安装目录的 `sd-args.txt` 作为启动参数，格式示例：

   ```
   -m "E:\models\dreamshaper_8LCM-iq4_nl-imv2.gguf" --lora-model-dir "E:\models" --listen-ip 127.0.0.1 --listen-port 1234 --vae-tiling --diffusion-fa
   ```

3. 点「读取 LoRA」列出 `--lora-model-dir` 里的 LoRA，下拉选择、填权重（0.8 起）；
4. LCM 蒸馏模型建议 **步数 8 / CFG 1**，普通模型 25 步 / CFG 7。

> 注意：SDXL LoRA 配 SD1.5 底模会不兼容，选错会报错或出糊图。

## License

MIT
