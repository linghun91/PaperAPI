# Paper Component API 客户端自动翻译实现指南

## 概述

本文档总结了如何使用Paper官方API实现真正的客户端自动翻译，完全移除硬编码映射方法，让Minecraft客户端根据自身语言设置自动显示物品名称。

## 核心理念

### ❌ 错误方法（硬编码映射）
```java
// 不要这样做
Map<String, String> translations = new HashMap<>();
translations.put("diamond_sword", "钻石剑");
translations.put("iron_sword", "铁剑");
// 需要维护大量映射，无法支持所有语言
```

### ✅ 正确方法（客户端自动翻译）
```java
// 推荐做法：一行代码实现多语言支持
Component.translatable(material.translationKey())
```

## Gradle依赖配置

在`build.gradle`中添加以下依赖：

```gradle
dependencies {
    compileOnly 'io.papermc.paper:paper-api:1.20.1-R0.1-SNAPSHOT'
    
    // Adventure API for Component support
    implementation 'net.kyori:adventure-api:4.14.0'
    implementation 'net.kyori:adventure-text-serializer-plain:4.14.0'
    implementation 'net.kyori:adventure-platform-bukkit:4.3.2'
}

repositories {
    mavenCentral()
    maven {
        name = 'papermc'
        url = 'https://repo.papermc.io/repository/maven-public/'
    }
}
```

## 必要的Import语句

```java
// Adventure Component API
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.TextComponent;
import net.kyori.adventure.text.format.NamedTextColor;
import net.kyori.adventure.text.serializer.legacy.LegacyComponentSerializer;

// Bukkit API
import org.bukkit.Material;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.ItemMeta;
import org.bukkit.entity.Item;
```

## 核心实现代码

### 1. 基础翻译管理器

```java
/**
 * 基于Paper API的物品翻译管理器
 * 使用Component.translatable()实现客户端自动翻译
 */
public class MaterialTranslationManager {
    
    /**
     * 获取物品的Component（支持自定义名称和客户端自动翻译）
     * 
     * @param itemStack 物品堆栈
     * @return 物品的Component，客户端会根据语言自动翻译
     */
    public static Component getItemComponent(ItemStack itemStack) {
        if (itemStack == null) {
            return Component.text("未知物品");
        }
        
        ItemMeta meta = itemStack.getItemMeta();
        // 优先使用自定义displayName（如"&6圣剑"）
        if (meta != null && meta.hasDisplayName()) {
            return Component.text(meta.getDisplayName());
        }
        
        // 使用Material的translationKey让客户端自动翻译
        return Component.translatable(itemStack.getType().translationKey());
    }
    
    /**
     * 获取物品的Component（仅基于Material）
     */
    public static Component getItemComponent(Material material) {
        if (material == null) {
            return Component.text("未知物品");
        }
        
        return Component.translatable(material.translationKey());
    }
    
    /**
     * 获取物品的翻译键
     */
    public static String getTranslationKey(Material material) {
        return material != null ? material.translationKey() : "item.minecraft.barrier";
    }
}
```

### 2. Component消息格式化器

```java
/**
 * 支持Component构建的消息格式化工具
 */
public class MessageFormatter {
    
    /**
     * 构建物品保护显示的Component
     * 
     * @param itemComponent 物品Component（支持自动翻译）
     * @param playerName 玩家名称
     * @param time 时间
     * @param source 物品来源
     * @return 完整的显示Component
     */
    public static Component buildProtectedDisplay(Component itemComponent, String playerName, long time, String source) {
        TextComponent.Builder builder = Component.text()
            .append(Component.text("[", NamedTextColor.WHITE))
            .append(itemComponent) // 物品名称保持原色或客户端翻译色
            .append(Component.text("]", NamedTextColor.WHITE));
        
        // 根据来源添加不同的标签
        if ("mob".equals(source)) {
            builder.append(Component.text("[", NamedTextColor.WHITE))
                   .append(Component.text("击杀者", NamedTextColor.RED))
                   .append(Component.text(playerName, NamedTextColor.GREEN))
                   .append(Component.text("]", NamedTextColor.WHITE));
        } else if ("death".equals(source)) {
            builder.append(Component.text("[", NamedTextColor.WHITE))
                   .append(Component.text("死亡玩家", NamedTextColor.RED))
                   .append(Component.text(playerName, NamedTextColor.GREEN))
                   .append(Component.text("]", NamedTextColor.WHITE));
        } else {
            builder.append(Component.text("[", NamedTextColor.WHITE))
                   .append(Component.text("主人", NamedTextColor.RED))
                   .append(Component.text(playerName, NamedTextColor.GREEN))
                   .append(Component.text("]", NamedTextColor.WHITE));
        }
        
        // 添加时间信息
        builder.append(Component.text("[", NamedTextColor.WHITE))
               .append(Component.text(time + "秒", NamedTextColor.YELLOW))
               .append(Component.text("]", NamedTextColor.WHITE));
        
        return builder.build();
    }
    
    /**
     * 构建未保护物品显示的Component
     */
    public static Component buildUnprotectedDisplay(Component itemComponent, long time) {
        return Component.text()
            .append(Component.text("[", NamedTextColor.WHITE))
            .append(itemComponent) // 物品名称保持原色
            .append(Component.text("]", NamedTextColor.WHITE))
            .append(Component.text("[", NamedTextColor.WHITE))
            .append(Component.text(time + "秒", NamedTextColor.YELLOW))
            .append(Component.text("]", NamedTextColor.WHITE))
            .build();
    }
}
```

### 3. 实际应用示例

```java
/**
 * 在ItemProtectionManager中的应用
 */
public class ItemProtectionManager {
    
    /**
     * 更新物品显示信息
     */
    private void updateItemDisplay(Item item, ProtectedItemData data, long currentTime, int timeoutTime) {
        if (!getConfigBoolean("settings.item-protection.display-info", true)) {
            return;
        }
        
        // 获取物品Component（支持客户端自动翻译）
        Component itemComponent = MaterialTranslationManager.getItemComponent(item.getItemStack());
        
        boolean protectionExpired = currentTime > data.getExpirationTime();
        Component displayComponent;
        
        if (protectionExpired) {
            long dropTime = data.getDropTime();
            long timeoutExpiration = dropTime + (timeoutTime * 1000L);
            long remainingTime = Math.max(0, (timeoutExpiration - currentTime) / 1000);
            displayComponent = MessageFormatter.buildUnprotectedDisplay(itemComponent, remainingTime);
        } else {
            String source = getItemSource(item);
            long remainingTime = Math.max(0, (data.getExpirationTime() - currentTime) / 1000);
            displayComponent = MessageFormatter.buildProtectedDisplay(itemComponent, data.getOwnerName(), remainingTime, source);
        }
        
        // 使用Paper的customName()方法设置Component
        item.customName(displayComponent);
        item.setCustomNameVisible(true);
        item.setGlowing(!protectionExpired);
    }
}
```

## 核心API说明

### Material.translationKey()
```java
// 获取Minecraft标准翻译键
Material.DIAMOND_SWORD.translationKey() → "item.minecraft.diamond_sword"
Material.LEATHER_HORSE_ARMOR.translationKey() → "item.minecraft.leather_horse_armor"
Material.IRON_PICKAXE.translationKey() → "item.minecraft.iron_pickaxe"
```

### Component.translatable()
```java
// 创建可翻译组件，客户端自动处理
Component translatable = Component.translatable("item.minecraft.diamond_sword");

// 客户端显示效果：
// 中文客户端: 钻石剑
// 英文客户端: Diamond Sword
// 日文客户端: ダイヤモンドの剣
// 德文客户端: Diamantschwert
```

### item.customName(Component)
```java
// Paper API扩展方法，支持Component
item.customName(component); // 推荐
item.setCustomName(string); // 传统方法，不推荐
```

## 优先级处理逻辑

```java
public static Component getItemComponent(ItemStack itemStack) {
    // 第一优先级：自定义名称（保留玩家的个性化命名）
    if (meta != null && meta.hasDisplayName()) {
        return Component.text(meta.getDisplayName()); // 如 "&6圣剑"
    }
    
    // 第二优先级：客户端自动翻译（支持所有语言）
    return Component.translatable(material.translationKey());
}
```

## 实际效果展示

### 自定义名称物品
```java
// 物品有自定义名称
ItemStack sword = new ItemStack(Material.DIAMOND_SWORD);
ItemMeta meta = sword.getItemMeta();
meta.setDisplayName("&6传说之剑");
sword.setItemMeta(meta);

// 显示结果：传说之剑 (金色)
Component result = MaterialTranslationManager.getItemComponent(sword);
```

### 普通物品自动翻译
```java
// 普通钻石剑
ItemStack sword = new ItemStack(Material.DIAMOND_SWORD);

// 显示结果：
// 中文客户端: 钻石剑
// 英文客户端: Diamond Sword  
// 日文客户端: ダイヤモンドの剑
Component result = MaterialTranslationManager.getItemComponent(sword);
```

### 复杂格式构建
```java
// 构建完整的保护物品显示
Component itemComponent = MaterialTranslationManager.getItemComponent(itemStack);
Component display = MessageFormatter.buildProtectedDisplay(
    itemComponent, 
    "玩家名", 
    30, 
    "player"
);

// 显示效果：[钻石剑][主人玩家名][30秒]
// 其中"钻石剑"会根据客户端语言自动翻译
```

## 系统优势

1. **零维护成本**: 无需维护翻译文件
2. **全语言支持**: 自动支持Minecraft的所有官方语言
3. **性能最优**: 无文件加载，无内存占用
4. **代码简洁**: 一行代码实现多语言支持
5. **官方标准**: 使用Paper推荐的最佳实践

## 注意事项

1. **只适用于Paper服务端**: 需要Paper API支持
2. **Adventure依赖**: 确保正确添加Adventure API依赖
3. **Component vs String**: 优先使用Component方法而非String方法
4. **向后兼容**: 保留自定义名称的优先级处理

## 迁移指南

### 从静态映射迁移到Component API

```java
// 旧方法（不推荐）
public static String getItemName(Material material) {
    Map<String, String> translations = loadTranslations();
    return translations.getOrDefault(material.name(), formatName(material));
}

// 新方法（推荐）
public static Component getItemComponent(Material material) {
    return Component.translatable(material.translationKey());
}
```

### 更新物品显示设置

```java
// 旧方法（不推荐）
item.setCustomName(colorize("[" + getItemName(material) + "][主人" + player + "]"));

// 新方法（推荐）
Component itemComponent = MaterialTranslationManager.getItemComponent(itemStack);
Component display = MessageFormatter.buildProtectedDisplay(itemComponent, player, time, source);
item.customName(display);
```

这个实现方案完全符合Paper官方推荐的最佳实践，实现了真正的客户端驱动国际化，无需任何服务端翻译文件。