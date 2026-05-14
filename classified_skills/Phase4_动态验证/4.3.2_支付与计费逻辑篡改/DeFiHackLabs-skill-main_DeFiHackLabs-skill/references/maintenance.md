# 维护指南

用于维护与扩展本技能体系。仅在需要更新案例、子技能或框架时加载。

## 目录结构约束

- 仅保留 `SKILL.md`、`references/`、`sub-skills/`
- `SKILL.md` 只放核心流程与导航，避免堆砌
- 参考资料放到 `references/`，子主题放到 `sub-skills/`
- 避免新增根目录说明文档（README/总结/迁移等）

## 更新流程

1. 更新案例
   - 在 `references/case-index.md` 追加记录
   - 如需调整分类，更新 `references/vcat-framework.md`
   - 同步对应子技能统计（案例数、平均损失等）

2. 更新子技能
   - 保持 YAML frontmatter（仅 `name` + `description`）
   - 保持结构一致（识别 → 模式 → 防御 → 典型案例 → 统计）
   - 仅引用存在的参考文件

3. 更新框架与工具
   - IPDOR 或 VCAT 有变化时更新对应 references 文档
   - 工具清单变更写入 `references/tools-and-resources.md`

## 质量检查

- `SKILL.md` 保持精炼（< 500 行）
- 链接只指向存在的文件
- 重复文件（.backup / -optimized）必须移除
- 新增参考文档要在 `SKILL.md` 中有入口

## 建议的版本记录

- 在相关文件底部更新 `最后更新` 日期
- 仅在数据口径变化时更新统计数字
