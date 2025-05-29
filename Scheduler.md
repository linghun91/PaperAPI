# 📅 Paper API Scheduler 完整指南

## 🌟 概述

Paper API 的 Scheduler 系统是一个强大的多线程任务调度框架，专为 Minecraft 服务器的多区域架构设计。它提供了四种不同类型的调度器，每种都针对特定的使用场景进行了优化。

## 📦 包结构

```
io.papermc.paper.threadedregions.scheduler
├── AsyncScheduler          - 异步任务调度器
├── EntityScheduler         - 实体任务调度器  
├── GlobalRegionScheduler   - 全局区域调度器
├── RegionScheduler         - 区域任务调度器
├── ScheduledTask          - 已调度任务接口
├── ScheduledTask.CancelledState    - 取消状态枚举
└── ScheduledTask.ExecutionState    - 执行状态枚举
```

---

## 🔄 AsyncScheduler - 异步任务调度器

### 📝 描述
用于调度异步执行的任务，这些任务在服务器tick进程之外运行，不会阻塞主线程。

### 🛠️ 核心方法

#### `runNow(Plugin plugin, Consumer<ScheduledTask> task)`
- **功能**: 立即异步执行任务
- **参数**: 
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
- **返回**: `ScheduledTask` 对象

#### `runDelayed(Plugin plugin, Consumer<ScheduledTask> task, long delay, TimeUnit unit)`
- **功能**: 延迟异步执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
  - `delay`: 延迟时间
  - `unit`: 时间单位
- **返回**: `ScheduledTask` 对象

#### `runAtFixedRate(Plugin plugin, Consumer<ScheduledTask> task, long initialDelay, long period, TimeUnit unit)`
- **功能**: 以固定频率重复执行异步任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
  - `initialDelay`: 初始延迟
  - `period`: 执行周期
  - `unit`: 时间单位
- **返回**: `ScheduledTask` 对象

#### `cancelTasks(Plugin plugin)`
- **功能**: 取消指定插件的所有任务
- **参数**: `plugin`: 指定的插件

### 🎯 适用场景
- 🌐 网络请求和API调用
- 💾 数据库操作
- 📁 文件I/O操作
- 🔄 后台数据处理

---

## 🎭 EntityScheduler - 实体任务调度器

### 📝 描述
专为实体设计的调度器，能够处理实体在世界间移动、传送、暂时移除等复杂状态变化。

### 🛠️ 核心方法

#### `execute(Plugin plugin, Runnable run, Runnable retired, long delay)`
- **功能**: 调度带延迟的任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `run`: 执行回调
  - `retired`: 实体被移除时的回调
  - `delay`: 延迟tick数
- **返回**: `boolean` - 是否成功调度

#### `run(Plugin plugin, Consumer<ScheduledTask> task, Runnable retired)`
- **功能**: 在下一tick执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
  - `retired`: 实体被移除时的回调
- **返回**: `ScheduledTask` 或 `null`

#### `runDelayed(Plugin plugin, Consumer<ScheduledTask> task, Runnable retired, long delayTicks)`
- **功能**: 延迟执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
  - `retired`: 实体被移除时的回调
  - `delayTicks`: 延迟tick数
- **返回**: `ScheduledTask` 或 `null`

#### `runAtFixedRate(Plugin plugin, Consumer<ScheduledTask> task, Runnable retired, long initialDelayTicks, long periodTicks)`
- **功能**: 以固定频率重复执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
  - `retired`: 实体被移除时的回调
  - `initialDelayTicks`: 初始延迟tick数
  - `periodTicks`: 执行周期tick数
- **返回**: `ScheduledTask` 或 `null`

### 🎯 适用场景
- 🏃 实体AI行为控制
- ⚡ 实体状态效果管理
- 🎯 实体跟踪和监控
- 🔄 实体动画和特效

---

## 🌍 GlobalRegionScheduler - 全局区域调度器

### 📝 描述
用于调度在全局区域执行的任务。全局区域负责维护世界时间、天气循环、睡眠跳过等全局功能。

### 🛠️ 核心方法

#### `execute(Plugin plugin, Runnable run)`
- **功能**: 在全局区域执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `run`: 要执行的任务

#### `run(Plugin plugin, Consumer<ScheduledTask> task)`
- **功能**: 在下一tick在全局区域执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
- **返回**: `ScheduledTask` 对象

#### `runDelayed(Plugin plugin, Consumer<ScheduledTask> task, long delayTicks)`
- **功能**: 延迟在全局区域执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
  - `delayTicks`: 延迟tick数
- **返回**: `ScheduledTask` 对象

#### `runAtFixedRate(Plugin plugin, Consumer<ScheduledTask> task, long initialDelayTicks, long periodTicks)`
- **功能**: 以固定频率在全局区域重复执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `task`: 要执行的任务
  - `initialDelayTicks`: 初始延迟tick数
  - `periodTicks`: 执行周期tick数
- **返回**: `ScheduledTask` 对象

#### `cancelTasks(Plugin plugin)`
- **功能**: 取消指定插件的所有全局任务
- **参数**: `plugin`: 指定的插件

### 🎯 适用场景
- 🌅 世界时间和天气管理
- 🎮 全局游戏规则控制
- 📊 服务器统计和监控
- 🔧 全局配置更新

---

## 🗺️ RegionScheduler - 区域任务调度器

### 📝 描述
根据位置调度任务到拥有该位置的区域执行。注意：不适用于实体任务，应使用EntityScheduler。

### 🛠️ 核心方法

#### `execute(Plugin plugin, World world, int chunkX, int chunkZ, Runnable run)`
- **功能**: 在指定区域执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `world`: 世界
  - `chunkX`: 区块X坐标
  - `chunkZ`: 区块Z坐标
  - `run`: 要执行的任务

#### `execute(Plugin plugin, Location location, Runnable run)`
- **功能**: 在指定位置的区域执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `location`: 位置
  - `run`: 要执行的任务

#### `run(Plugin plugin, World world, int chunkX, int chunkZ, Consumer<ScheduledTask> task)`
- **功能**: 在下一tick在指定区域执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `world`: 世界
  - `chunkX`: 区块X坐标
  - `chunkZ`: 区块Z坐标
  - `task`: 要执行的任务
- **返回**: `ScheduledTask` 对象

#### `runDelayed(Plugin plugin, World world, int chunkX, int chunkZ, Consumer<ScheduledTask> task, long delayTicks)`
- **功能**: 延迟在指定区域执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `world`: 世界
  - `chunkX`: 区块X坐标
  - `chunkZ`: 区块Z坐标
  - `task`: 要执行的任务
  - `delayTicks`: 延迟tick数
- **返回**: `ScheduledTask` 对象

#### `runAtFixedRate(Plugin plugin, World world, int chunkX, int chunkZ, Consumer<ScheduledTask> task, long initialDelayTicks, long periodTicks)`
- **功能**: 以固定频率在指定区域重复执行任务
- **参数**:
  - `plugin`: 拥有任务的插件
  - `world`: 世界
  - `chunkX`: 区块X坐标
  - `chunkZ`: 区块Z坐标
  - `task`: 要执行的任务
  - `initialDelayTicks`: 初始延迟tick数
  - `periodTicks`: 执行周期tick数
- **返回**: `ScheduledTask` 对象

### 🎯 适用场景
- 🏗️ 区块加载和卸载处理
- 🌱 方块更新和生长
- ⚡ 区域特定的游戏机制
- 🎆 区域特效和粒子系统

---

## 📋 ScheduledTask - 已调度任务接口

### 📝 描述
表示已调度到调度器的任务，提供任务状态查询和控制功能。

### 🛠️ 核心方法

#### `getOwningPlugin()`
- **功能**: 获取调度此任务的插件
- **返回**: `Plugin` 对象

#### `isRepeatingTask()`
- **功能**: 检查是否为重复任务
- **返回**: `boolean`

#### `cancel()`
- **功能**: 尝试取消任务
- **返回**: `CancelledState` 枚举

#### `getExecutionState()`
- **功能**: 获取当前执行状态
- **返回**: `ExecutionState` 枚举

#### `isCancelled()`
- **功能**: 检查任务是否已取消
- **返回**: `boolean`

---

## 🚫 CancelledState - 取消状态枚举

### 📝 状态值

- **`CANCELLED_BY_CALLER`** ✅ - 成功被调用者取消
- **`CANCELLED_ALREADY`** ⚠️ - 已经被取消
- **`RUNNING`** 🏃 - 正在执行，无法取消
- **`ALREADY_EXECUTED`** ✔️ - 已执行完成，无法取消
- **`NEXT_RUNS_CANCELLED`** 🔄 - 重复任务的未来执行已取消
- **`NEXT_RUNS_CANCELLED_ALREADY`** 🔄⚠️ - 重复任务的未来执行已经被取消

---

## ⚡ ExecutionState - 执行状态枚举

### 📝 状态值

- **`IDLE`** 💤 - 当前未执行，但可能在未来执行
- **`RUNNING`** 🏃 - 当前正在执行
- **`FINISHED`** ✅ - 非重复任务已完成执行
- **`CANCELLED`** ❌ - 不执行且不会在未来执行
- **`CANCELLED_RUNNING`** 🏃❌ - 重复任务正在执行但未来执行已取消

---

## 🎮 实际应用示例

### 🔥 异步数据处理
```java
// 异步处理玩家数据
asyncScheduler.runNow(plugin, task -> {
    // 执行耗时的数据库操作
    PlayerData data = database.loadPlayerData(player.getUniqueId());
    // 回到主线程更新UI
    Bukkit.getScheduler().runTask(plugin, () -> {
        updatePlayerUI(player, data);
    });
});
```

### 🎭 实体AI控制
```java
// 实体定期巡逻
entity.getScheduler().runAtFixedRate(plugin, task -> {
    if (entity.isValid()) {
        performPatrolBehavior(entity);
    } else {
        task.cancel();
    }
}, null, 0, 20); // 每秒执行一次
```

### 🌍 全局事件管理
```java
// 全局天气控制
globalScheduler.runAtFixedRate(plugin, task -> {
    for (World world : Bukkit.getWorlds()) {
        updateWeatherCycle(world);
    }
}, 0, 1200); // 每分钟检查一次
```

### 🗺️ 区域特定处理
```java
// 特定区域的方块更新
regionScheduler.runDelayed(plugin, location, task -> {
    updateChunkBlocks(location.getChunk());
}, 100); // 5秒后执行
```

---

## 🎯 最佳实践

### ✅ 推荐做法
1. **选择合适的调度器**: 根据任务类型选择最适合的调度器
2. **处理实体状态**: 使用EntityScheduler时总是提供retired回调
3. **资源清理**: 插件禁用时取消所有任务
4. **错误处理**: 在任务中添加适当的异常处理

### ❌ 避免事项
1. **不要在异步任务中直接操作Bukkit API**
2. **不要对实体使用RegionScheduler**
3. **不要忘记处理任务取消的情况**
4. **不要在retired回调中执行复杂操作**

---

## 🔧 性能优化建议

### 🚀 提升性能
- 合理使用异步调度器处理耗时操作
- 避免创建过多的短期任务
- 使用合适的执行周期，避免过于频繁的调度
- 及时取消不需要的重复任务

### 📊 监控和调试
- 定期检查任务执行状态
- 监控任务数量和执行时间
- 使用适当的日志记录任务执行情况

---

*📚 本文档基于 Paper API 1.21.5 版本编写，涵盖了Scheduler系统的所有核心功能和最佳实践。*
