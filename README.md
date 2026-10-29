# jq · 配方与重建

**用可迭代的配方描述 jq，再独立生成并检验实现。**

[配方](https://github.com/recipkg/jq) · [重建源码](https://github.com/recipkg/jq-source) · [标准库](https://github.com/recipkg/standard-library) · [验收样本](https://github.com/recipkg/standard-library-verification)

![从软件与依赖整理配方，再交给 Agent 重建](https://raw.githubusercontent.com/recipkg/recipkg/main/assets/recipkg-cover.png)

> **进行中：**配方和源码都尚未完成。首轮独立重建只通过部分样本，不能替代 jq 使用；具体通过与失败记录见 [重建状态](generated/jq-source/STATUS.md)。

## 目录

| 位置 | 用途 |
| --- | --- |
| `recipes/jq/` | 固定提交的 Markdown 配方仓库，描述行为、依赖和验收。 |
| `generated/jq-source/` | 固定提交的独立重建源码仓库。源码不进入配方归档。 |

两者均为 Git 子模块；更新时分别提交并推送各自仓库，再更新此处的固定提交。

## 构建提示词

```sh
git clone --recurse-submodules https://github.com/recipkg/jq-workspace.git
cd jq-workspace
work_dir="$(mktemp -d)"
recipkg pack recipes/jq -o "$work_dir/jq.recipe.tar.gz"
recipkg check "$work_dir/jq.recipe.tar.gz"
recipkg build "$work_dir/jq.recipe.tar.gz" --output "$work_dir/build.md"
printf '构建提示词：%s\n' "$work_dir/build.md"
```

`build` 只生成包含递归依赖的提示词，不调用模型或执行软件。归档检查通过不等于重建通过。

## 验证重建

使用 Python 3.11+ 与另行检出的 `standard-library-verification`：

```sh
candidate="$PWD/generated/jq-source/jq"
cd /path/to/standard-library-verification
RECIPKG_TEST_JQ="$candidate" python3 -m unittest discover -s tests -p test_recipes.py -k JqSamples -v
```

该命令只覆盖有限样本。记录实际通过、失败及跳过的项；即使通过也不能当作完整兼容。集合与聚合过滤器还可对照已安装的上游版本：

```sh
RECIPKG_TEST_JQ="$candidate" RECIPKG_TEST_JQ_REFERENCE=/usr/bin/jq python3 -m unittest discover -s tests -p test_jq_collections.py -v
RECIPKG_TEST_JQ="$candidate" RECIPKG_TEST_JQ_REFERENCE=/usr/bin/jq python3 -m unittest discover -s tests -p test_jq_aggregation.py -v
```

确认 `/usr/bin/jq` 是希望对照的版本；更多差分命令与限制见[验收样本仓库](https://github.com/recipkg/standard-library-verification)。
