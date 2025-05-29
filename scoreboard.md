# 📊 Paper API Scoreboard 完整指南

## 📋 目录
- [📊 Scoreboard 核心接口](#-scoreboard-核心接口)
- [🎯 Objective 目标管理](#-objective-目标管理)
- [👥 Team 团队系统](#-team-团队系统)
- [📈 Score 分数管理](#-score-分数管理)
- [📏 Criteria 评分标准](#-criteria-评分标准)
- [🖥️ DisplaySlot 显示位置](#️-displayslot-显示位置)
- [⚙️ ScoreboardManager 管理器](#️-scoreboardmanager-管理器)
- [🎮 实际应用示例](#-实际应用示例)

---

## 📊 Scoreboard 核心接口

### 🔧 主要方法

#### 📝 Objective 管理
```java
// 注册新目标 (推荐使用Adventure Component)
@NotNull Objective registerNewObjective(@NotNull String name, @NotNull Criteria criteria, @Nullable Component displayName)
@NotNull Objective registerNewObjective(@NotNull String name, @NotNull Criteria criteria, @Nullable Component displayName, @NotNull RenderType renderType)

// 获取目标
@Nullable Objective getObjective(@NotNull String name)
@Nullable Objective getObjective(@NotNull DisplaySlot slot)
@NotNull Set<Objective> getObjectives()
@NotNull Set<Objective> getObjectivesByCriteria(@NotNull Criteria criteria)

// 清除显示槽位
void clearSlot(@NotNull DisplaySlot slot)
```

#### 👥 Team 管理
```java
// 注册新团队
@NotNull Team registerNewTeam(@NotNull String name)

// 获取团队
@Nullable Team getTeam(@NotNull String teamName)
@NotNull Set<Team> getTeams()
@Nullable Team getPlayerTeam(@NotNull OfflinePlayer player)
@Nullable Team getEntryTeam(@NotNull String entry)
@Nullable Team getEntityTeam(Entity entity)
```

#### 📈 Score 管理
```java
// 获取分数
@NotNull Set<Score> getScores(@NotNull OfflinePlayer player)
@NotNull Set<Score> getScores(@NotNull String entry)
@NotNull Set<Score> getScoresFor(Entity entity)

// 重置分数
void resetScores(@NotNull OfflinePlayer player)
void resetScores(@NotNull String entry)
void resetScoresFor(Entity entity)

// 获取所有条目
@NotNull Set<String> getEntries()
```

---

## 🎯 Objective 目标管理

### 🔧 核心方法

#### 📝 基本属性
```java
// 获取名称和显示名称
@NotNull String getName()
@NotNull Component displayName()
void displayName(@Nullable Component displayName)

// 获取评分标准
@NotNull Criteria getTrackedCriteria()

// 获取关联的记分板
@Nullable Scoreboard getScoreboard()
```

#### 🖥️ 显示设置
```java
// 设置显示槽位
void setDisplaySlot(@Nullable DisplaySlot slot)
@Nullable DisplaySlot getDisplaySlot()

// 设置渲染类型
void setRenderType(@NotNull RenderType renderType)
@NotNull RenderType getRenderType()
```

#### 📊 分数操作
```java
// 获取分数对象
@NotNull Score getScore(@NotNull OfflinePlayer player)
@NotNull Score getScore(@NotNull String entry)
@NotNull Score getScoreFor(Entity entity)

// 检查是否可修改
boolean isModifiable()
```

#### 🔢 数字格式化
```java
// 设置数字格式
@Nullable NumberFormat numberFormat()
void numberFormat(@Nullable NumberFormat format)
```

#### ⚡ 自动更新
```java
// 自动更新显示
boolean willAutoUpdateDisplay()
void setAutoUpdateDisplay(boolean autoUpdateDisplay)
```

#### 🗑️ 注销
```java
// 注销目标
void unregister()
```

---

## 👥 Team 团队系统

### 🔧 核心方法

#### 📝 基本属性
```java
// 获取团队名称和显示名称
@NotNull String getName()
@NotNull Component displayName()
void displayName(@Nullable Component displayName)

// 获取关联的记分板
@Nullable Scoreboard getScoreboard()
```

#### 🎨 外观设置
```java
// 颜色设置
@NotNull TextColor color()
void color(@Nullable NamedTextColor color)
boolean hasColor()

// 前缀和后缀
@NotNull Component prefix()
void prefix(@Nullable Component prefix)
@NotNull Component suffix()
void suffix(@Nullable Component suffix)
```

#### 👤 成员管理
```java
// 添加成员
void addPlayer(@NotNull OfflinePlayer player)
void addEntry(@NotNull String entry)
void addEntity(Entity entity)
void addEntries(Collection<String> entries)
void addEntities(Collection<Entity> entities)

// 移除成员
boolean removePlayer(@NotNull OfflinePlayer player)
boolean removeEntry(@NotNull String entry)
boolean removeEntity(Entity entity)
boolean removeEntries(Collection<String> entries)
boolean removeEntities(Collection<Entity> entities)

// 检查成员
boolean hasPlayer(@NotNull OfflinePlayer player)
boolean hasEntry(@NotNull String entry)
boolean hasEntity(Entity entity)

// 获取成员信息
@NotNull Set<String> getEntries()
int getSize()
```

#### ⚙️ 团队选项
```java
// 设置团队选项
@NotNull Team.OptionStatus getOption(@NotNull Team.Option option)
void setOption(@NotNull Team.Option option, @NotNull Team.OptionStatus status)

// 友军伤害
boolean allowFriendlyFire()
void setAllowFriendlyFire(boolean enabled)

// 隐身队友可见性
boolean canSeeFriendlyInvisibles()
void setCanSeeFriendlyInvisibles(boolean enabled)
```

#### 🗑️ 注销
```java
// 注销团队
void unregister()
```

### 🎛️ Team.Option 选项类型
- `NAME_TAG_VISIBILITY` - 名称标签可见性
- `DEATH_MESSAGE_VISIBILITY` - 死亡消息可见性
- `COLLISION_RULE` - 碰撞规则

### 🎚️ Team.OptionStatus 选项状态
- `ALWAYS` - 总是
- `NEVER` - 从不
- `FOR_OTHER_TEAMS` - 对其他团队
- `FOR_OWN_TEAM` - 对自己团队

---

## 📈 Score 分数管理

### 🔧 核心方法

#### 📝 基本属性
```java
// 获取条目和目标
@NotNull String getEntry()
@NotNull Objective getObjective()
@Nullable Scoreboard getScoreboard()
```

#### 📊 分数操作
```java
// 获取和设置分数
int getScore()
void setScore(int score)

// 检查分数状态
boolean isScoreSet()
void resetScore()
```

#### 🎯 触发器功能
```java
// 触发器设置 (仅适用于 Criteria.TRIGGER)
boolean isTriggerable()
void setTriggerable(boolean triggerable)
```

#### 🏷️ 自定义名称
```java
// 自定义显示名称
@Nullable Component customName()
void customName(@Nullable Component customName)
```

#### 🔢 数字格式化
```java
// 数字格式设置
@Nullable NumberFormat numberFormat()
void numberFormat(@Nullable NumberFormat format)
```

---

## 📏 Criteria 评分标准

### 🎯 内置标准

#### 🔧 基础标准
- `DUMMY` - 虚拟标准，不会自动改变
- `TRIGGER` - 触发器标准，通过 /trigger 命令改变

#### 📊 玩家统计
- `DEATH_COUNT` - 死亡次数
- `PLAYER_KILL_COUNT` - 击杀玩家次数
- `TOTAL_KILL_COUNT` - 总击杀次数

#### ❤️ 玩家状态
- `HEALTH` - 生命值 (0-20)
- `FOOD` - 饥饿值 (0-20)
- `AIR` - 氧气值 (0-300)
- `ARMOR` - 护甲值 (0-20)
- `XP` - 经验点数
- `LEVEL` - 经验等级

#### 🎨 团队击杀统计
- `TEAM_KILL_[COLOR]` - 击杀指定颜色团队成员次数
- `KILLED_BY_TEAM_[COLOR]` - 被指定颜色团队成员击杀次数

### 🔧 自定义标准
```java
// 创建自定义标准
@NotNull Criteria create(@NotNull String name)

// 基于统计数据的标准
@NotNull Criteria statistic(@NotNull Statistic statistic)
@NotNull Criteria statistic(@NotNull Statistic statistic, @NotNull Material material)
@NotNull Criteria statistic(@NotNull Statistic statistic, @NotNull EntityType entityType)
```

### 📋 标准属性
```java
// 获取标准信息
@NotNull String getName()
boolean isReadOnly()
@NotNull RenderType getDefaultRenderType()
```

---

## 🖥️ DisplaySlot 显示位置

### 📍 可用位置
- `PLAYER_LIST` - 玩家列表 (Tab键)
- `SIDEBAR` - 侧边栏 (右侧)
- `BELOW_NAME` - 名称下方

### 🎨 团队特定侧边栏
- `SIDEBAR_TEAM_[COLOR]` - 特定颜色团队的侧边栏
  - 支持所有16种Minecraft颜色

### 🔧 实用方法
```java
// 获取显示槽位ID
String getId()
```

---

## ⚙️ ScoreboardManager 管理器

### 🔧 核心方法
```java
// 获取主记分板 (服务器默认)
@NotNull Scoreboard getMainScoreboard()

// 创建新记分板
@NotNull Scoreboard getNewScoreboard()
```

---

## 🎮 实际应用示例

### 🏆 创建玩家统计面板
```java
// 获取记分板管理器
ScoreboardManager manager = Bukkit.getScoreboardManager();
Scoreboard board = manager.getNewScoreboard();

// 创建目标
Objective objective = board.registerNewObjective(
    "stats", 
    Criteria.DUMMY, 
    Component.text("玩家统计").color(NamedTextColor.GOLD)
);
objective.setDisplaySlot(DisplaySlot.SIDEBAR);

// 设置分数
objective.getScore("击杀数").setScore(10);
objective.getScore("死亡数").setScore(3);
objective.getScore("等级").setScore(25);

// 应用到玩家
player.setScoreboard(board);
```

### 👥 创建团队PVP系统
```java
Scoreboard board = manager.getNewScoreboard();

// 创建红队
Team redTeam = board.registerNewTeam("red");
redTeam.displayName(Component.text("红队").color(NamedTextColor.RED));
redTeam.color(NamedTextColor.RED);
redTeam.prefix(Component.text("[红队] ").color(NamedTextColor.RED));
redTeam.setAllowFriendlyFire(false);

// 创建蓝队
Team blueTeam = board.registerNewTeam("blue");
blueTeam.displayName(Component.text("蓝队").color(NamedTextColor.BLUE));
blueTeam.color(NamedTextColor.BLUE);
blueTeam.prefix(Component.text("[蓝队] ").color(NamedTextColor.BLUE));
blueTeam.setAllowFriendlyFire(false);

// 添加玩家到团队
redTeam.addPlayer(player1);
blueTeam.addPlayer(player2);
```

### 📊 实时健康显示
```java
// 创建健康显示目标
Objective healthObj = board.registerNewObjective(
    "health", 
    Criteria.HEALTH, 
    Component.text("❤").color(NamedTextColor.RED)
);
healthObj.setDisplaySlot(DisplaySlot.BELOW_NAME);
healthObj.setRenderType(RenderType.HEARTS);
```

### 🎯 触发器系统
```java
// 创建触发器目标
Objective trigger = board.registerNewObjective(
    "shop", 
    Criteria.TRIGGER, 
    Component.text("商店")
);

// 设置玩家触发器
Score playerScore = trigger.getScore(player.getName());
playerScore.setScore(0);
playerScore.setTriggerable(true);

// 玩家可以使用: /trigger shop set 1
```

### 🏅 排行榜系统
```java
// 创建排行榜
Objective leaderboard = board.registerNewObjective(
    "kills", 
    Criteria.PLAYER_KILL_COUNT, 
    Component.text("击杀排行榜").color(NamedTextColor.YELLOW)
);
leaderboard.setDisplaySlot(DisplaySlot.SIDEBAR);

// 自动跟踪玩家击杀数
// 分数会自动更新，无需手动设置
```

---

## 🎨 高级功能

### 🔢 数字格式化
```java
// 使用Paper的NumberFormat API
import io.papermc.paper.scoreboard.numbers.NumberFormat;

// 设置固定文本格式
objective.numberFormat(NumberFormat.fixed(Component.text("分")));

// 设置空白格式 (隐藏数字)
objective.numberFormat(NumberFormat.blank());
```

### 🎭 动态更新
```java
// 启用自动更新显示
objective.setAutoUpdateDisplay(true);

// 动态更新分数
Bukkit.getScheduler().runTaskTimer(plugin, () -> {
    objective.getScore("在线玩家").setScore(Bukkit.getOnlinePlayers().size());
}, 0L, 20L); // 每秒更新
```

### 🎪 多记分板管理
```java
// 为不同玩家设置不同记分板
Map<UUID, Scoreboard> playerBoards = new HashMap<>();

public void setPlayerBoard(Player player, String type) {
    Scoreboard board = createBoardByType(type);
    playerBoards.put(player.getUniqueId(), board);
    player.setScoreboard(board);
}
```

---

## 💡 最佳实践

1. **🎯 使用Adventure Component** - 优先使用现代的Component API而不是过时的String方法
2. **♻️ 重用记分板** - 避免频繁创建新记分板，考虑重用现有的
3. **🔄 及时清理** - 在插件卸载时正确注销目标和团队
4. **⚡ 性能优化** - 避免过于频繁的分数更新
5. **🎨 用户体验** - 合理使用颜色和格式化，保持界面清晰
6. **🛡️ 异常处理** - 处理记分板相关的IllegalStateException
7. **📊 数据持久化** - 考虑将重要的记分板数据保存到文件或数据库

---

## 🔗 相关链接
- [Paper API 官方文档](https://jd.papermc.io/paper/1.21.5/)
- [Adventure Text Component](https://docs.advntr.dev/text.html)
- [Minecraft Wiki - 记分板](https://minecraft.wiki/w/Scoreboard)
