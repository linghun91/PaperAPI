# 🎮 Paper API Player 完整指南

## 📋 目录
- [🎯 Player 基础接口](#player-基础接口)
- [🎪 Player 事件系统](#player-事件系统)
- [👤 HumanEntity 功能](#humanentity-功能)
- [💪 LivingEntity 功能](#livingentity-功能)
- [🎨 有趣功能实现](#有趣功能实现)

---

## 🎯 Player 基础接口

### 📊 基本信息管理
```java
// 玩家基本信息
String getName()                    // 获取玩家名称
UUID getUniqueId()                 // 获取玩家UUID
Component displayName()            // 获取显示名称
void displayName(Component name)   // 设置显示名称
PlayerProfile getPlayerProfile()   // 获取玩家档案
void setPlayerProfile(PlayerProfile profile) // 设置玩家档案
```

### 🌐 网络与连接
```java
// 网络信息
InetSocketAddress getAddress()     // 获取IP地址
InetSocketAddress getHAProxyAddress() // 获取代理地址
int getPing()                      // 获取延迟
String getClientBrandName()        // 获取客户端品牌
String getLocale()                 // 获取语言设置
boolean isAllowingServerListings() // 是否允许服务器列表
```

### 🎮 游戏模式与状态
```java
// 游戏模式
GameMode getGameMode()             // 获取游戏模式
void setGameMode(GameMode mode)    // 设置游戏模式
GameMode getPreviousGameMode()     // 获取上一个游戏模式

// 飞行相关
boolean getAllowFlight()           // 是否允许飞行
void setAllowFlight(boolean flight) // 设置飞行权限
boolean isFlying()                 // 是否正在飞行
void setFlying(boolean value)      // 设置飞行状态
float getFlySpeed()                // 获取飞行速度
void setFlySpeed(float value)      // 设置飞行速度
```

### 💰 经验与等级
```java
// 经验系统
int getLevel()                     // 获取等级
void setLevel(int level)           // 设置等级
float getExp()                     // 获取经验进度
void setExp(float exp)             // 设置经验进度
int getTotalExperience()           // 获取总经验
void setTotalExperience(int exp)   // 设置总经验
int getExpToLevel()                // 获取升级所需经验
void giveExp(int amount)           // 给予经验
int applyMending(int amount)       // 应用修补附魔
```

### 🎒 物品栏管理
```java
// 物品栏
PlayerInventory getInventory()     // 获取物品栏
Inventory getEnderChest()          // 获取末影箱
ItemStack getItemOnCursor()        // 获取光标物品
void setItemOnCursor(ItemStack item) // 设置光标物品

// 装备
EntityEquipment getEquipment()     // 获取装备
ItemStack getItemInHand()          // 获取手持物品
void setItemInHand(ItemStack item) // 设置手持物品
```

### 🍖 生命与饥饿
```java
// 生命值
double getHealth()                 // 获取生命值
void setHealth(double health)      // 设置生命值
double getMaxHealth()              // 获取最大生命值
void setMaxHealth(double health)   // 设置最大生命值

// 饥饿系统
int getFoodLevel()                 // 获取饥饿值
void setFoodLevel(int value)       // 设置饥饿值
float getSaturation()              // 获取饱和度
void setSaturation(float value)    // 设置饱和度
float getExhaustion()              // 获取疲劳值
void setExhaustion(float value)    // 设置疲劳值
```

---

## 🎪 Player 事件系统

### 🔗 连接事件
```java
// 登录相关
AsyncPlayerPreLoginEvent    // 异步预登录事件
PlayerLoginEvent           // 登录事件
PlayerJoinEvent           // 加入服务器事件
PlayerQuitEvent           // 离开服务器事件
PlayerKickEvent           // 踢出事件
```

### 💬 聊天事件
```java
// 聊天系统
AsyncChatEvent            // 异步聊天事件
AsyncChatDecorateEvent    // 聊天装饰事件
PlayerCommandPreprocessEvent // 命令预处理事件
PlayerChatTabCompleteEvent   // 聊天补全事件
```

### 🎯 交互事件
```java
// 玩家交互
PlayerInteractEvent           // 交互事件
PlayerInteractEntityEvent     // 实体交互事件
PlayerInteractAtEntityEvent   // 精确实体交互事件
PlayerItemHeldEvent          // 物品切换事件
PlayerDropItemEvent          // 丢弃物品事件
PlayerPickupItemEvent        // 拾取物品事件
```

### 🏃 移动事件
```java
// 移动相关
PlayerMoveEvent              // 移动事件
PlayerTeleportEvent          // 传送事件
PlayerChangedWorldEvent      // 世界切换事件
PlayerToggleFlightEvent      // 飞行切换事件
PlayerToggleSneakEvent       // 潜行切换事件
PlayerToggleSprintEvent      // 疾跑切换事件
```

### 🛏️ 生存事件
```java
// 生存相关
PlayerBedEnterEvent          // 进入床铺事件
PlayerBedLeaveEvent          // 离开床铺事件
PlayerDeepSleepEvent         // 深度睡眠事件
PlayerRespawnEvent           // 重生事件
PlayerExpChangeEvent         // 经验变化事件
PlayerLevelChangeEvent       // 等级变化事件
```

---

## 👤 HumanEntity 功能

### 🎒 物品栏操作
```java
// 物品栏管理
InventoryView openInventory(Inventory inventory) // 打开物品栏
void closeInventory()                           // 关闭物品栏
InventoryView getOpenInventory()                // 获取当前打开的物品栏

// 特殊界面
InventoryView openWorkbench(Location location, boolean force) // 打开工作台
InventoryView openEnchanting(Location location, boolean force) // 打开附魔台
InventoryView openAnvil(Location location, boolean force)      // 打开铁砧
```

### 🛏️ 睡眠系统
```java
// 睡眠相关
boolean sleep(Location location, boolean force) // 尝试睡觉
void wakeup(boolean setSpawnLocation)           // 唤醒
Location getBedLocation()                       // 获取床位置
boolean isDeeplySleeping()                      // 是否深度睡眠
int getSleepTicks()                            // 获取睡眠时间
```

### 🎣 钓鱼系统
```java
// 钓鱼相关
FishHook getFishHook()                         // 获取鱼钩
```

### 🔥 烟花推进
```java
// 烟花推进
Firework fireworkBoost(ItemStack fireworkItemStack) // 烟花推进
void startRiptideAttack(int duration, float attackStrength, ItemStack attackItem) // 激流攻击
```

---

## 💪 LivingEntity 功能

### ⚔️ 战斗系统
```java
// 攻击相关
void attack(Entity target)                    // 攻击目标
float getAttackCooldown()                     // 获取攻击冷却
void resetCooldown()                          // 重置冷却
float getCooledAttackStrength(float adjustTicks) // 获取攻击强度

// 伤害相关
void damage(double amount)                    // 造成伤害
void damage(double amount, Entity source)    // 指定来源伤害
double getLastDamage()                        // 获取最后伤害
int getNoDamageTicks()                        // 获取无敌时间
void setNoDamageTicks(int ticks)             // 设置无敌时间
```

### 🧪 药水效果
```java
// 药水效果管理
boolean addPotionEffect(PotionEffect effect)           // 添加药水效果
boolean removePotionEffect(PotionEffectType type)      // 移除药水效果
Collection<PotionEffect> getActivePotionEffects()      // 获取所有药水效果
PotionEffect getPotionEffect(PotionEffectType type)    // 获取指定药水效果
boolean hasPotionEffect(PotionEffectType type)         // 是否有药水效果
```

### 🏹 箭矢系统
```java
// 箭矢相关
int getArrowsInBody()                         // 获取身上箭矢数量
void setArrowsInBody(int count, boolean fireEvent) // 设置箭矢数量
int getArrowCooldown()                        // 获取箭矢冷却
void setArrowCooldown(int ticks)              // 设置箭矢冷却
```

### 👁️ 视线检测
```java
// 视线相关
Location getEyeLocation()                     // 获取眼部位置
double getEyeHeight()                         // 获取眼部高度
Block getTargetBlock(Set<Material> transparent, int maxDistance) // 获取目标方块
Entity getTargetEntity(int maxDistance)      // 获取目标实体
List<Block> getLineOfSight(Set<Material> transparent, int maxDistance) // 获取视线方块
```

---

## 🎨 有趣功能实现

### 🎆 特效系统
```java
// 视觉特效
void showElderGuardian()                      // 显示远古守卫者特效
void showDemoScreen()                         // 显示演示界面
void sendEntityEffect(EntityEffect effect, Entity target) // 发送实体特效
void playPickupItemAnimation(Item item, int quantity)     // 播放拾取动画
```

### 🎵 音效系统
```java
// 音效播放
void playSound(Location location, Sound sound, float volume, float pitch) // 播放音效
void playSound(Location location, String sound, float volume, float pitch) // 自定义音效
void stopSound(Sound sound)                   // 停止音效
void stopAllSounds()                          // 停止所有音效
```

### 📦 资源包管理
```java
// 资源包
void setResourcePack(String url)             // 设置资源包
void setResourcePack(String url, byte[] hash) // 带哈希的资源包
void addResourcePack(UUID id, String url, byte[] hash, String prompt, boolean force) // 添加资源包
void removeResourcePack(UUID id)             // 移除资源包
```

### 🎯 目标追踪
```java
// 实体追踪
void hideEntity(Entity entity)               // 隐藏实体
void showEntity(Entity entity)               // 显示实体
boolean canSee(Entity entity)                // 是否能看见实体
Set<Entity> getTrackedEntities()             // 获取追踪的实体
```

### 🗺️ 地图系统
```java
// 地图相关
void sendMap(MapView map)                     // 发送地图
Location getCompassTarget()                  // 获取指南针目标
void setCompassTarget(Location location)     // 设置指南针目标
```

### 🌍 世界交互
```java
// 世界操作
void sendBlockChange(Location loc, BlockData block) // 发送方块变化
void sendBlockUpdate(Location location, TileState tileState) // 发送方块更新
void sendSignChange(Location loc, String[] lines) // 发送告示牌变化
boolean breakBlock(Block block)               // 破坏方块
```

### 💾 数据持久化
```java
// 数据存储
PersistentDataContainer getPersistentDataContainer() // 获取持久化数据容器
void setMetadata(String metadataKey, MetadataValue newMetadataValue) // 设置元数据
List<MetadataValue> getMetadata(String metadataKey) // 获取元数据
```

### 🎪 高级功能
```java
// 高级操作
void openSign(Sign sign, Side side)          // 打开告示牌编辑
void openVirtualSign(Position block, Side side) // 打开虚拟告示牌
void lookAt(Entity entity, LookAnchor playerAnchor, LookAnchor entityAnchor) // 看向实体
void give(Collection<ItemStack> items, boolean dropIfFull) // 给予物品
PlayerGiveResult give(ItemStack... items)    // 给予物品并返回结果
```

---

## 🚀 实用示例

### 🎮 创建传送门效果
```java
public void createPortalEffect(Player player) {
    player.showElderGuardian(true);
    player.playSound(player.getLocation(), Sound.ENTITY_ENDERMAN_TELEPORT, 1.0f, 1.0f);
    player.addPotionEffect(new PotionEffect(PotionEffectType.BLINDNESS, 20, 1));
}
```

### 🎯 实现瞄准辅助
```java
public Entity getTargetedEntity(Player player, int range) {
    return player.getTargetEntity(range, false);
}
```

### 🎒 智能物品给予
```java
public void giveItemsSafely(Player player, ItemStack... items) {
    PlayerGiveResult result = player.give(items);
    if (!result.getLeftovers().isEmpty()) {
        player.sendMessage("§c背包已满，部分物品已掉落！");
    }
}
```

---

## 🎪 Paper专属Player事件

### 🎨 聊天装饰事件
```java
// Paper专属聊天事件
AsyncChatCommandDecorateEvent    // 命令聊天装饰事件
AsyncChatDecorateEvent          // 聊天装饰事件
PlayerSignCommandPreprocessEvent // 告示牌命令预处理
```

### 🛡️ 战斗相关事件
```java
// 战斗系统
PrePlayerAttackEntityEvent      // 攻击前事件
PlayerShieldDisableEvent        // 盾牌禁用事件
PlayerArmSwingEvent            // 手臂挥舞事件
PlayerStopUsingItemEvent       // 停止使用物品事件
```

### 🎯 交互增强事件
```java
// 高级交互
PlayerPickBlockEvent           // 选择方块事件
PlayerPickEntityEvent          // 选择实体事件
PlayerPickItemEvent           // 选择物品事件
PlayerFlowerPotManipulateEvent // 花盆操作事件
PlayerItemFrameChangeEvent     // 物品展示框变化事件
```

### 🏪 商业交易事件
```java
// 交易系统
PlayerTradeEvent              // 村民交易事件
PlayerPurchaseEvent           // 购买事件
CartographyItemEvent          // 制图台事件
PlayerLoomPatternSelectEvent  // 织布机图案选择事件
PlayerStonecutterRecipeSelectEvent // 切石机配方选择事件
```

### 📚 知识系统事件
```java
// 书籍与学习
PlayerInsertLecternBookEvent  // 讲台插入书籍事件
PlayerTakeLecternBookEvent    // 讲台取出书籍事件
PlayerLecternPageChangeEvent  // 讲台翻页事件
PlayerOpenSignEvent          // 打开告示牌事件
PlayerNameEntityEvent        // 命名实体事件
```

### 🎮 游戏机制事件
```java
// 游戏机制
PlayerInventorySlotChangeEvent    // 物品栏槽位变化事件
PlayerItemCooldownEvent          // 物品冷却事件
PlayerItemGroupCooldownEvent     // 物品组冷却事件
PlayerFailMoveEvent             // 移动失败事件
PlayerBedFailEnterEvent         // 进入床铺失败事件
PlayerDeepSleepEvent           // 深度睡眠事件
PlayerClientLoadedWorldEvent    // 客户端加载世界事件
```

### 🌟 特殊能力事件
```java
// 特殊能力
PlayerChangeBeaconEffectEvent   // 信标效果变化事件
PlayerMapFilledEvent           // 地图填充事件
PlayerTrackEntityEvent         // 实体追踪事件
PlayerUntrackEntityEvent       // 取消实体追踪事件
```

---

## 🔧 实用工具方法

### 📊 数据获取工具
```java
// 客户端信息
<T> T getClientOption(ClientOption<T> option)  // 获取客户端选项
int getClientViewDistance()                    // 获取客户端视距
Duration getIdleDuration()                     // 获取空闲时间
void resetIdleDuration()                       // 重置空闲时间

// 服务器状态
boolean isOnline()                             // 是否在线
long getFirstPlayed()                          // 首次游戏时间
long getLastPlayed()                           // 最后游戏时间
boolean hasPlayedBefore()                      // 是否之前游戏过
```

### 🎯 位置与移动
```java
// 位置管理
Location getLocation()                         // 获取位置
Location getBedSpawnLocation()                 // 获取床重生点
void setBedSpawnLocation(Location location)    // 设置床重生点
Location getLastDeathLocation()                // 获取最后死亡位置
void setLastDeathLocation(Location location)   // 设置最后死亡位置

// 移动控制
void teleport(Location location)              // 传送
void teleport(Entity destination)             // 传送到实体
boolean teleport(Location location, TeleportCause cause) // 带原因传送
Vector getVelocity()                          // 获取速度
void setVelocity(Vector velocity)             // 设置速度
```

### 🎨 视觉效果
```java
// 粒子效果
void spawnParticle(Particle particle, Location location, int count) // 生成粒子
void spawnParticle(Particle particle, double x, double y, double z, int count) // 指定坐标粒子

// 标题显示
void sendTitle(String title, String subtitle)  // 发送标题
void sendTitle(String title, String subtitle, int fadeIn, int stay, int fadeOut) // 带时间标题
void resetTitle()                              // 重置标题
void clearTitle()                              // 清除标题

// ActionBar
void sendActionBar(String message)            // 发送动作栏消息
void sendActionBar(Component message)         // 发送组件动作栏消息
```

### 🎵 音效与音乐
```java
// 高级音效
void playSound(Location location, Sound sound, SoundCategory category, float volume, float pitch) // 分类音效
void playSound(Entity entity, Sound sound, float volume, float pitch) // 实体音效
void stopSound(Sound sound, SoundCategory category) // 停止分类音效

// 音乐控制
void playNote(Location loc, Instrument instrument, Note note) // 播放音符
```

### 🌍 世界交互增强
```java
// 方块操作
void sendBlockChanges(Collection<BlockState> blocks) // 批量方块变化
void sendMultiBlockChange(Map<Location, BlockData> blockChanges) // 多方块变化
void sendChunkChange(Chunk chunk)                    // 发送区块变化

// 实体操作
void hideEntity(Plugin plugin, Entity entity)       // 插件隐藏实体
void showEntity(Plugin plugin, Entity entity)       // 插件显示实体
Set<Entity> getHiddenEntities()                     // 获取隐藏实体列表
```

### 💾 高级数据管理
```java
// 统计数据
int getStatistic(Statistic statistic)              // 获取统计
void setStatistic(Statistic statistic, int newValue) // 设置统计
void incrementStatistic(Statistic statistic)        // 增加统计
void decrementStatistic(Statistic statistic)        // 减少统计

// 成就系统
AdvancementProgress getAdvancementProgress(Advancement advancement) // 获取成就进度
Collection<Advancement> getCompletedAdvancements()  // 获取已完成成就
```

### 🎮 游戏机制控制
```java
// 时间控制
void setPlayerTime(long time, boolean relative)     // 设置玩家时间
long getPlayerTime()                                // 获取玩家时间
long getPlayerTimeOffset()                          // 获取时间偏移
boolean isPlayerTimeRelative()                      // 是否相对时间
void resetPlayerTime()                              // 重置玩家时间

// 天气控制
void setPlayerWeather(WeatherType type)             // 设置玩家天气
WeatherType getPlayerWeather()                      // 获取玩家天气
void resetPlayerWeather()                           // 重置玩家天气
```

---

## 🎯 高级应用示例

### 🎪 创建自定义GUI系统
```java
public class CustomGUI {
    public void openCustomInventory(Player player) {
        Inventory inv = Bukkit.createInventory(null, 27, "§6自定义界面");

        // 添加物品
        ItemStack item = new ItemStack(Material.DIAMOND);
        ItemMeta meta = item.getItemMeta();
        meta.setDisplayName("§b特殊物品");
        meta.setLore(Arrays.asList("§7这是一个特殊物品", "§7点击获得奖励"));
        item.setItemMeta(meta);

        inv.setItem(13, item);
        player.openInventory(inv);

        // 播放打开音效
        player.playSound(player.getLocation(), Sound.BLOCK_CHEST_OPEN, 1.0f, 1.0f);
    }
}
```

### 🎯 实现智能传送系统
```java
public class SmartTeleport {
    public void safeTeleport(Player player, Location target) {
        // 检查目标位置安全性
        if (!isSafeLocation(target)) {
            target = findSafeLocation(target);
        }

        // 传送前效果
        player.addPotionEffect(new PotionEffect(PotionEffectType.BLINDNESS, 20, 1));
        player.playSound(player.getLocation(), Sound.ENTITY_ENDERMAN_TELEPORT, 1.0f, 1.0f);

        // 延迟传送
        Bukkit.getScheduler().runTaskLater(plugin, () -> {
            player.teleport(target);
            player.sendTitle("§a传送成功", "§7欢迎来到新位置", 10, 40, 10);

            // 传送后效果
            player.spawnParticle(Particle.PORTAL, player.getLocation(), 50);
            player.playSound(player.getLocation(), Sound.ENTITY_PLAYER_LEVELUP, 1.0f, 1.0f);
        }, 20L);
    }

    private boolean isSafeLocation(Location loc) {
        Block block = loc.getBlock();
        Block above = loc.clone().add(0, 1, 0).getBlock();
        Block below = loc.clone().add(0, -1, 0).getBlock();

        return !block.getType().isSolid() &&
               !above.getType().isSolid() &&
               below.getType().isSolid();
    }
}
```

### 🎨 创建动态标题系统
```java
public class DynamicTitle {
    public void showProgressTitle(Player player, String task, int current, int max) {
        String title = "§6" + task;
        String subtitle = createProgressBar(current, max);

        player.sendTitle(title, subtitle, 0, 20, 0);

        // 进度音效
        float pitch = 0.5f + ((float) current / max) * 1.5f;
        player.playSound(player.getLocation(), Sound.BLOCK_NOTE_BLOCK_PLING, 0.5f, pitch);
    }

    private String createProgressBar(int current, int max) {
        int barLength = 20;
        int filled = (int) ((double) current / max * barLength);

        StringBuilder bar = new StringBuilder("§a");
        for (int i = 0; i < filled; i++) {
            bar.append("█");
        }
        bar.append("§7");
        for (int i = filled; i < barLength; i++) {
            bar.append("█");
        }

        return bar.toString() + " §f" + current + "/" + max;
    }
}
```

### 🎮 实现技能冷却系统
```java
public class SkillCooldown {
    private Map<UUID, Map<String, Long>> cooldowns = new HashMap<>();

    public boolean useSkill(Player player, String skillName, long cooldownMs) {
        UUID playerId = player.getUniqueId();
        Map<String, Long> playerCooldowns = cooldowns.computeIfAbsent(playerId, k -> new HashMap<>());

        long currentTime = System.currentTimeMillis();
        Long lastUsed = playerCooldowns.get(skillName);

        if (lastUsed != null && currentTime - lastUsed < cooldownMs) {
            long remaining = cooldownMs - (currentTime - lastUsed);
            player.sendActionBar("§c技能冷却中: " + (remaining / 1000) + "秒");
            return false;
        }

        playerCooldowns.put(skillName, currentTime);
        player.sendActionBar("§a技能 " + skillName + " 已使用！");
        return true;
    }
}
```

---

## 🎪 更多高级功能

### 🔒 权限与封禁系统
```java
// 权限管理
boolean hasPermission(String permission)           // 检查权限
boolean hasPermission(Permission perm)             // 检查权限对象
void addAttachment(Plugin plugin, String permission, boolean value) // 添加权限附件
boolean isOp()                                     // 是否为管理员
void setOp(boolean value)                          // 设置管理员状态

// 封禁系统
BanEntry ban(String reason, Duration duration, String source, boolean kickPlayer) // 封禁玩家
BanEntry banIp(String reason, Duration duration, String source, boolean kickPlayer) // 封禁IP
void kick(Component message)                       // 踢出玩家
void kick(Component message, PlayerKickEvent.Cause cause) // 带原因踢出
```

### 🎯 高级战斗系统
```java
// 战斗追踪
CombatTracker getCombatTracker()                   // 获取战斗追踪器
int getWardenWarningLevel()                        // 获取监守者警告等级
void setWardenWarningLevel(int level)              // 设置监守者警告等级
void increaseWardenWarningLevel()                  // 增加监守者警告等级
int getWardenWarningCooldown()                     // 获取监守者警告冷却
void setWardenWarningCooldown(int cooldown)        // 设置监守者警告冷却

// 攻击冷却
float getCooldownPeriod()                          // 获取冷却周期
float getCooledAttackStrength(float adjustTicks)   // 获取冷却攻击强度
void resetCooldown()                               // 重置攻击冷却
```

### 🎒 高级物品栏操作
```java
// 物品冷却
int getCooldown(Material material)                 // 获取材料冷却
int getCooldown(ItemStack item)                    // 获取物品冷却
void setCooldown(Material material, int ticks)     // 设置材料冷却
void setCooldown(ItemStack item, int ticks)        // 设置物品冷却
boolean hasCooldown(Material material)             // 是否有冷却

// 物品操作
Item dropItem(ItemStack itemStack)                 // 丢弃物品
Item dropItem(EquipmentSlot slot, int amount)      // 丢弃装备槽物品
PlayerGiveResult give(ItemStack... items)          // 给予物品
PlayerGiveResult give(Collection<ItemStack> items, boolean dropIfFull) // 给予物品集合
```

### 🌟 特殊能力与状态
```java
// 特殊状态
boolean isGliding()                                // 是否滑翔
void setGliding(boolean gliding)                   // 设置滑翔状态
boolean isSwimming()                               // 是否游泳
void setSwimming(boolean swimming)                 // 设置游泳状态
boolean isRiptiding()                              // 是否激流
boolean isClimbing()                               // 是否攀爬

// 移动能力
Input getCurrentInput()                            // 获取当前输入
float getWalkSpeed()                               // 获取行走速度
void setWalkSpeed(float value)                     // 设置行走速度
float getFlySpeed()                                // 获取飞行速度
void setFlySpeed(float value)                      // 设置飞行速度
```

### 📊 数据组件系统
```java
// 数据组件 (Paper 1.21+)
<T> T getDataComponent(DataComponentType<T> type)  // 获取数据组件
<T> void setDataComponent(DataComponentType<T> type, T value) // 设置数据组件
boolean hasDataComponent(DataComponentType<?> type) // 是否有数据组件
void removeDataComponent(DataComponentType<?> type) // 移除数据组件
```

---

## 🎨 创意功能实现

### 🎪 全息文字系统
```java
public class HologramSystem {
    private Map<UUID, List<ArmorStand>> holograms = new HashMap<>();

    public void createHologram(Player player, Location location, List<String> lines) {
        List<ArmorStand> stands = new ArrayList<>();

        for (int i = 0; i < lines.size(); i++) {
            Location standLoc = location.clone().add(0, -i * 0.25, 0);
            ArmorStand stand = location.getWorld().spawn(standLoc, ArmorStand.class);

            stand.setVisible(false);
            stand.setGravity(false);
            stand.setCustomName(lines.get(i));
            stand.setCustomNameVisible(true);
            stand.setMarker(true);

            stands.add(stand);
        }

        holograms.put(player.getUniqueId(), stands);

        // 只对特定玩家显示
        for (Player other : Bukkit.getOnlinePlayers()) {
            if (!other.equals(player)) {
                stands.forEach(stand -> other.hideEntity(plugin, stand));
            }
        }
    }

    public void removeHologram(Player player) {
        List<ArmorStand> stands = holograms.remove(player.getUniqueId());
        if (stands != null) {
            stands.forEach(ArmorStand::remove);
        }
    }
}
```

### 🎯 智能NPC对话系统
```java
public class NPCDialogSystem {
    private Map<UUID, DialogSession> sessions = new HashMap<>();

    public void startDialog(Player player, String npcName, List<String> messages) {
        DialogSession session = new DialogSession(npcName, messages);
        sessions.put(player.getUniqueId(), session);

        showNextMessage(player);
    }

    private void showNextMessage(Player player) {
        DialogSession session = sessions.get(player.getUniqueId());
        if (session == null || session.isFinished()) {
            sessions.remove(player.getUniqueId());
            return;
        }

        String message = session.getNextMessage();
        player.sendTitle("§6" + session.getNpcName(), "§f" + message, 10, 60, 10);

        // 打字机效果音效
        player.playSound(player.getLocation(), Sound.BLOCK_NOTE_BLOCK_PLING, 0.5f, 1.5f);

        // 自动显示下一条消息
        Bukkit.getScheduler().runTaskLater(plugin, () -> showNextMessage(player), 80L);
    }

    private static class DialogSession {
        private final String npcName;
        private final List<String> messages;
        private int currentIndex = 0;

        public DialogSession(String npcName, List<String> messages) {
            this.npcName = npcName;
            this.messages = messages;
        }

        public String getNpcName() { return npcName; }
        public String getNextMessage() { return messages.get(currentIndex++); }
        public boolean isFinished() { return currentIndex >= messages.size(); }
    }
}
```

### 🎮 粒子轨迹系统
```java
public class ParticleTrail {
    private Map<UUID, BukkitTask> trails = new HashMap<>();

    public void startTrail(Player player, Particle particle, int density) {
        stopTrail(player); // 停止现有轨迹

        BukkitTask task = Bukkit.getScheduler().runTaskTimer(plugin, () -> {
            if (!player.isOnline()) {
                stopTrail(player);
                return;
            }

            Location loc = player.getLocation().add(0, 0.1, 0);
            player.getWorld().spawnParticle(particle, loc, density, 0.2, 0.2, 0.2, 0);

        }, 0L, 2L);

        trails.put(player.getUniqueId(), task);
    }

    public void stopTrail(Player player) {
        BukkitTask task = trails.remove(player.getUniqueId());
        if (task != null) {
            task.cancel();
        }
    }

    public void createHeartTrail(Player player) {
        // 爱心粒子轨迹
        startTrail(player, Particle.HEART, 3);
        player.sendActionBar("§c❤ 爱心轨迹已激活 ❤");
    }

    public void createMagicTrail(Player player) {
        // 魔法粒子轨迹
        startTrail(player, Particle.ENCHANT, 5);
        player.sendActionBar("§5✨ 魔法轨迹已激活 ✨");
    }
}
```

### 🎪 动态记分板系统
```java
public class DynamicScoreboard {
    private Map<UUID, Scoreboard> scoreboards = new HashMap<>();

    public void createScoreboard(Player player) {
        ScoreboardManager manager = Bukkit.getScoreboardManager();
        Scoreboard board = manager.getNewScoreboard();

        Objective objective = board.registerNewObjective("stats", "dummy", "§6§l服务器信息");
        objective.setDisplaySlot(DisplaySlot.SIDEBAR);

        updateScoreboard(player, board, objective);
        player.setScoreboard(board);
        scoreboards.put(player.getUniqueId(), board);

        // 定期更新
        Bukkit.getScheduler().runTaskTimer(plugin, () -> {
            if (player.isOnline()) {
                updateScoreboard(player, board, objective);
            }
        }, 0L, 20L);
    }

    private void updateScoreboard(Player player, Scoreboard board, Objective objective) {
        // 清除旧分数
        board.getEntries().forEach(board::resetScores);

        // 添加新信息
        objective.getScore("§7§m                    ").setScore(15);
        objective.getScore("§e玩家: §f" + player.getName()).setScore(14);
        objective.getScore("§e等级: §f" + player.getLevel()).setScore(13);
        objective.getScore("§e生命: §c" + (int)player.getHealth() + "❤").setScore(12);
        objective.getScore("§e饥饿: §6" + player.getFoodLevel() + "🍖").setScore(11);
        objective.getScore("§e坐标: §f" +
            (int)player.getLocation().getX() + ", " +
            (int)player.getLocation().getY() + ", " +
            (int)player.getLocation().getZ()).setScore(10);
        objective.getScore("§e在线: §a" + Bukkit.getOnlinePlayers().size()).setScore(9);
        objective.getScore("§7§m                    ").setScore(8);
    }

    public void removeScoreboard(Player player) {
        scoreboards.remove(player.getUniqueId());
        player.setScoreboard(Bukkit.getScoreboardManager().getMainScoreboard());
    }
}
```

### 🎯 区域保护系统
```java
public class RegionProtection {
    private Map<String, Region> regions = new HashMap<>();

    public void createRegion(String name, Location pos1, Location pos2, UUID owner) {
        Region region = new Region(name, pos1, pos2, owner);
        regions.put(name, region);
    }

    public boolean canInteract(Player player, Location location) {
        for (Region region : regions.values()) {
            if (region.contains(location)) {
                if (!region.canInteract(player)) {
                    player.sendActionBar("§c你没有权限在此区域操作！");
                    player.playSound(player.getLocation(), Sound.BLOCK_NOTE_BLOCK_BASS, 1.0f, 0.5f);
                    return false;
                }
            }
        }
        return true;
    }

    private static class Region {
        private final String name;
        private final Location min, max;
        private final UUID owner;
        private final Set<UUID> members = new HashSet<>();

        public Region(String name, Location pos1, Location pos2, UUID owner) {
            this.name = name;
            this.owner = owner;

            double minX = Math.min(pos1.getX(), pos2.getX());
            double minY = Math.min(pos1.getY(), pos2.getY());
            double minZ = Math.min(pos1.getZ(), pos2.getZ());
            double maxX = Math.max(pos1.getX(), pos2.getX());
            double maxY = Math.max(pos1.getY(), pos2.getY());
            double maxZ = Math.max(pos1.getZ(), pos2.getZ());

            this.min = new Location(pos1.getWorld(), minX, minY, minZ);
            this.max = new Location(pos1.getWorld(), maxX, maxY, maxZ);
        }

        public boolean contains(Location loc) {
            return loc.getWorld().equals(min.getWorld()) &&
                   loc.getX() >= min.getX() && loc.getX() <= max.getX() &&
                   loc.getY() >= min.getY() && loc.getY() <= max.getY() &&
                   loc.getZ() >= min.getZ() && loc.getZ() <= max.getZ();
        }

        public boolean canInteract(Player player) {
            return player.getUniqueId().equals(owner) ||
                   members.contains(player.getUniqueId()) ||
                   player.hasPermission("region.admin");
        }

        public void addMember(UUID playerId) { members.add(playerId); }
        public void removeMember(UUID playerId) { members.remove(playerId); }
    }
}
```

---

## 🎯 性能优化建议

### ⚡ 事件处理优化
```java
// 使用异步事件处理
@EventHandler(priority = EventPriority.MONITOR, ignoreCancelled = true)
public void onAsyncChat(AsyncChatEvent event) {
    // 异步处理聊天事件，不阻塞主线程
    Player player = event.getPlayer();
    // 处理逻辑...
}

// 批量操作优化
public void updateMultiplePlayers(Collection<Player> players) {
    // 批量更新而不是逐个更新
    Bukkit.getScheduler().runTask(plugin, () -> {
        for (Player player : players) {
            // 批量处理
        }
    });
}
```

### 🔄 缓存策略
```java
public class PlayerDataCache {
    private final Map<UUID, PlayerData> cache = new ConcurrentHashMap<>();
    private final long CACHE_EXPIRE_TIME = 300000; // 5分钟

    public PlayerData getPlayerData(Player player) {
        UUID uuid = player.getUniqueId();
        PlayerData data = cache.get(uuid);

        if (data == null || data.isExpired()) {
            data = loadPlayerData(player);
            cache.put(uuid, data);
        }

        return data;
    }

    private PlayerData loadPlayerData(Player player) {
        // 从数据库或文件加载数据
        return new PlayerData(player, System.currentTimeMillis() + CACHE_EXPIRE_TIME);
    }
}
```

---

*📚 本文档基于 Paper API 1.21.5 版本编写，涵盖了Player接口的所有主要功能和用法。包含了基础API、Paper专属功能、实用工具方法、高级应用示例以及性能优化建议。这是一个完整的Player API参考指南，适合各个水平的开发者使用。*
