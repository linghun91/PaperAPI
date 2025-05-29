# 📦 Paper API Datapack 完整指南

## 📋 概述

Paper API 提供了强大的 Datapack 管理系统，允许插件动态发现、注册、管理和控制数据包。这个系统支持从插件 JAR 内部、文件系统路径或 URI 位置加载自定义数据包。

## 🏗️ 核心接口架构

### 📄 Datapack 接口
主要的数据包快照接口，表示服务器上的数据包状态。

**核心方法：**
- `boolean isEnabled()` - 获取数据包启用状态
- `void setEnabled(boolean enabled)` - 设置数据包启用状态（会触发资源重载）
- `Component computeDisplayName()` - 计算 Minecraft 显示名称组件

**继承方法（来自 DiscoveredDatapack）：**
- `String getName()` - 获取数据包名称/ID
- `Component getTitle()` - 获取标题组件
- `Component getDescription()` - 获取描述组件
- `boolean isRequired()` - 检查是否为必需数据包
- `Datapack.Compatibility getCompatibility()` - 获取兼容性状态
- `Set<FeatureFlag> getRequiredFeatures()` - 获取所需功能标志
- `DatapackSource getSource()` - 获取数据包来源

### 🔧 DatapackManager 接口
数据包管理器，提供数据包的查询和管理功能。

**核心方法：**
- `void refreshPacks()` - 刷新可用和选定的数据包
- `@Nullable Datapack getPack(String name)` - 根据名称获取数据包
- `Collection<Datapack> getPacks()` - 获取所有可用数据包
- `Collection<Datapack> getEnabledPacks()` - 获取已启用的数据包

**获取方式：**
```java
DatapackManager manager = Bukkit.getServer().getDatapackManager();
```

### 📝 DatapackRegistrar 接口
数据包注册器，用于在数据包发现阶段注册新的数据包。

**核心发现方法：**
- `discoverPack(URI uri, String id)` - 从 URI 发现数据包
- `discoverPack(Path path, String id)` - 从路径发现数据包
- `discoverPack(URI uri, String id, Consumer<Configurer> configurer)` - 带配置的发现
- `discoverPack(PluginMeta pluginMeta, URI uri, String id, Consumer<Configurer> configurer)` - 指定插件元数据的发现

**管理方法：**
- `boolean hasPackDiscovered(String name)` - 检查数据包是否已发现
- `DiscoveredDatapack getDiscoveredPack(String name)` - 获取已发现的数据包
- `boolean removeDiscoveredPack(String name)` - 移除已发现的数据包
- `Map<String, DiscoveredDatapack> getDiscoveredPacks()` - 获取所有已发现的数据包

## ⚙️ 枚举类型

### 🔄 Datapack.Compatibility
数据包兼容性状态：
- `COMPATIBLE` - 兼容
- `TOO_OLD` - 版本过旧
- `TOO_NEW` - 版本过新

### 📍 Datapack.Position
数据包在加载顺序中的位置：
- `TOP` - 顶部位置
- `BOTTOM` - 底部位置

### 📂 DatapackSource
数据包来源类型：
- `DEFAULT` - 默认来源
- `BUILT_IN` - 内置数据包
- `FEATURE` - 功能数据包
- `WORLD` - 世界数据包
- `SERVER` - 服务器数据包
- `PLUGIN` - 插件数据包

## 🎛️ 配置器

### DatapackRegistrar.Configurer
用于配置数据包的额外选项：

**方法：**
- `title(Component title)` - 设置数据包标题
- `autoEnableOnServerStart(boolean autoEnable)` - 设置服务器启动时自动启用
- `position(boolean fixed, Datapack.Position position)` - 配置加载顺序位置

## 🚀 实际应用功能

### 🎮 动态内容管理
- **自定义配方系统** - 通过数据包动态添加/移除合成配方
- **世界生成定制** - 加载自定义世界生成器和生物群系
- **结构包管理** - 动态加载建筑结构和地牢设计

### 🎯 游戏机制扩展
- **战利品表定制** - 自定义怪物掉落和宝箱内容
- **进度系统** - 创建自定义成就和进度树
- **标签系统** - 定义物品、方块、实体标签分组

### 🔧 服务器管理
- **热重载支持** - 无需重启服务器即可更新内容
- **版本兼容检查** - 自动检测数据包兼容性
- **依赖管理** - 处理数据包间的依赖关系

### 🎨 内容创作
- **资源包集成** - 与客户端资源包协同工作
- **多语言支持** - 通过数据包实现本地化
- **模组兼容** - 为模组服务器提供数据包支持

## 📚 生命周期事件

### LifecycleEvents.DATAPACK_DISCOVERY
数据包发现事件，在服务器搜索数据包时触发。

**使用场景：**
- 插件启动时注册内置数据包
- 动态发现外部数据包文件
- 实现数据包的条件加载

**事件特点：**
- 只能在 `PluginBootstrap.bootstrap()` 中注册
- 每次数据包发现时都会触发
- 支持优先级设置

## 💡 最佳实践

### 🔒 安全考虑
- 验证数据包来源的可信性
- 检查符号链接权限（遵循 `allowed_symlinks.txt`）
- 处理 IO 异常和加载失败情况

### ⚡ 性能优化
- 合理使用 `refreshPacks()` 避免频繁刷新
- 缓存数据包查询结果
- 异步处理大型数据包加载

### 🎯 用户体验
- 提供清晰的数据包状态反馈
- 支持数据包的启用/禁用切换
- 实现数据包冲突检测和解决

## 🔗 相关 API 集成

### 与命令系统集成
- 通过 Brigadier 命令注册数据包管理命令
- 实现数据包状态查询和控制指令

### 与事件系统集成
- 监听数据包加载/卸载事件
- 响应数据包状态变化

### 与配置系统集成
- 将数据包设置保存到插件配置
- 支持配置文件驱动的数据包管理

## 💻 代码示例

### 🎯 基础数据包注册示例

```java
public class DatapackPlugin implements PluginBootstrap {
    @Override
    public void bootstrap(BootstrapContext context) {
        final LifecycleEventManager<BootstrapContext> manager = context.getLifecycleManager();

        manager.registerEventHandler(LifecycleEvents.DATAPACK_DISCOVERY, event -> {
            DatapackRegistrar registrar = event.registrar();

            try {
                // 从插件 JAR 内部注册数据包
                final URI uri = Objects.requireNonNull(
                    DatapackPlugin.class.getResource("/pack")
                ).toURI();

                registrar.discoverPack(uri, "my_custom_pack", configurer -> {
                    configurer.title(Component.text("我的自定义数据包"))
                             .autoEnableOnServerStart(true)
                             .position(false, Datapack.Position.TOP);
                });

            } catch (final URISyntaxException | IOException e) {
                throw new RuntimeException("数据包加载失败", e);
            }
        });
    }
}
```

### 🔧 数据包管理器使用示例

```java
public class DatapackCommand implements CommandExecutor {

    @Override
    public boolean onCommand(CommandSender sender, Command command, String label, String[] args) {
        DatapackManager manager = Bukkit.getServer().getDatapackManager();

        if (args.length == 0) {
            // 列出所有数据包
            listDatapacks(sender, manager);
            return true;
        }

        switch (args[0].toLowerCase()) {
            case "enable":
                if (args.length > 1) {
                    enableDatapack(sender, manager, args[1]);
                }
                break;
            case "disable":
                if (args.length > 1) {
                    disableDatapack(sender, manager, args[1]);
                }
                break;
            case "refresh":
                refreshDatapacks(sender, manager);
                break;
        }
        return true;
    }

    private void listDatapacks(CommandSender sender, DatapackManager manager) {
        sender.sendMessage("§6=== 数据包列表 ===");

        for (Datapack pack : manager.getPacks()) {
            String status = pack.isEnabled() ? "§a启用" : "§c禁用";
            String required = pack.isRequired() ? " §e[必需]" : "";
            String compatibility = getCompatibilityColor(pack.getCompatibility());

            sender.sendMessage(String.format("§f%s %s %s%s",
                pack.getName(), status, compatibility, required));
        }
    }

    private void enableDatapack(CommandSender sender, DatapackManager manager, String name) {
        Datapack pack = manager.getPack(name);
        if (pack == null) {
            sender.sendMessage("§c数据包 '" + name + "' 不存在！");
            return;
        }

        if (pack.isEnabled()) {
            sender.sendMessage("§e数据包 '" + name + "' 已经启用！");
            return;
        }

        pack.setEnabled(true);
        sender.sendMessage("§a成功启用数据包 '" + name + "'！");
    }

    private String getCompatibilityColor(Datapack.Compatibility compatibility) {
        return switch (compatibility) {
            case COMPATIBLE -> "§a[兼容]";
            case TOO_OLD -> "§c[过旧]";
            case TOO_NEW -> "§6[过新]";
        };
    }
}
```

### 🎮 高级数据包管理系统

```java
public class AdvancedDatapackManager {
    private final Plugin plugin;
    private final Map<String, DatapackInfo> managedPacks = new HashMap<>();

    public AdvancedDatapackManager(Plugin plugin) {
        this.plugin = plugin;
        loadConfiguration();
    }

    public void registerConditionalDatapack(String packId, Supplier<Boolean> condition) {
        DatapackManager manager = Bukkit.getServer().getDatapackManager();

        // 定期检查条件
        Bukkit.getScheduler().runTaskTimer(plugin, () -> {
            Datapack pack = manager.getPack(packId);
            if (pack != null) {
                boolean shouldEnable = condition.get();
                if (pack.isEnabled() != shouldEnable) {
                    pack.setEnabled(shouldEnable);
                    plugin.getLogger().info(String.format(
                        "数据包 %s 已%s（条件触发）",
                        packId, shouldEnable ? "启用" : "禁用"
                    ));
                }
            }
        }, 20L, 100L); // 每5秒检查一次
    }

    public void createDatapackGroup(String groupName, List<String> packIds) {
        // 创建数据包组，统一管理
        managedPacks.put(groupName, new DatapackInfo(groupName, packIds));
    }

    public void enableGroup(String groupName) {
        DatapackInfo group = managedPacks.get(groupName);
        if (group == null) return;

        DatapackManager manager = Bukkit.getServer().getDatapackManager();
        for (String packId : group.getPackIds()) {
            Datapack pack = manager.getPack(packId);
            if (pack != null && !pack.isEnabled()) {
                pack.setEnabled(true);
            }
        }
    }

    private static class DatapackInfo {
        private final String name;
        private final List<String> packIds;

        public DatapackInfo(String name, List<String> packIds) {
            this.name = name;
            this.packIds = new ArrayList<>(packIds);
        }

        public List<String> getPackIds() { return packIds; }
    }
}
```

## 🎨 创意应用场景

### 🌍 动态世界主题切换
通过数据包实现季节性内容更新，如圣诞主题、万圣节主题等。

### 🎯 玩家进度解锁
根据玩家成就解锁新的合成配方、结构生成等内容。

### 🏆 竞技模式支持
为不同的游戏模式加载对应的数据包配置。

### 🔄 A/B 测试框架
通过数据包实现服务器功能的 A/B 测试。

## 🛠️ 高级技巧与最佳实践

### 🔍 数据包状态监控

```java
public class DatapackMonitor {
    private final Plugin plugin;
    private final Map<String, Boolean> lastKnownStates = new HashMap<>();

    public void startMonitoring() {
        Bukkit.getScheduler().runTaskTimer(plugin, this::checkDatapackChanges, 0L, 20L);
    }

    private void checkDatapackChanges() {
        DatapackManager manager = Bukkit.getServer().getDatapackManager();

        for (Datapack pack : manager.getPacks()) {
            String name = pack.getName();
            boolean currentState = pack.isEnabled();
            Boolean lastState = lastKnownStates.get(name);

            if (lastState == null || lastState != currentState) {
                onDatapackStateChanged(pack, lastState, currentState);
                lastKnownStates.put(name, currentState);
            }
        }
    }

    private void onDatapackStateChanged(Datapack pack, Boolean oldState, boolean newState) {
        String action = newState ? "启用" : "禁用";
        plugin.getLogger().info(String.format("数据包 %s 已%s", pack.getName(), action));

        // 触发自定义事件
        Bukkit.getPluginManager().callEvent(
            new DatapackStateChangeEvent(pack, oldState, newState)
        );
    }
}
```

### 🎯 条件加载系统

```java
public class ConditionalDatapackLoader {

    public void setupTimeBasedLoading() {
        // 基于时间的数据包加载
        Bukkit.getScheduler().runTaskTimer(plugin, () -> {
            LocalTime now = LocalTime.now();
            DatapackManager manager = Bukkit.getServer().getDatapackManager();

            // 夜间模式数据包
            Datapack nightPack = manager.getPack("night_mode");
            if (nightPack != null) {
                boolean shouldEnable = now.isAfter(LocalTime.of(20, 0)) ||
                                     now.isBefore(LocalTime.of(6, 0));
                if (nightPack.isEnabled() != shouldEnable) {
                    nightPack.setEnabled(shouldEnable);
                }
            }
        }, 0L, 1200L); // 每分钟检查
    }

    public void setupPlayerCountBasedLoading() {
        // 基于玩家数量的数据包加载
        Bukkit.getScheduler().runTaskTimer(plugin, () -> {
            int playerCount = Bukkit.getOnlinePlayers().size();
            DatapackManager manager = Bukkit.getServer().getDatapackManager();

            // 高人数优化包
            Datapack optimizationPack = manager.getPack("high_player_optimization");
            if (optimizationPack != null) {
                boolean shouldEnable = playerCount > 50;
                if (optimizationPack.isEnabled() != shouldEnable) {
                    optimizationPack.setEnabled(shouldEnable);
                }
            }
        }, 0L, 200L); // 每10秒检查
    }
}
```

### 🔧 数据包依赖管理

```java
public class DatapackDependencyManager {
    private final Map<String, Set<String>> dependencies = new HashMap<>();

    public void addDependency(String datapack, String dependency) {
        dependencies.computeIfAbsent(datapack, k -> new HashSet<>()).add(dependency);
    }

    public boolean enableWithDependencies(String datapackName) {
        DatapackManager manager = Bukkit.getServer().getDatapackManager();

        // 检查并启用依赖
        Set<String> deps = dependencies.get(datapackName);
        if (deps != null) {
            for (String dep : deps) {
                Datapack depPack = manager.getPack(dep);
                if (depPack == null) {
                    plugin.getLogger().warning(String.format(
                        "依赖数据包 %s 不存在，无法启用 %s", dep, datapackName));
                    return false;
                }
                if (!depPack.isEnabled()) {
                    depPack.setEnabled(true);
                    plugin.getLogger().info(String.format("已启用依赖数据包: %s", dep));
                }
            }
        }

        // 启用目标数据包
        Datapack targetPack = manager.getPack(datapackName);
        if (targetPack != null && !targetPack.isEnabled()) {
            targetPack.setEnabled(true);
            return true;
        }
        return false;
    }
}
```

## ⚠️ 故障排除指南

### 🚨 常见问题

#### 问题1: 数据包无法发现
**症状**: `discoverPack()` 返回 null
**解决方案**:
- 检查文件路径是否正确
- 验证 pack.mcmeta 文件格式
- 确认符号链接权限设置
- 检查 IOException 异常信息

#### 问题2: 数据包启用失败
**症状**: `setEnabled(true)` 无效果
**解决方案**:
- 检查数据包兼容性状态
- 验证所需功能标志是否可用
- 确认数据包内容格式正确
- 查看服务器日志错误信息

#### 问题3: 资源重载卡顿
**症状**: `setEnabled()` 导致服务器卡顿
**解决方案**:
- 减少数据包大小
- 分批启用多个数据包
- 在低峰时段进行重载
- 使用异步操作包装

### 🔍 调试技巧

```java
public class DatapackDebugger {

    public void debugDatapackInfo(String packName) {
        DatapackManager manager = Bukkit.getServer().getDatapackManager();
        Datapack pack = manager.getPack(packName);

        if (pack == null) {
            plugin.getLogger().info("数据包不存在: " + packName);
            return;
        }

        plugin.getLogger().info("=== 数据包调试信息 ===");
        plugin.getLogger().info("名称: " + pack.getName());
        plugin.getLogger().info("标题: " + PlainTextComponentSerializer.plainText().serialize(pack.getTitle()));
        plugin.getLogger().info("描述: " + PlainTextComponentSerializer.plainText().serialize(pack.getDescription()));
        plugin.getLogger().info("启用状态: " + pack.isEnabled());
        plugin.getLogger().info("必需状态: " + pack.isRequired());
        plugin.getLogger().info("兼容性: " + pack.getCompatibility());
        plugin.getLogger().info("来源: " + pack.getSource());
        plugin.getLogger().info("所需功能: " + pack.getRequiredFeatures());
    }

    public void listAllDatapacks() {
        DatapackManager manager = Bukkit.getServer().getDatapackManager();
        plugin.getLogger().info("=== 所有数据包 ===");

        for (Datapack pack : manager.getPacks()) {
            String status = pack.isEnabled() ? "[启用]" : "[禁用]";
            plugin.getLogger().info(String.format("%s %s - %s",
                status, pack.getName(), pack.getCompatibility()));
        }
    }
}
```

---

*📝 注意：此文档基于 Paper API 1.21.5 版本，某些功能可能在不同版本中有所差异。*
