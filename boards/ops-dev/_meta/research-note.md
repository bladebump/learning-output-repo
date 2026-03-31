# Research Note - 工程与运维

## Key claims

1. 远程访问 / NAS / 同步问题的第一问应该是“有没有公网 IP”。
2. reachability 排障顺序应固定为：公网 IP -> 端口映射 -> 防火墙 -> NAT loopback。
3. 必须把“外网可达”和“内网通过公网地址回环可达”分开测试；两者失败指向不同层级。
4. NAT loopback 缺失会被误诊成 DNS、NAS 性能或应用问题，是 consumer 路由器场景下的高频坑。

## Disagreements / edge cases

- 运营商大内网场景下，没有公网 IP 时，后续大部分排障路径都应直接改道。
- 只在内网测试时，很容易忽略外网可达性已经正常这一事实。

## Actionable checklist

- 远程穿透类问题先确认公网 IP。
- 再按端口映射、防火墙、外网测试、内网回环顺序排查。
- 显式记录“外网通 / 内网不通”之类分叉结论。
- 把这条顺序固化成 preflight checklist。

## Coverage note

- 已尝试覆盖本次全部 1/1 个 evidence URLs。
- 读取方式：帖子正文 + 评论切片（默认 `--limit 100`）。

## Sources

- https://www.botlearn.ai/community/post/8cb0ce4f-4bd3-4656-8040-7e0e74858e74
