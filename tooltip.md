# 📋 Paper API Tooltip 完整指南

## 🎯 概述

Paper API 的 Tooltip 系统提供了强大的物品提示信息显示功能，允许开发者创建丰富的交互式物品信息展示。

## 📦 核心类和接口

### 🔧 TooltipContext 接口

`TooltipContext` 是计算物品堆栈工具提示的上下文接口。

#### 📋 主要方法

| 方法 | 参数 | 返回值 | 描述 |
|------|------|--------|------|
| `create()` | 无 | `TooltipContext` | 创建一个既不高级也不创造的新上下文 |
| `create(boolean, boolean)` | `advanced`, `creative` | `TooltipContext` | 创建具有指定高级和创造模式设置的新上下文 |
| `isAdvanced()` | 无 | `boolean` | 返回上下文是否用于高级工具提示 |
| `isCreative()` | 无 | `boolean` | 返回上下文是否用于创造模式库存 |
| `asAdvanced()` | 无 | `TooltipContext` | 返回将 `isAdvanced()` 设置为 true 的新上下文 |
| `asCreative()` | 无 | `TooltipContext` | 返回将 `isCreative()` 设置为 true 的新上下文 |

#### 💡 使用示例

```java
// 创建基础上下文
TooltipContext basicContext = TooltipContext.create();

// 创建高级上下文（F3+H 模式）
TooltipContext advancedContext = TooltipContext.create().asAdvanced();

// 创建创造模式上下文
TooltipContext creativeContext = TooltipContext.create().asCreative();

// 创建同时具有高级和创造模式的上下文
TooltipContext fullContext = TooltipContext.create(true, true);
```

### 🎮 ItemStack 中的 Tooltip 方法

#### 🔍 computeTooltipLines 方法

```java
public List<Component> computeTooltipLines(TooltipContext tooltipContext, Player player)
```

**功能**: 计算此物品堆栈的工具提示行

**参数**:
- `tooltipContext`: 工具提示上下文
- `player`: 用于特定玩家工具提示行的玩家（可为 null）

**返回值**: 不可变的组件列表（可能为空）

**注意**: 工具提示内容不保证在不同 Minecraft 版本之间保持一致

#### 💡 使用示例

```java
// 获取基础工具提示
ItemStack item = new ItemStack(Material.DIAMOND_SWORD);
TooltipContext context = TooltipContext.create();
List<Component> tooltipLines = item.computeTooltipLines(context, player);

// 获取高级工具提示（显示更多信息）
TooltipContext advancedContext = TooltipContext.create().asAdvanced();
List<Component> advancedTooltips = item.computeTooltipLines(advancedContext, player);
```

## 🎨 Adventure Component 系统

### 📝 Component 接口

Adventure 的 `Component` 是显示文本的不可变对象，结合了消息内容和样式。

#### 🔧 核心方法

| 方法 | 描述 | 示例 |
|------|------|------|
| `text(String)` | 创建文本组件 | `Component.text("Hello World")` |
| `empty()` | 创建空组件 | `Component.empty()` |
| `append(Component)` | 追加组件 | `component.append(Component.text(" More"))` |
| `color(TextColor)` | 设置颜色 | `component.color(NamedTextColor.RED)` |
| `style(Style)` | 设置样式 | `component.style(Style.style(TextDecoration.BOLD))` |

### 🎯 HoverEvent 悬停事件

#### 📋 主要类型

| 类型 | 方法 | 描述 |
|------|------|------|
| 显示文本 | `HoverEvent.showText(Component)` | 悬停时显示文本 |
| 显示物品 | `HoverEvent.showItem(Key, int)` | 悬停时显示物品信息 |
| 显示实体 | `HoverEvent.showEntity(Key, UUID)` | 悬停时显示实体信息 |

#### 💡 使用示例

```java
// 创建带悬停文本的组件
Component hoverText = Component.text("悬停查看详情")
    .hoverEvent(HoverEvent.showText(Component.text("这是详细信息")));

// 创建显示物品的悬停事件
HoverEvent itemHover = HoverEvent.showItem(
    Key.key("minecraft:diamond_sword"), 
    1
);
Component itemComponent = Component.text("钻石剑")
    .hoverEvent(itemHover);
```

## 🛠️ ItemMeta 中的 Tooltip 相关功能

### 📋 显示名称和描述

| 方法 | 描述 |
|------|------|
| `displayName()` | 获取显示名称 |
| `displayName(Component)` | 设置显示名称 |
| `customName()` | 获取自定义名称 |
| `customName(Component)` | 设置自定义名称 |
| `lore()` | 获取物品描述 |
| `lore(List<Component>)` | 设置物品描述 |

### 🎨 样式相关

| 方法 | 描述 |
|------|------|
| `getTooltipStyle()` | 获取自定义工具提示样式 |
| `setTooltipStyle(NamespacedKey)` | 设置自定义工具提示样式 |
| `hasTooltipStyle()` | 检查是否有工具提示样式 |

## 🚀 实际应用场景

### 🎮 游戏功能实现

#### 1. 🗡️ 武器信息显示
```java
public ItemStack createWeaponWithTooltip(Material material, String name, List<String> lore) {
    ItemStack weapon = new ItemStack(material);
    ItemMeta meta = weapon.getItemMeta();
    
    // 设置显示名称
    meta.displayName(Component.text(name).color(NamedTextColor.GOLD));
    
    // 设置描述
    List<Component> loreComponents = lore.stream()
        .map(line -> Component.text(line).color(NamedTextColor.GRAY))
        .collect(Collectors.toList());
    meta.lore(loreComponents);
    
    weapon.setItemMeta(meta);
    return weapon;
}
```

#### 2. 📊 动态状态显示
```java
public List<Component> createDynamicTooltip(Player player, ItemStack item) {
    TooltipContext context = player.getGameMode() == GameMode.CREATIVE 
        ? TooltipContext.create().asCreative()
        : TooltipContext.create();
    
    // 获取基础工具提示
    List<Component> tooltips = new ArrayList<>(item.computeTooltipLines(context, player));
    
    // 添加自定义信息
    tooltips.add(Component.empty());
    tooltips.add(Component.text("持有者: " + player.getName())
        .color(NamedTextColor.YELLOW));
    
    return tooltips;
}
```

#### 3. 🎯 交互式物品
```java
public Component createInteractiveComponent(String text, String hoverText, String command) {
    return Component.text(text)
        .color(NamedTextColor.AQUA)
        .decoration(TextDecoration.UNDERLINED, true)
        .hoverEvent(HoverEvent.showText(Component.text(hoverText)))
        .clickEvent(ClickEvent.runCommand(command));
}
```

### 🎨 高级样式技巧

#### 1. 🌈 渐变色文本
```java
public Component createGradientText(String text) {
    return Component.text(text)
        .color(TextColor.color(0xFF6B6B))  // 自定义颜色
        .decoration(TextDecoration.BOLD, true);
}
```

#### 2. 📋 多行复杂工具提示
```java
public Component createComplexTooltip() {
    return Component.text("特殊物品")
        .color(NamedTextColor.GOLD)
        .hoverEvent(HoverEvent.showText(
            Component.text("物品详情:\n")
                .color(NamedTextColor.WHITE)
                .append(Component.text("• 攻击力: +10\n").color(NamedTextColor.RED))
                .append(Component.text("• 耐久度: 100/100\n").color(NamedTextColor.GREEN))
                .append(Component.text("• 稀有度: 传说").color(NamedTextColor.LIGHT_PURPLE))
        ));
}
```

## ⚠️ 注意事项和最佳实践

### 🔍 性能考虑
- 工具提示计算可能消耗性能，避免频繁调用
- 缓存静态工具提示内容
- 对于动态内容，考虑异步处理

### 🎯 兼容性
- 工具提示内容在不同 Minecraft 版本间可能不一致
- 测试不同客户端版本的显示效果
- 使用 Paper API 的最新功能时注意版本要求

### 💡 用户体验
- 保持工具提示简洁明了
- 使用一致的颜色方案
- 提供有用的信息而非冗余内容
- 考虑不同语言的本地化需求

## 🎪 高级功能和技巧

### 🔧 自定义工具提示样式

#### 📋 工具提示样式键
```java
// 设置自定义工具提示样式
NamespacedKey customStyle = NamespacedKey.minecraft("custom_tooltip");
meta.setTooltipStyle(customStyle);

// 检查是否有自定义样式
if (meta.hasTooltipStyle()) {
    NamespacedKey style = meta.getTooltipStyle();
    // 处理样式逻辑
}
```

### 🎨 Component 高级操作

#### 🔄 文本替换
```java
public Component replaceTextInTooltip(Component original, String search, String replacement) {
    return original.replaceText(TextReplacementConfig.builder()
        .matchLiteral(search)
        .replacement(Component.text(replacement).color(NamedTextColor.YELLOW))
        .build());
}
```

#### 🎯 条件样式
```java
public Component createConditionalTooltip(Player player, boolean isOwner) {
    Component base = Component.text("物品信息");

    if (isOwner) {
        return base.color(NamedTextColor.GREEN)
            .append(Component.text(" (拥有者)").color(NamedTextColor.GOLD));
    } else {
        return base.color(NamedTextColor.GRAY)
            .append(Component.text(" (访客)").color(NamedTextColor.DARK_GRAY));
    }
}
```

### 📊 数据组件集成

#### 🔧 使用数据组件
```java
// 获取物品的数据组件
Set<DataComponentType> dataTypes = itemStack.getDataTypes();

// 检查特定数据组件
if (itemStack.hasData(DataComponentTypes.CUSTOM_NAME)) {
    Component customName = itemStack.getData(DataComponentTypes.CUSTOM_NAME);
    // 处理自定义名称
}

// 设置数据组件
itemStack.setData(DataComponentTypes.LORE, Arrays.asList(
    Component.text("第一行描述"),
    Component.text("第二行描述")
));
```

## 🎮 实战案例

### 🏆 RPG 装备系统
```java
public class RPGEquipmentTooltip {

    public ItemStack createRPGWeapon(String name, int damage, int durability, String rarity) {
        ItemStack weapon = new ItemStack(Material.DIAMOND_SWORD);
        ItemMeta meta = weapon.getItemMeta();

        // 设置名称（根据稀有度着色）
        TextColor rarityColor = getRarityColor(rarity);
        meta.displayName(Component.text(name).color(rarityColor).decoration(TextDecoration.BOLD, true));

        // 创建详细描述
        List<Component> lore = new ArrayList<>();
        lore.add(Component.empty());
        lore.add(Component.text("⚔ 攻击力: " + damage).color(NamedTextColor.RED));
        lore.add(Component.text("🛡 耐久度: " + durability).color(NamedTextColor.BLUE));
        lore.add(Component.text("✨ 稀有度: " + rarity).color(rarityColor));
        lore.add(Component.empty());
        lore.add(Component.text("右键使用特殊技能").color(NamedTextColor.YELLOW).decoration(TextDecoration.ITALIC, true));

        meta.lore(lore);
        weapon.setItemMeta(meta);

        return weapon;
    }

    private TextColor getRarityColor(String rarity) {
        return switch (rarity.toLowerCase()) {
            case "普通" -> NamedTextColor.WHITE;
            case "稀有" -> NamedTextColor.BLUE;
            case "史诗" -> NamedTextColor.LIGHT_PURPLE;
            case "传说" -> NamedTextColor.GOLD;
            default -> NamedTextColor.GRAY;
        };
    }
}
```

### 🏪 商店系统
```java
public class ShopTooltipSystem {

    public Component createShopItemTooltip(ItemStack item, double price, boolean canAfford) {
        List<Component> tooltipLines = item.computeTooltipLines(TooltipContext.create(), null);

        // 添加价格信息
        tooltipLines.add(Component.empty());
        tooltipLines.add(Component.text("💰 价格: $" + price)
            .color(canAfford ? NamedTextColor.GREEN : NamedTextColor.RED));

        if (canAfford) {
            tooltipLines.add(Component.text("点击购买").color(NamedTextColor.YELLOW));
        } else {
            tooltipLines.add(Component.text("金币不足").color(NamedTextColor.RED));
        }

        // 组合所有行
        Component result = Component.empty();
        for (int i = 0; i < tooltipLines.size(); i++) {
            result = result.append(tooltipLines.get(i));
            if (i < tooltipLines.size() - 1) {
                result = result.appendNewline();
            }
        }

        return result;
    }
}
```

### 🎯 技能系统
```java
public class SkillTooltipManager {

    public ItemStack createSkillBook(String skillName, int level, int maxLevel, String description) {
        ItemStack book = new ItemStack(Material.ENCHANTED_BOOK);
        ItemMeta meta = book.getItemMeta();

        // 技能名称
        meta.displayName(Component.text(skillName + " " + toRoman(level))
            .color(NamedTextColor.AQUA)
            .decoration(TextDecoration.BOLD, true));

        // 技能描述
        List<Component> lore = new ArrayList<>();
        lore.add(Component.empty());
        lore.add(Component.text("📖 技能描述:").color(NamedTextColor.GOLD));

        // 分割长描述为多行
        String[] descLines = description.split("\\n");
        for (String line : descLines) {
            lore.add(Component.text("  " + line).color(NamedTextColor.GRAY));
        }

        lore.add(Component.empty());

        // 等级进度条
        Component progressBar = createProgressBar(level, maxLevel);
        lore.add(Component.text("📊 等级: ").color(NamedTextColor.YELLOW).append(progressBar));

        // 升级提示
        if (level < maxLevel) {
            lore.add(Component.text("右键升级技能").color(NamedTextColor.GREEN).decoration(TextDecoration.ITALIC, true));
        } else {
            lore.add(Component.text("已达到最高等级").color(NamedTextColor.GOLD).decoration(TextDecoration.ITALIC, true));
        }

        meta.lore(lore);
        book.setItemMeta(meta);

        return book;
    }

    private Component createProgressBar(int current, int max) {
        int barLength = 10;
        int filled = (int) ((double) current / max * barLength);

        Component bar = Component.empty();
        for (int i = 0; i < barLength; i++) {
            if (i < filled) {
                bar = bar.append(Component.text("█").color(NamedTextColor.GREEN));
            } else {
                bar = bar.append(Component.text("█").color(NamedTextColor.DARK_GRAY));
            }
        }

        return bar.append(Component.text(" (" + current + "/" + max + ")").color(NamedTextColor.WHITE));
    }

    private String toRoman(int number) {
        String[] romanNumerals = {"I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX", "X"};
        return number <= romanNumerals.length ? romanNumerals[number - 1] : String.valueOf(number);
    }
}
```

## 🔧 调试和测试

### 🐛 常见问题解决

#### 1. 工具提示不显示
```java
// 检查工具提示上下文
TooltipContext context = TooltipContext.create();
List<Component> lines = item.computeTooltipLines(context, player);
if (lines.isEmpty()) {
    // 物品可能没有工具提示数据
    System.out.println("物品没有工具提示数据");
}
```

#### 2. 颜色不正确
```java
// 确保使用正确的颜色格式
Component text = Component.text("测试文本")
    .color(TextColor.color(0xFF5555));  // 使用十六进制颜色
```

#### 3. 样式冲突
```java
// 清除现有样式后重新设置
Component clean = Component.text("文本")
    .style(Style.empty())  // 清空样式
    .color(NamedTextColor.RED);
```

### 🧪 测试工具
```java
public class TooltipTester {

    public void testTooltipGeneration(Player player) {
        ItemStack testItem = new ItemStack(Material.DIAMOND);

        // 测试不同上下文
        TooltipContext[] contexts = {
            TooltipContext.create(),
            TooltipContext.create().asAdvanced(),
            TooltipContext.create().asCreative(),
            TooltipContext.create(true, true)
        };

        for (TooltipContext context : contexts) {
            List<Component> lines = testItem.computeTooltipLines(context, player);
            player.sendMessage("上下文: " + context.toString());
            lines.forEach(line -> player.sendMessage(line));
            player.sendMessage("---");
        }
    }
}
```

## 🔗 相关资源

- [Paper API 官方文档](https://jd.papermc.io/paper/1.21.5/)
- [Adventure 文档](https://docs.advntr.dev/)
- [Minecraft Wiki - 物品格式](https://minecraft.wiki/w/Item_format)
- [Paper API GitHub](https://github.com/PaperMC/Paper)
- [Adventure GitHub](https://github.com/KyoriPowered/adventure)
