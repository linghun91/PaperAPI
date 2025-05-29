# 📊 Paper API Boss 系统完整文档

## 📋 概述
Paper API 的 boss 包提供了强大的 Boss Bar 系统和末影龙战斗管理功能，允许开发者创建自定义的 Boss Bar 显示和管理末影龙战斗状态。

## 🎯 核心接口和类

### 🔥 BossBar 接口
主要的 Boss Bar 接口，提供完整的 Boss Bar 管理功能。

#### 📝 基本属性方法
- `String getTitle()` - 获取 Boss Bar 标题
- `void setTitle(String title)` - 设置 Boss Bar 标题
- `BarColor getColor()` - 获取 Boss Bar 颜色
- `void setColor(BarColor color)` - 设置 Boss Bar 颜色
- `BarStyle getStyle()` - 获取 Boss Bar 样式
- `void setStyle(BarStyle style)` - 设置 Boss Bar 样式
- `double getProgress()` - 获取进度值 (0.0-1.0)
- `void setProgress(double progress)` - 设置进度值 (0.0-1.0)

#### 👥 玩家管理方法
- `void addPlayer(Player player)` - 添加玩家到 Boss Bar
- `void removePlayer(Player player)` - 从 Boss Bar 移除玩家
- `void removeAll()` - 移除所有玩家
- `List<Player> getPlayers()` - 获取所有观看的玩家列表

#### 🏳️ 标志管理方法
- `void addFlag(BarFlag flag)` - 添加可选标志
- `void removeFlag(BarFlag flag)` - 移除标志
- `boolean hasFlag(BarFlag flag)` - 检查是否有指定标志

#### 👁️ 可见性控制
- `void setVisible(boolean visible)` - 设置可见性
- `boolean isVisible()` - 检查是否可见
- `void show()` - 显示 Boss Bar (已弃用，使用 setVisible)
- `void hide()` - 隐藏 Boss Bar (已弃用，使用 setVisible)

### 🎨 BarColor 枚举
定义 Boss Bar 的颜色选项：
- `PINK` - 粉色
- `BLUE` - 蓝色
- `RED` - 红色
- `GREEN` - 绿色
- `YELLOW` - 黄色
- `PURPLE` - 紫色
- `WHITE` - 白色

### 📏 BarStyle 枚举
定义 Boss Bar 的样式：
- `SOLID` - 实心条 (无分段)
- `SEGMENTED_6` - 分为 6 段
- `SEGMENTED_10` - 分为 10 段
- `SEGMENTED_12` - 分为 12 段
- `SEGMENTED_20` - 分为 20 段

### 🚩 BarFlag 枚举
定义 Boss Bar 的特殊效果标志：
- `DARKEN_SKY` - 使天空变暗 (类似凋零战斗)
- `PLAY_BOSS_MUSIC` - 播放末影龙 Boss 音乐
- `CREATE_FOG` - 在世界周围创建雾气

### 🔑 KeyedBossBar 接口
继承自 BossBar 和 Keyed 接口，表示具有 NamespacedKey 的自定义 Boss Bar。
- 继承所有 BossBar 方法
- 实现 Keyed 接口的 `getKey()` 和 `key()` 方法

## 🐉 末影龙战斗系统

### 🏟️ DragonBattle 接口
管理末影龙战斗状态的核心接口。

#### 🐲 龙实体管理
- `EnderDragon getEnderDragon()` - 获取当前活跃的末影龙 (死亡时返回 null)
- `BossBar getBossBar()` - 获取龙战斗的 Boss Bar

#### 🚪 传送门管理
- `Location getEndPortalLocation()` - 获取末地传送门位置
- `boolean generateEndPortal(boolean withPortals)` - 生成末地传送门

#### 🔄 重生系统
- `boolean hasBeenPreviouslyKilled()` - 检查龙是否之前被杀死过
- `void setPreviouslyKilled(boolean previouslyKilled)` - 设置龙是否被杀死过
- `void initiateRespawn()` - 启动重生序列 (如同玩家放置4个末影水晶)
- `boolean initiateRespawn(Collection<EnderCrystal> enderCrystals)` - 使用指定水晶启动重生

#### 📊 重生阶段管理
- `DragonBattle.RespawnPhase getRespawnPhase()` - 获取当前重生阶段
- `boolean setRespawnPhase(DragonBattle.RespawnPhase phase)` - 设置重生阶段

#### 💎 水晶管理
- `void resetCrystals()` - 重置黑曜石柱上的水晶
- `List<EnderCrystal> getRespawnCrystals()` - 获取用于重生的水晶
- `List<EnderCrystal> getHealingCrystals()` - 获取治疗龙的水晶

#### 🌀 传送门管理
- `int getGatewayCount()` - 获取传送门数量 (0-20)
- `boolean spawnNewGateway()` - 使用默认机制生成新传送门
- `void spawnNewGateway(Position position)` - 在指定位置生成传送门

### 🔄 DragonBattle.RespawnPhase 枚举
表示龙重生过程的阶段：
- `NONE` - 无重生进行中
- `START` - 水晶光束向上指向天空
- `PREPARING_TO_SUMMON_PILLARS` - 水晶光束保持向上
- `SUMMONING_PILLARS` - 光束在柱子间传递，重新生成水晶
- `SUMMONING_DRAGON` - 所有水晶指向天空，即将召唤龙
- `END` - 重生序列结束，龙被召唤

## 🎮 实用功能示例

### 🎯 任务进度显示
```java
// 创建任务进度 Boss Bar
BossBar taskBar = Bukkit.createBossBar("任务进度", BarColor.GREEN, BarStyle.SEGMENTED_10);
taskBar.setProgress(0.7); // 70% 完成
taskBar.addPlayer(player);
```

### ⚔️ Boss 战斗状态
```java
// 创建 Boss 战斗 Bar
BossBar bossBar = Bukkit.createBossBar("末影龙", BarColor.PURPLE, BarStyle.SOLID);
bossBar.addFlag(BarFlag.DARKEN_SKY);
bossBar.addFlag(BarFlag.PLAY_BOSS_MUSIC);
bossBar.setProgress(boss.getHealth() / boss.getMaxHealth());
```

### 🌟 特殊效果展示
```java
// 创建带特效的 Boss Bar
BossBar effectBar = Bukkit.createBossBar("神秘事件", BarColor.WHITE, BarStyle.SEGMENTED_6);
effectBar.addFlag(BarFlag.CREATE_FOG);
effectBar.addFlag(BarFlag.DARKEN_SKY);
```

### 🐉 末影龙战斗控制
```java
// 获取末影龙战斗
DragonBattle battle = world.getEnderDragonBattle();
if (battle != null) {
    // 启动龙重生
    battle.initiateRespawn();
    
    // 监控重生阶段
    DragonBattle.RespawnPhase phase = battle.getRespawnPhase();
    
    // 生成新传送门
    battle.spawnNewGateway();
}
```

## 🎨 创意应用场景

### 🏆 竞技场系统
- 使用不同颜色显示队伍状态
- 分段样式显示回合进度
- 特殊效果增强氛围

### 📈 服务器状态显示
- 实时显示在线玩家数
- 服务器性能监控
- 事件倒计时

### 🎪 小游戏集成
- 游戏时间显示
- 生命值/能量条
- 特殊技能冷却

### 🌍 世界事件
- 全服活动进度
- 季节性事件倒计时
- 动态天气效果配合

## ⚠️ 注意事项

1. **性能考虑**: Boss Bar 会向所有相关玩家发送数据包，避免频繁更新
2. **玩家管理**: 及时清理不需要的玩家，避免内存泄漏
3. **进度值范围**: 进度值必须在 0.0-1.0 之间
4. **标志组合**: 某些标志组合可能产生意想不到的视觉效果
5. **末影龙战斗**: 只在末地环境的世界中可用

## 🔧 最佳实践

1. **统一管理**: 创建 Boss Bar 管理器统一处理所有 Boss Bar
2. **资源清理**: 在插件卸载时清理所有 Boss Bar
3. **玩家离线处理**: 监听玩家离线事件，自动移除 Boss Bar
4. **配置化**: 将 Boss Bar 样式和文本配置化
5. **事件驱动**: 使用事件系统更新 Boss Bar 状态
