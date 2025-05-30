# 🪂 GLIDER 数据组件 API 文档

## 📋 概述

GLIDER 是 Paper API 1.21.5 中引入的一个数据组件类型，允许任何物品在装备时具有类似鞘翅的滑翔功能。这是一个非值组件（NonValued），只需要设置即可生效。

## 🔧 基本信息

- **组件类型**: `DataComponentType.NonValued`
- **API 路径**: `io.papermc.paper.datacomponent.DataComponentTypes.GLIDER`
- **引入版本**: Minecraft 1.21.2 (24w36a)
- **Paper API**: 1.21.5+

## 📚 API 方法详解

### 🎯 核心方法

#### 设置 GLIDER 组件
```java
// 为物品添加滑翔功能
itemStack.setData(DataComponentTypes.GLIDER);
```

#### 检查是否有 GLIDER 组件
```java
// 检查物品是否具有滑翔功能
boolean hasGlider = itemStack.hasData(DataComponentTypes.GLIDER);
```

#### 移除 GLIDER 组件
```java
// 移除物品的滑翔功能
itemStack.unsetData(DataComponentTypes.GLIDER);
```

### 🛠️ 配合 Equippable 组件使用

GLIDER 组件通常需要与 Equippable 组件配合使用，以指定装备槽位：

```java
import io.papermc.paper.datacomponent.DataComponentTypes;
import io.papermc.paper.datacomponent.item.Equippable;
import org.bukkit.inventory.EquipmentSlot;
import net.kyori.adventure.key.Key;

// 创建可装备的滑翔物品
ItemStack gliderItem = new ItemStack(Material.NETHER_STAR);

// 设置为可装备在胸甲槽位
Equippable equippable = Equippable.equippable(EquipmentSlot.CHEST)
    .equipSound(Key.key("item.armor.equip_elytra"))
    .build();

gliderItem.setData(DataComponentTypes.EQUIPPABLE, equippable);
gliderItem.setData(DataComponentTypes.GLIDER);
```

## 🎮 实用功能示例

### ⭐ 创建自定义滑翔翅膀

```java
public ItemStack createCustomGlider(Material material, String name) {
    ItemStack glider = new ItemStack(material);
    
    // 设置自定义名称
    glider.setData(DataComponentTypes.CUSTOM_NAME, 
        Component.text(name).color(NamedTextColor.AQUA));
    
    // 设置为胸甲装备
    Equippable equippable = Equippable.equippable(EquipmentSlot.CHEST)
        .equipSound(Key.key("item.armor.equip_elytra"))
        .dispensable(true)
        .swappable(true)
        .damageOnHurt(false)
        .build();
    
    glider.setData(DataComponentTypes.EQUIPPABLE, equippable);
    glider.setData(DataComponentTypes.GLIDER);
    
    return glider;
}
```

### 🎨 创建头盔滑翔器

```java
public ItemStack createGliderHelmet() {
    ItemStack helmet = new ItemStack(Material.DIAMOND_HELMET);
    
    // 设置为头盔装备但具有滑翔功能
    Equippable equippable = Equippable.equippable(EquipmentSlot.HEAD)
        .equipSound(Key.key("item.armor.equip_diamond"))
        .build();
    
    helmet.setData(DataComponentTypes.EQUIPPABLE, equippable);
    helmet.setData(DataComponentTypes.GLIDER);
    
    return helmet;
}
```

### 🛡️ 创建滑翔盾牌

```java
public ItemStack createGliderShield() {
    ItemStack shield = new ItemStack(Material.SHIELD);
    
    // 盾牌默认在副手，但我们可以让它具有滑翔功能
    Equippable equippable = Equippable.equippable(EquipmentSlot.OFFHAND)
        .equipSound(Key.key("item.armor.equip_generic"))
        .build();
    
    shield.setData(DataComponentTypes.EQUIPPABLE, equippable);
    shield.setData(DataComponentTypes.GLIDER);
    
    return shield;
}
```

### 🎪 创建多功能滑翔装备

```java
public ItemStack createAdvancedGlider() {
    ItemStack glider = new ItemStack(Material.ELYTRA);
    
    // 设置高级属性
    glider.setData(DataComponentTypes.CUSTOM_NAME, 
        Component.text("高级滑翔翼").color(NamedTextColor.GOLD));
    
    // 设置说明
    List<Component> lore = Arrays.asList(
        Component.text("✈ 无限滑翔").color(NamedTextColor.GREEN),
        Component.text("⚡ 超音速飞行").color(NamedTextColor.YELLOW),
        Component.text("🛡 防摔伤保护").color(NamedTextColor.BLUE)
    );
    glider.setData(DataComponentTypes.LORE, ItemLore.lore(lore));
    
    // 设置为不可破坏
    glider.setData(DataComponentTypes.UNBREAKABLE);
    
    // 设置装备属性
    Equippable equippable = Equippable.equippable(EquipmentSlot.CHEST)
        .equipSound(Key.key("item.armor.equip_elytra"))
        .dispensable(true)
        .swappable(true)
        .damageOnHurt(false)
        .build();
    
    glider.setData(DataComponentTypes.EQUIPPABLE, equippable);
    glider.setData(DataComponentTypes.GLIDER);
    
    return glider;
}
```

## 🎯 实际应用场景

### 🏰 RPG 游戏装备系统
- 创建不同等级的滑翔装备
- 职业专属滑翔道具
- 临时滑翔药水效果物品

### 🎮 小游戏模式
- 空岛生存滑翔翼
- 竞速比赛专用装备
- 探险模式飞行道具

### 🎨 装饰性物品
- 时装滑翔翼
- 节日主题飞行装备
- VIP 专属滑翔道具

## ⚠️ 注意事项

1. **装备槽位要求**: GLIDER 组件需要物品被装备才能生效
2. **Equippable 依赖**: 通常需要配合 Equippable 组件指定装备槽位
3. **兼容性**: 仅在 Paper 1.21.5+ 版本可用
4. **实验性功能**: 标记为 @Experimental，未来版本可能有变化

## 🔗 相关组件

- **EQUIPPABLE**: 定义装备槽位和装备属性
- **UNBREAKABLE**: 设置物品不可破坏
- **CUSTOM_NAME**: 自定义物品名称
- **LORE**: 物品说明文本
- **ENCHANTMENTS**: 附魔效果

## 📖 命令示例

```bash
# 给予玩家一个可滑翔的下界之星（装备在头部）
/give @s nether_star[equippable={slot:"head"},glider={}]

# 给予玩家一个可滑翔的钻石胸甲
/give @s diamond_chestplate[glider={}]

# 给予玩家一个自定义名称的滑翔翼
/give @s elytra[custom_name='{"text":"超级滑翔翼","color":"gold"}',glider={}]
```

## 🔍 高级用法

### 🎛️ 动态滑翔器管理

```java
public class GliderManager {

    /**
     * 检查玩家是否装备了滑翔器
     */
    public boolean hasGliderEquipped(Player player) {
        ItemStack chestplate = player.getInventory().getChestplate();
        if (chestplate != null) {
            return chestplate.hasData(DataComponentTypes.GLIDER);
        }
        return false;
    }

    /**
     * 为现有装备添加滑翔功能
     */
    public void addGliderToItem(ItemStack item, EquipmentSlot slot) {
        if (item == null || item.getType() == Material.AIR) return;

        // 设置装备属性
        Equippable equippable = Equippable.equippable(slot)
            .equipSound(Key.key("item.armor.equip_elytra"))
            .dispensable(true)
            .swappable(true)
            .build();

        item.setData(DataComponentTypes.EQUIPPABLE, equippable);
        item.setData(DataComponentTypes.GLIDER);
    }

    /**
     * 移除物品的滑翔功能
     */
    public void removeGliderFromItem(ItemStack item) {
        if (item != null) {
            item.unsetData(DataComponentTypes.GLIDER);
        }
    }
}
```

### 🎨 滑翔器工厂类

```java
public class GliderFactory {

    public enum GliderType {
        BASIC("基础滑翔翼", Material.LEATHER_CHESTPLATE, NamedTextColor.GRAY),
        ADVANCED("高级滑翔翼", Material.IRON_CHESTPLATE, NamedTextColor.WHITE),
        EPIC("史诗滑翔翼", Material.DIAMOND_CHESTPLATE, NamedTextColor.BLUE),
        LEGENDARY("传说滑翔翼", Material.NETHERITE_CHESTPLATE, NamedTextColor.GOLD);

        private final String name;
        private final Material material;
        private final TextColor color;

        GliderType(String name, Material material, TextColor color) {
            this.name = name;
            this.material = material;
            this.color = color;
        }
    }

    public ItemStack createGlider(GliderType type) {
        ItemStack glider = new ItemStack(type.material);

        // 设置名称
        glider.setData(DataComponentTypes.CUSTOM_NAME,
            Component.text(type.name).color(type.color));

        // 设置说明
        List<Component> lore = new ArrayList<>();
        lore.add(Component.text("🪂 滑翔功能").color(NamedTextColor.GREEN));
        lore.add(Component.text("装备后可以滑翔").color(NamedTextColor.GRAY));

        switch (type) {
            case ADVANCED:
                lore.add(Component.text("⚡ 提升滑翔速度").color(NamedTextColor.YELLOW));
                break;
            case EPIC:
                lore.add(Component.text("💎 钻石级防护").color(NamedTextColor.AQUA));
                lore.add(Component.text("🛡 减少摔落伤害").color(NamedTextColor.BLUE));
                break;
            case LEGENDARY:
                lore.add(Component.text("🔥 下界合金强化").color(NamedTextColor.DARK_RED));
                lore.add(Component.text("⚡ 超音速滑翔").color(NamedTextColor.GOLD));
                lore.add(Component.text("🛡 完全免疫摔伤").color(NamedTextColor.GREEN));
                glider.setData(DataComponentTypes.UNBREAKABLE);
                break;
        }

        glider.setData(DataComponentTypes.LORE, ItemLore.lore(lore));

        // 设置装备属性
        Equippable equippable = Equippable.equippable(EquipmentSlot.CHEST)
            .equipSound(Key.key("item.armor.equip_elytra"))
            .dispensable(true)
            .swappable(true)
            .damageOnHurt(type != GliderType.LEGENDARY)
            .build();

        glider.setData(DataComponentTypes.EQUIPPABLE, equippable);
        glider.setData(DataComponentTypes.GLIDER);

        return glider;
    }
}
```

### 🎮 事件监听器示例

```java
public class GliderEventListener implements Listener {

    @EventHandler
    public void onPlayerToggleFlight(PlayerToggleFlightEvent event) {
        Player player = event.getPlayer();

        // 检查玩家是否装备了滑翔器
        ItemStack chestplate = player.getInventory().getChestplate();
        if (chestplate != null && chestplate.hasData(DataComponentTypes.GLIDER)) {
            // 允许滑翔
            event.setCancelled(false);
            player.sendMessage(Component.text("🪂 滑翔模式激活！").color(NamedTextColor.GREEN));
        }
    }

    @EventHandler
    public void onPlayerMove(PlayerMoveEvent event) {
        Player player = event.getPlayer();

        // 检查玩家是否在滑翔状态
        if (player.isGliding()) {
            ItemStack chestplate = player.getInventory().getChestplate();
            if (chestplate != null && chestplate.hasData(DataComponentTypes.GLIDER)) {
                // 可以在这里添加特殊效果
                spawnGliderParticles(player);
            }
        }
    }

    private void spawnGliderParticles(Player player) {
        Location loc = player.getLocation();
        player.getWorld().spawnParticle(
            Particle.CLOUD,
            loc.add(0, -0.5, 0),
            5,
            0.5, 0.1, 0.5,
            0.02
        );
    }
}
```

## 🧪 测试和调试

### 🔧 调试工具类

```java
public class GliderDebugger {

    public void debugItemComponents(ItemStack item) {
        if (item == null) {
            System.out.println("物品为空");
            return;
        }

        System.out.println("=== 物品组件调试信息 ===");
        System.out.println("物品类型: " + item.getType());
        System.out.println("是否有GLIDER组件: " + item.hasData(DataComponentTypes.GLIDER));
        System.out.println("是否有EQUIPPABLE组件: " + item.hasData(DataComponentTypes.EQUIPPABLE));

        if (item.hasData(DataComponentTypes.EQUIPPABLE)) {
            Equippable equippable = item.getData(DataComponentTypes.EQUIPPABLE);
            if (equippable != null) {
                System.out.println("装备槽位: " + equippable.slot());
                System.out.println("是否可分发: " + equippable.dispensable());
                System.out.println("是否可交换: " + equippable.swappable());
                System.out.println("受伤时损坏: " + equippable.damageOnHurt());
            }
        }

        System.out.println("所有数据组件: " + item.getDataTypes());
    }

    public void testGliderFunctionality(Player player) {
        ItemStack testGlider = new ItemStack(Material.ELYTRA);
        testGlider.setData(DataComponentTypes.GLIDER);

        Equippable equippable = Equippable.equippable(EquipmentSlot.CHEST)
            .equipSound(Key.key("item.armor.equip_elytra"))
            .build();
        testGlider.setData(DataComponentTypes.EQUIPPABLE, equippable);

        player.getInventory().setChestplate(testGlider);
        player.sendMessage(Component.text("测试滑翔器已装备！").color(NamedTextColor.GREEN));

        debugItemComponents(testGlider);
    }
}
```

## 📊 性能优化建议

### ⚡ 最佳实践

1. **缓存组件检查结果**
```java
// 避免频繁检查组件
private final Map<UUID, Boolean> gliderCache = new HashMap<>();

public boolean hasGliderCached(Player player) {
    return gliderCache.computeIfAbsent(player.getUniqueId(), uuid -> {
        ItemStack chest = player.getInventory().getChestplate();
        return chest != null && chest.hasData(DataComponentTypes.GLIDER);
    });
}
```

2. **批量处理组件设置**
```java
public void setupGliderBatch(List<ItemStack> items, EquipmentSlot slot) {
    Equippable equippable = Equippable.equippable(slot)
        .equipSound(Key.key("item.armor.equip_elytra"))
        .build();

    for (ItemStack item : items) {
        if (item != null && item.getType() != Material.AIR) {
            item.setData(DataComponentTypes.EQUIPPABLE, equippable);
            item.setData(DataComponentTypes.GLIDER);
        }
    }
}
```

## 🔮 未来发展方向

### 🚀 可能的扩展功能

1. **自定义滑翔参数**: 未来可能支持滑翔速度、持续时间等参数
2. **条件滑翔**: 基于环境或玩家状态的滑翔限制
3. **滑翔特效**: 更丰富的视觉和音效支持
4. **多槽位支持**: 支持在不同装备槽位使用滑翔功能

---

*📝 本文档基于 Paper API 1.21.5 版本编写，涵盖了 GLIDER 数据组件的完整使用方法和实践案例。*
