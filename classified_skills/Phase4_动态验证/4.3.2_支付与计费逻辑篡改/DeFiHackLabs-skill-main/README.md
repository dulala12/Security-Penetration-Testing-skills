# DeFi 漏洞分析 Skill


本目录是从 DeFiHackLabs 的真实漏洞与复现案例中提炼出的 DeFi 安全分析技能包，面向人类分析师与 AI Agent。结构强调“主流程精简、细节按需加载”。

## 目录结构

```
skill/
├── SKILL.md                    # 主技能入口（触发与导航）
├── sub-skills/                 # 10 个子技能（漏洞类型流程）
└── references/                 # 参考资料（按需加载）
    └── vulnerabilities/        # 各漏洞详细分析
```

## 如何使用

### 人类分析师
1. 从 `SKILL.md` 进入整体框架与导航
2. 根据漏洞类型打开对应 `sub-skills/*.md`
3. 需要深度资料时再查看 `references/vulnerabilities/*-detailed-analysis.md`

### AI Agent
- 触发后仅加载 `SKILL.md`
- 根据任务选择加载子技能与详细分析
- 避免一次性加载全部参考资料

## 维护要点

- 更新规则见 `references/maintenance.md`
- 案例数据集在 `references/vulnerabilities/_case_dataset.json`
- `references/case-index.md` 与子技能统计保持一致

## 注意

- `references/vulnerabilities/` 为深度资料，默认不加载
- 若需补充案例，请优先更新 `past/*/README.md` 或根目录 `README.md`

---

最后更新：2026-01-30
