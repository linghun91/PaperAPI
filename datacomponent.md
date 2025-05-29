# 📦 Paper API DataComponent 完整指南

## 🌟 概述

DataComponent 是 Paper API 1.21.5 中引入的强大物品数据系统，允许开发者精确控制物品的各种属性和行为。这个系统替代了传统的 ItemMeta 系统，提供了更灵活和类型安全的物品数据管理方式。

## 🏗️ 核心架构

### 📋 主要接口

#### DataComponentView
- **功能**: 只读数据组件视图
- **方法**:
  - `getData(DataComponentType<T> type)` - 获取组件数据
  - `getDataOrDefault(DataComponentType<T> type, T fallback)` - 获取数据或默认值
  - `hasData(DataComponentType type)` - 检查是否存在组件

#### DataComponentHolder
- **功能**: 可变数据组件容器
- **继承**: DataComponentView
- **方法**:
  - `setData(DataComponentType<T> type, T value)` - 设置组件数据
  - `setData(DataComponentType.NonValued type)` - 设置无值组件

#### DataComponentBuilder
- **功能**: 组件构建器基类
- **用途**: 构建复杂的数据组件

## 🎯 核心组件类型

### 🍖 食物系统 (FOOD)
```java
// 创建食物属性
FoodProperties food = FoodProperties.food()
    .nutrition(4)           // 恢复饥饿值
    .saturation(2.4f)       // 饱和度
    .canAlwaysEat(true)     // 总是可以吃
    .build();

itemStack.setData(DataComponentTypes.FOOD, food);
```

### ⚔️ 工具系统 (TOOL)
```java
// 创建工具属性
Tool tool = Tool.tool()
    .defaultMiningSpeed(1.0f)
    .damagePerBlock(1)
    .canDestroyBlocksInCreative(true)
    .addRule(Tool.rule(
        RegistryKeySet.of(BlockType.STONE),
        4.0f,  // 挖掘速度
        TriState.TRUE  // 正确掉落
    ))
    .build();

itemStack.setData(DataComponentTypes.TOOL, tool);
```

### ✨ 附魔系统 (ENCHANTMENTS)
```java
// 创建附魔
Map<Enchantment, Integer> enchants = Map.of(
    Enchantment.SHARPNESS, 5,
    Enchantment.UNBREAKING, 3
);
ItemEnchantments enchantments = ItemEnchantments.itemEnchantments(enchants);

itemStack.setData(DataComponentTypes.ENCHANTMENTS, enchantments);
```

### 🧪 药水系统 (POTION_CONTENTS)
```java
// 创建药水内容
PotionContents potion = PotionContents.potionContents()
    .potion(PotionType.HEALING)
    .customColor(Color.RED)
    .addCustomEffect(new PotionEffect(PotionEffectType.SPEED, 200, 1))
    .customName("super_healing")
    .build();

itemStack.setData(DataComponentTypes.POTION_CONTENTS, potion);
```

## 🎨 外观与显示

### 🏷️ 名称与描述 (CUSTOM_NAME, ITEM_NAME, LORE)
```java
// 自定义名称 (可在铁砧修改)
itemStack.setData(DataComponentTypes.CUSTOM_NAME, 
    Component.text("神圣之剑").color(NamedTextColor.GOLD));

// 物品名称 (不可修改)
itemStack.setData(DataComponentTypes.ITEM_NAME, 
    Component.text("传说武器"));

// 物品描述
ItemLore lore = ItemLore.lore()
    .addLine(Component.text("一把传说中的武器"))
    .addLine(Component.text("拥有神秘的力量"))
    .build();
itemStack.setData(DataComponentTypes.LORE, lore);
```

### 🎭 自定义模型 (CUSTOM_MODEL_DATA)
```java
// 设置自定义模型数据
CustomModelData modelData = CustomModelData.customModelData()
    .addFloat(1.5f)
    .addFlag(true)
    .addString("special_sword")
    .build();

itemStack.setData(DataComponentTypes.CUSTOM_MODEL_DATA, modelData);
```

### 🌈 染色系统 (DYED_COLOR)
```java
// 染色物品
DyedItemColor dyedColor = DyedItemColor.dyedItemColor()
    .color(Color.PURPLE)
    .showInTooltip(true)
    .build();

itemStack.setData(DataComponentTypes.DYED_COLOR, dyedColor);
```

## 🛡️ 耐久与防护

### 💪 耐久系统 (MAX_DAMAGE, DAMAGE, UNBREAKABLE)
```java
// 设置最大耐久
itemStack.setData(DataComponentTypes.MAX_DAMAGE, 1000);

// 设置当前损坏值
itemStack.setData(DataComponentTypes.DAMAGE, 100);

// 设置无法破坏
itemStack.setData(DataComponentTypes.UNBREAKABLE);
```

### 🛡️ 伤害抗性 (DAMAGE_RESISTANT)
```java
// 创建伤害抗性
DamageResistant resistant = DamageResistant.damageResistant()
    .addType(DamageType.FIRE)
    .addType(DamageType.EXPLOSION)
    .build();

itemStack.setData(DataComponentTypes.DAMAGE_RESISTANT, resistant);
```

## 📦 容器与存储

### 🎒 束包内容 (BUNDLE_CONTENTS)
```java
// 创建束包内容
BundleContents bundle = BundleContents.bundleContents()
    .addItem(new ItemStack(Material.DIAMOND, 5))
    .addItem(new ItemStack(Material.GOLD_INGOT, 10))
    .build();

itemStack.setData(DataComponentTypes.BUNDLE_CONTENTS, bundle);
```

### 📦 容器内容 (CONTAINER)
```java
// 创建容器内容
ItemContainerContents container = ItemContainerContents.containerContents()
    .setItem(0, new ItemStack(Material.DIAMOND_SWORD))
    .setItem(1, new ItemStack(Material.SHIELD))
    .build();

itemStack.setData(DataComponentTypes.CONTAINER, container);
```

## 🎯 特殊功能

### 🏹 弩箭装填 (CHARGED_PROJECTILES)
```java
// 装填弩箭
ChargedProjectiles projectiles = ChargedProjectiles.chargedProjectiles()
    .addProjectile(new ItemStack(Material.ARROW, 1))
    .addProjectile(new ItemStack(Material.SPECTRAL_ARROW, 1))
    .build();

itemStack.setData(DataComponentTypes.CHARGED_PROJECTILES, projectiles);
```

### 🧭 磁石追踪 (LODESTONE_TRACKER)
```java
// 设置磁石指南针
LodestoneTracker tracker = LodestoneTracker.lodestoneTracker()
    .target(new Location(world, 100, 64, 200))
    .tracked(true)
    .build();

itemStack.setData(DataComponentTypes.LODESTONE_TRACKER, tracker);
```

### 🎆 烟花效果 (FIREWORKS, FIREWORK_EXPLOSION)
```java
// 创建烟花
Fireworks fireworks = Fireworks.fireworks()
    .flightDuration(2)
    .addExplosion(FireworkEffect.builder()
        .with(FireworkEffect.Type.BALL)
        .withColor(Color.RED)
        .withFade(Color.BLUE)
        .build())
    .build();

itemStack.setData(DataComponentTypes.FIREWORKS, fireworks);
```

## 🎮 游戏机制

### 🍽️ 消耗系统 (CONSUMABLE)
```java
// 创建消耗属性
Consumable consumable = Consumable.consumable()
    .consumeSeconds(1.6f)
    .animation(ItemUseAnimation.EAT)
    .sound(Sound.ENTITY_GENERIC_EAT)
    .hasConsumeParticles(true)
    .addConsumeEffect(ConsumeEffect.applyStatusEffects()
        .addEffect(new PotionEffect(PotionEffectType.REGENERATION, 100, 1))
        .probability(1.0f)
        .build())
    .build();

itemStack.setData(DataComponentTypes.CONSUMABLE, consumable);
```

### ⚔️ 武器系统 (WEAPON)
```java
// 创建武器属性
Weapon weapon = Weapon.weapon()
    .attackDamage(8.0f)
    .attackSpeed(1.6f)
    .build();

itemStack.setData(DataComponentTypes.WEAPON, weapon);
```

### 👕 装备系统 (EQUIPPABLE)
```java
// 创建装备属性
Equippable equippable = Equippable.equippable()
    .slot(EquipmentSlot.HEAD)
    .equipSound(Sound.ITEM_ARMOR_EQUIP_DIAMOND)
    .model(Key.key("custom_helmet"))
    .cameraOverlay(Key.key("pumpkin_blur"))
    .allowedEntities(RegistryKeySet.of(EntityType.PLAYER))
    .dispensable(true)
    .swappable(true)
    .damageOnHurt(true)
    .build();

itemStack.setData(DataComponentTypes.EQUIPPABLE, equippable);
```

## 🎨 高级功能

### 📚 书籍系统 (WRITABLE_BOOK_CONTENT, WRITTEN_BOOK_CONTENT)
```java
// 可写书籍
WritableBookContent writableBook = WritableBookContent.writableBookContent()
    .addPage("第一页内容")
    .addPage("第二页内容")
    .build();

// 已写书籍
WrittenBookContent writtenBook = WrittenBookContent.writtenBookContent()
    .title("我的冒险日记")
    .author("玩家名")
    .generation(BookMeta.Generation.ORIGINAL)
    .addPage(Component.text("这是我的冒险故事..."))
    .resolved(true)
    .build();
```

### 🗺️ 地图系统 (MAP_ID, MAP_DECORATIONS, MAP_COLOR)
```java
// 地图ID
itemStack.setData(DataComponentTypes.MAP_ID, MapId.mapId(1));

// 地图装饰
MapDecorations decorations = MapDecorations.mapDecorations()
    .addDecoration("spawn", MapDecorations.DecorationEntry.decorationEntry()
        .type(MapCursor.Type.RED_MARKER)
        .x(0.0)
        .z(0.0)
        .rotation(0.0f)
        .build())
    .build();

// 地图颜色
MapItemColor mapColor = MapItemColor.mapItemColor()
    .color(Color.GREEN)
    .build();
```

## 🔧 实用工具方法

### 📋 组件检查与操作
```java
// 检查是否有组件
if (itemStack.hasData(DataComponentTypes.FOOD)) {
    FoodProperties food = itemStack.getData(DataComponentTypes.FOOD);
    // 处理食物属性
}

// 获取带默认值的数据
int maxDamage = itemStack.getDataOrDefault(DataComponentTypes.MAX_DAMAGE, 0);

// 批量设置组件
itemStack.setData(DataComponentTypes.CUSTOM_NAME, Component.text("特殊物品"));
itemStack.setData(DataComponentTypes.RARITY, ItemRarity.EPIC);
itemStack.setData(DataComponentTypes.ENCHANTMENT_GLINT_OVERRIDE, true);
```

### 🎯 组件构建器模式
```java
// 使用构建器创建复杂组件
ItemAttributeModifiers attributes = ItemAttributeModifiers.itemAttributeModifiers()
    .addEntry(ItemAttributeModifiers.Entry.entry()
        .attribute(Attribute.GENERIC_ATTACK_DAMAGE)
        .modifier(AttributeModifier.modifier()
            .name("weapon_damage")
            .amount(10.0)
            .operation(AttributeModifier.Operation.ADD_NUMBER)
            .build())
        .slot(EquipmentSlotGroup.MAINHAND)
        .build())
    .showInTooltip(true)
    .build();

itemStack.setData(DataComponentTypes.ATTRIBUTE_MODIFIERS, attributes);
```

## 🚀 最佳实践

### ✅ 推荐做法
1. **类型安全**: 使用泛型确保类型安全
2. **构建器模式**: 使用构建器创建复杂组件
3. **空值检查**: 始终检查组件是否存在
4. **性能优化**: 缓存常用的组件实例

### ❌ 避免的做法
1. **直接修改**: 不要直接修改返回的组件对象
2. **频繁创建**: 避免频繁创建相同的组件
3. **忽略验证**: 不要忽略组件数据的验证

## 🎉 创意应用示例

### 🗡️ 创建传说武器
```java
public ItemStack createLegendarySword() {
    ItemStack sword = new ItemStack(Material.NETHERITE_SWORD);
    
    // 基础属性
    sword.setData(DataComponentTypes.CUSTOM_NAME, 
        Component.text("龙牙之刃").color(NamedTextColor.GOLD));
    sword.setData(DataComponentTypes.RARITY, ItemRarity.EPIC);
    sword.setData(DataComponentTypes.ENCHANTMENT_GLINT_OVERRIDE, true);
    
    // 描述
    ItemLore lore = ItemLore.lore()
        .addLine(Component.text("传说中的神器").color(NamedTextColor.GRAY))
        .addLine(Component.text("拥有毁天灭地的力量").color(NamedTextColor.DARK_PURPLE))
        .build();
    sword.setData(DataComponentTypes.LORE, lore);
    
    // 附魔
    Map<Enchantment, Integer> enchants = Map.of(
        Enchantment.SHARPNESS, 10,
        Enchantment.UNBREAKING, 5,
        Enchantment.FIRE_ASPECT, 3
    );
    sword.setData(DataComponentTypes.ENCHANTMENTS, 
        ItemEnchantments.itemEnchantments(enchants));
    
    // 属性修饰符
    ItemAttributeModifiers attributes = ItemAttributeModifiers.itemAttributeModifiers()
        .addEntry(ItemAttributeModifiers.Entry.entry()
            .attribute(Attribute.GENERIC_ATTACK_DAMAGE)
            .modifier(AttributeModifier.modifier()
                .name("legendary_damage")
                .amount(15.0)
                .operation(AttributeModifier.Operation.ADD_NUMBER)
                .build())
            .slot(EquipmentSlotGroup.MAINHAND)
            .build())
        .build();
    sword.setData(DataComponentTypes.ATTRIBUTE_MODIFIERS, attributes);
    
    return sword;
}
```

## 🐾 生物变种系统

### 🐺 动物变种组件
```java
// 狼的变种
itemStack.setData(DataComponentTypes.WOLF_VARIANT, Wolf.Variant.PALE);
itemStack.setData(DataComponentTypes.WOLF_SOUND_VARIANT, Wolf.SoundVariant.AGGRESSIVE);
itemStack.setData(DataComponentTypes.WOLF_COLLAR, DyeColor.RED);

// 猫的变种
itemStack.setData(DataComponentTypes.CAT_VARIANT, Cat.Type.SIAMESE);
itemStack.setData(DataComponentTypes.CAT_COLLAR, DyeColor.BLUE);

// 其他动物变种
itemStack.setData(DataComponentTypes.HORSE_VARIANT, Horse.Color.BROWN);
itemStack.setData(DataComponentTypes.LLAMA_VARIANT, Llama.Color.BROWN);
itemStack.setData(DataComponentTypes.PARROT_VARIANT, Parrot.Variant.RED);
itemStack.setData(DataComponentTypes.RABBIT_VARIANT, Rabbit.Type.BROWN);
itemStack.setData(DataComponentTypes.TROPICAL_FISH_PATTERN, TropicalFish.Pattern.BETTY);
itemStack.setData(DataComponentTypes.TROPICAL_FISH_BASE_COLOR, DyeColor.ORANGE);
itemStack.setData(DataComponentTypes.TROPICAL_FISH_PATTERN_COLOR, DyeColor.WHITE);
```

## 🎨 装饰与美化

### 🏺 花盆装饰 (POT_DECORATIONS)
```java
// 创建装饰花盆
PotDecorations decorations = PotDecorations.potDecorations()
    .back(Material.BRICK)
    .left(Material.HEART_POTTERY_SHERD)
    .right(Material.EXPLORER_POTTERY_SHERD)
    .front(Material.FRIEND_POTTERY_SHERD)
    .build();

itemStack.setData(DataComponentTypes.POT_DECORATIONS, decorations);
```

### 🎨 旗帜图案 (BANNER_PATTERNS)
```java
// 创建旗帜图案
BannerPatternLayers patterns = BannerPatternLayers.bannerPatternLayers()
    .addLayer(PatternType.STRIPE_BOTTOM, DyeColor.RED)
    .addLayer(PatternType.STRIPE_TOP, DyeColor.BLUE)
    .addLayer(PatternType.CROSS, DyeColor.WHITE)
    .build();

itemStack.setData(DataComponentTypes.BANNER_PATTERNS, patterns);
itemStack.setData(DataComponentTypes.BASE_COLOR, DyeColor.BLACK);
```

### 🖼️ 画作变种 (PAINTING_VARIANT)
```java
// 设置画作类型
itemStack.setData(DataComponentTypes.PAINTING_VARIANT, Art.KEBAB);
```

## 🔧 高级机制

### 🎯 冒险模式限制 (CAN_PLACE_ON, CAN_BREAK)
```java
// 创建冒险模式谓词
ItemAdventurePredicate placeOn = ItemAdventurePredicate.itemAdventurePredicate()
    .addBlock(BlockType.STONE)
    .addBlock(BlockType.DIRT)
    .showInTooltip(true)
    .build();

ItemAdventurePredicate canBreak = ItemAdventurePredicate.itemAdventurePredicate()
    .addBlock(BlockType.COBBLESTONE)
    .addBlock(BlockType.WOOD)
    .showInTooltip(true)
    .build();

itemStack.setData(DataComponentTypes.CAN_PLACE_ON, placeOn);
itemStack.setData(DataComponentTypes.CAN_BREAK, canBreak);
```

### 🛡️ 盾牌阻挡 (BLOCKS_ATTACKS)
```java
// 创建攻击阻挡
BlocksAttacks blocksAttacks = BlocksAttacks.blocksAttacks()
    .addBlockedDamageType(DamageType.MOB_ATTACK)
    .addBlockedDamageType(DamageType.PLAYER_ATTACK)
    .build();

itemStack.setData(DataComponentTypes.BLOCKS_ATTACKS, blocksAttacks);
```

### 💀 死亡保护 (DEATH_PROTECTION)
```java
// 创建死亡保护
DeathProtection protection = DeathProtection.deathProtection()
    .addDeathEffect(ConsumeEffect.playSound()
        .sound(Sound.ITEM_TOTEM_USE)
        .build())
    .addDeathEffect(ConsumeEffect.applyStatusEffects()
        .addEffect(new PotionEffect(PotionEffectType.REGENERATION, 900, 1))
        .addEffect(new PotionEffect(PotionEffectType.ABSORPTION, 100, 1))
        .probability(1.0f)
        .build())
    .build();

itemStack.setData(DataComponentTypes.DEATH_PROTECTION, protection);
```

## 🎵 音效与音乐

### 🎺 乐器系统 (INSTRUMENT)
```java
// 设置山羊角乐器
itemStack.setData(DataComponentTypes.INSTRUMENT, MusicInstrument.PONDER_GOAT_HORN);
```

### 🎵 唱片机播放 (JUKEBOX_PLAYABLE)
```java
// 创建唱片机可播放
JukeboxPlayable playable = JukeboxPlayable.jukeboxPlayable()
    .song(Key.key("minecraft:pigstep"))
    .showInTooltip(true)
    .build();

itemStack.setData(DataComponentTypes.JUKEBOX_PLAYABLE, playable);
```

### 🔊 音符盒音效 (NOTE_BLOCK_SOUND)
```java
// 设置音符盒音效
itemStack.setData(DataComponentTypes.NOTE_BLOCK_SOUND,
    Key.key("minecraft:block.note_block.bell"));
```

## 📖 知识与配方

### 📚 知识之书 (RECIPES)
```java
// 设置解锁的配方
List<Key> recipes = List.of(
    Key.key("minecraft:diamond_sword"),
    Key.key("minecraft:diamond_pickaxe"),
    Key.key("minecraft:enchanting_table")
);

itemStack.setData(DataComponentTypes.RECIPES, recipes);
```

## 🧪 特殊药水

### 🌟 不祥之瓶 (OMINOUS_BOTTLE_AMPLIFIER)
```java
// 设置不祥之瓶强度
OminousBottleAmplifier amplifier = OminousBottleAmplifier.ominousBottleAmplifier()
    .amplifier(2)
    .build();

itemStack.setData(DataComponentTypes.OMINOUS_BOTTLE_AMPLIFIER, amplifier);
```

### 🍄 迷之炖菜 (SUSPICIOUS_STEW_EFFECTS)
```java
// 创建迷之炖菜效果
SuspiciousStewEffects stewEffects = SuspiciousStewEffects.suspiciousStewEffects()
    .addEffect(new PotionEffect(PotionEffectType.NIGHT_VISION, 100, 0))
    .addEffect(new PotionEffect(PotionEffectType.JUMP, 200, 1))
    .build();

itemStack.setData(DataComponentTypes.SUSPICIOUS_STEW_EFFECTS, stewEffects);
```

## 🔄 使用机制

### ⏱️ 使用冷却 (USE_COOLDOWN)
```java
// 设置使用冷却
UseCooldown cooldown = UseCooldown.useCooldown()
    .seconds(5.0f)
    .cooldownGroup(Key.key("custom:special_items"))
    .build();

itemStack.setData(DataComponentTypes.USE_COOLDOWN, cooldown);
```

### 🔄 使用剩余 (USE_REMAINDER)
```java
// 设置使用后剩余物品
UseRemainder remainder = UseRemainder.useRemainder()
    .item(new ItemStack(Material.GLASS_BOTTLE))
    .build();

itemStack.setData(DataComponentTypes.USE_REMAINDER, remainder);
```

## 🎨 视觉效果

### ✨ 附魔光效覆盖 (ENCHANTMENT_GLINT_OVERRIDE)
```java
// 强制显示附魔光效
itemStack.setData(DataComponentTypes.ENCHANTMENT_GLINT_OVERRIDE, true);

// 强制隐藏附魔光效
itemStack.setData(DataComponentTypes.ENCHANTMENT_GLINT_OVERRIDE, false);
```

### 🎨 工具提示样式 (TOOLTIP_STYLE, TOOLTIP_DISPLAY)
```java
// 设置工具提示样式
itemStack.setData(DataComponentTypes.TOOLTIP_STYLE,
    Key.key("minecraft:indestructible"));

// 自定义工具提示显示
TooltipDisplay tooltipDisplay = TooltipDisplay.tooltipDisplay()
    .hideTooltip(false)
    .hideAdditionalTooltip(true)
    .build();

itemStack.setData(DataComponentTypes.TOOLTIP_DISPLAY, tooltipDisplay);
```

## 🔧 修复与维护

### 🔨 可修复性 (REPAIRABLE)
```java
// 设置可修复材料
Repairable repairable = Repairable.repairable()
    .addRepairMaterial(Material.DIAMOND)
    .addRepairMaterial(Material.NETHERITE_INGOT)
    .build();

itemStack.setData(DataComponentTypes.REPAIRABLE, repairable);
```

### 💰 修复成本 (REPAIR_COST)
```java
// 设置铁砧修复成本
itemStack.setData(DataComponentTypes.REPAIR_COST, 5);
```

## 🎮 游戏性增强

### 🪂 滑翔能力 (GLIDER)
```java
// 设置滑翔能力
itemStack.setData(DataComponentTypes.GLIDER);
```

### 👻 无形弹射物 (INTANGIBLE_PROJECTILE)
```java
// 设置无形弹射物
itemStack.setData(DataComponentTypes.INTANGIBLE_PROJECTILE);
```

### 🎯 附魔能力 (ENCHANTABLE)
```java
// 设置附魔能力
Enchantable enchantable = Enchantable.enchantable()
    .value(15)  // 附魔能力值
    .build();

itemStack.setData(DataComponentTypes.ENCHANTABLE, enchantable);
```

## 🏗️ 方块数据

### 🧱 方块数据属性 (BLOCK_DATA)
```java
// 设置方块数据属性
BlockItemDataProperties blockData = BlockItemDataProperties.blockItemDataProperties()
    .putProperty("facing", "north")
    .putProperty("powered", "true")
    .build();

itemStack.setData(DataComponentTypes.BLOCK_DATA, blockData);
```

### 🎁 容器战利品 (CONTAINER_LOOT)
```java
// 设置容器战利品
SeededContainerLoot loot = SeededContainerLoot.seededContainerLoot()
    .lootTable(Key.key("minecraft:chests/simple_dungeon"))
    .seed(12345L)
    .build();

itemStack.setData(DataComponentTypes.CONTAINER_LOOT, loot);
```

## 🔊 音效系统

### 💥 破坏音效 (BREAK_SOUND)
```java
// 设置破坏音效
itemStack.setData(DataComponentTypes.BREAK_SOUND,
    Key.key("minecraft:block.glass.break"));
```

## 🎯 完整示例：创建多功能工具

```java
public ItemStack createMultiTool() {
    ItemStack tool = new ItemStack(Material.NETHERITE_PICKAXE);

    // 基础信息
    tool.setData(DataComponentTypes.CUSTOM_NAME,
        Component.text("万能工具").color(NamedTextColor.AQUA));
    tool.setData(DataComponentTypes.RARITY, ItemRarity.EPIC);

    // 工具属性
    Tool toolComponent = Tool.tool()
        .defaultMiningSpeed(8.0f)
        .damagePerBlock(0)  // 不消耗耐久
        .canDestroyBlocksInCreative(true)
        .addRule(Tool.rule(
            RegistryKeySet.of(BlockType.STONE, BlockType.DIRT, BlockType.WOOD),
            15.0f,
            TriState.TRUE
        ))
        .build();
    tool.setData(DataComponentTypes.TOOL, toolComponent);

    // 无法破坏
    tool.setData(DataComponentTypes.UNBREAKABLE);

    // 附魔
    Map<Enchantment, Integer> enchants = Map.of(
        Enchantment.EFFICIENCY, 10,
        Enchantment.FORTUNE, 5,
        Enchantment.SILK_TOUCH, 1
    );
    tool.setData(DataComponentTypes.ENCHANTMENTS,
        ItemEnchantments.itemEnchantments(enchants));

    // 描述
    ItemLore lore = ItemLore.lore()
        .addLine(Component.text("一把神奇的工具").color(NamedTextColor.GRAY))
        .addLine(Component.text("可以挖掘任何方块").color(NamedTextColor.GREEN))
        .addLine(Component.text("永不损坏").color(NamedTextColor.GOLD))
        .build();
    tool.setData(DataComponentTypes.LORE, lore);

    return tool;
}
```

## 📊 完整组件类型参考表

### 🎯 基础属性组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `MAX_STACK_SIZE` | 📦 | 最大堆叠数量 | Integer (1-99) |
| `MAX_DAMAGE` | 💪 | 最大耐久值 | Integer |
| `DAMAGE` | 💔 | 当前损坏值 | Integer |
| `UNBREAKABLE` | 🛡️ | 无法破坏 | NonValued |
| `RARITY` | ⭐ | 物品稀有度 | ItemRarity |
| `CUSTOM_NAME` | 🏷️ | 自定义名称 | Component |
| `ITEM_NAME` | 📝 | 物品名称 | Component |
| `LORE` | 📜 | 物品描述 | ItemLore |

### 🎨 外观组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `CUSTOM_MODEL_DATA` | 🎭 | 自定义模型数据 | CustomModelData |
| `DYED_COLOR` | 🌈 | 染色颜色 | DyedItemColor |
| `ENCHANTMENT_GLINT_OVERRIDE` | ✨ | 附魔光效覆盖 | Boolean |
| `ITEM_MODEL` | 🎨 | 物品模型 | Key |
| `TOOLTIP_STYLE` | 💬 | 工具提示样式 | Key |
| `TOOLTIP_DISPLAY` | 📋 | 工具提示显示 | TooltipDisplay |

### ⚔️ 战斗与工具组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `TOOL` | 🔨 | 工具属性 | Tool |
| `WEAPON` | ⚔️ | 武器属性 | Weapon |
| `ENCHANTMENTS` | ✨ | 附魔列表 | ItemEnchantments |
| `STORED_ENCHANTMENTS` | 📚 | 存储附魔 | ItemEnchantments |
| `ATTRIBUTE_MODIFIERS` | 💪 | 属性修饰符 | ItemAttributeModifiers |
| `DAMAGE_RESISTANT` | 🛡️ | 伤害抗性 | DamageResistant |
| `BLOCKS_ATTACKS` | 🛡️ | 阻挡攻击 | BlocksAttacks |

### 🍖 食物与消耗组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `FOOD` | 🍖 | 食物属性 | FoodProperties |
| `CONSUMABLE` | 🍽️ | 消耗属性 | Consumable |
| `POTION_CONTENTS` | 🧪 | 药水内容 | PotionContents |
| `SUSPICIOUS_STEW_EFFECTS` | 🍄 | 迷之炖菜效果 | SuspiciousStewEffects |
| `OMINOUS_BOTTLE_AMPLIFIER` | 🌟 | 不祥之瓶强度 | OminousBottleAmplifier |
| `USE_REMAINDER` | 🔄 | 使用剩余 | UseRemainder |
| `USE_COOLDOWN` | ⏱️ | 使用冷却 | UseCooldown |

### 📦 容器与存储组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `CONTAINER` | 📦 | 容器内容 | ItemContainerContents |
| `BUNDLE_CONTENTS` | 🎒 | 束包内容 | BundleContents |
| `CHARGED_PROJECTILES` | 🏹 | 装填弹射物 | ChargedProjectiles |
| `CONTAINER_LOOT` | 🎁 | 容器战利品 | SeededContainerLoot |

### 🎮 游戏机制组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `CAN_PLACE_ON` | 🎯 | 可放置方块 | ItemAdventurePredicate |
| `CAN_BREAK` | 🎯 | 可破坏方块 | ItemAdventurePredicate |
| `EQUIPPABLE` | 👕 | 装备属性 | Equippable |
| `REPAIRABLE` | 🔨 | 可修复性 | Repairable |
| `REPAIR_COST` | 💰 | 修复成本 | Integer |
| `ENCHANTABLE` | 🎯 | 附魔能力 | Enchantable |
| `GLIDER` | 🪂 | 滑翔能力 | NonValued |
| `INTANGIBLE_PROJECTILE` | 👻 | 无形弹射物 | NonValued |
| `DEATH_PROTECTION` | 💀 | 死亡保护 | DeathProtection |

### 🗺️ 地图与导航组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `MAP_ID` | 🗺️ | 地图ID | MapId |
| `MAP_COLOR` | 🎨 | 地图颜色 | MapItemColor |
| `MAP_DECORATIONS` | 📍 | 地图装饰 | MapDecorations |
| `MAP_POST_PROCESSING` | 🔄 | 地图后处理 | MapPostProcessing |
| `LODESTONE_TRACKER` | 🧭 | 磁石追踪 | LodestoneTracker |

### 🎵 音效与音乐组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `INSTRUMENT` | 🎺 | 乐器类型 | MusicInstrument |
| `JUKEBOX_PLAYABLE` | 🎵 | 唱片机播放 | JukeboxPlayable |
| `NOTE_BLOCK_SOUND` | 🔊 | 音符盒音效 | Key |
| `BREAK_SOUND` | 💥 | 破坏音效 | Key |

### 📚 书籍与知识组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `WRITABLE_BOOK_CONTENT` | 📝 | 可写书内容 | WritableBookContent |
| `WRITTEN_BOOK_CONTENT` | 📖 | 已写书内容 | WrittenBookContent |
| `RECIPES` | 📚 | 配方列表 | List<Key> |

### 🎨 装饰与美化组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `BANNER_PATTERNS` | 🎨 | 旗帜图案 | BannerPatternLayers |
| `BASE_COLOR` | 🎨 | 基础颜色 | DyeColor |
| `POT_DECORATIONS` | 🏺 | 花盆装饰 | PotDecorations |
| `TRIM` | ✨ | 盔甲纹饰 | ItemArmorTrim |
| `FIREWORKS` | 🎆 | 烟花效果 | Fireworks |
| `FIREWORK_EXPLOSION` | 💥 | 烟花爆炸 | FireworkEffect |
| `PROFILE` | 👤 | 玩家头颅 | ResolvableProfile |
| `PAINTING_VARIANT` | 🖼️ | 画作变种 | Art |

### 🐾 生物变种组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `WOLF_VARIANT` | 🐺 | 狼变种 | Wolf.Variant |
| `WOLF_SOUND_VARIANT` | 🔊 | 狼音效变种 | Wolf.SoundVariant |
| `WOLF_COLLAR` | 🎀 | 狼项圈颜色 | DyeColor |
| `CAT_VARIANT` | 🐱 | 猫变种 | Cat.Type |
| `CAT_COLLAR` | 🎀 | 猫项圈颜色 | DyeColor |
| `HORSE_VARIANT` | 🐴 | 马变种 | Horse.Color |
| `LLAMA_VARIANT` | 🦙 | 羊驼变种 | Llama.Color |
| `PARROT_VARIANT` | 🦜 | 鹦鹉变种 | Parrot.Variant |
| `RABBIT_VARIANT` | 🐰 | 兔子变种 | Rabbit.Type |
| `TROPICAL_FISH_PATTERN` | 🐠 | 热带鱼图案 | TropicalFish.Pattern |
| `TROPICAL_FISH_BASE_COLOR` | 🎨 | 热带鱼基色 | DyeColor |
| `TROPICAL_FISH_PATTERN_COLOR` | 🎨 | 热带鱼图案色 | DyeColor |
| `AXOLOTL_VARIANT` | 🦎 | 美西螈变种 | Axolotl.Variant |
| `FROG_VARIANT` | 🐸 | 青蛙变种 | Frog.Variant |
| `MOOSHROOM_VARIANT` | 🍄 | 哞菇变种 | MushroomCow.Variant |
| `SHEEP_COLOR` | 🐑 | 羊毛颜色 | DyeColor |
| `SHULKER_COLOR` | 📦 | 潜影盒颜色 | DyeColor |

### 🏗️ 方块与建筑组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `BLOCK_DATA` | 🧱 | 方块数据 | BlockItemDataProperties |

### 🎁 特殊功能组件
| 组件类型 | 图标 | 功能描述 | 数据类型 |
|---------|------|----------|----------|
| `PROVIDES_TRIM_MATERIAL` | ✨ | 提供纹饰材料 | TrimMaterial |
| `PROVIDES_BANNER_PATTERNS` | 🎨 | 提供旗帜图案 | TagKey<PatternType> |
| `POTION_DURATION_SCALE` | ⏱️ | 药水持续时间缩放 | Float |

## 🎯 总结

DataComponent 系统包含了 **80+** 个不同的组件类型，涵盖了物品的各个方面：

- **🎯 基础属性**: 8个组件控制物品的基本特性
- **🎨 外观定制**: 6个组件控制物品的视觉效果
- **⚔️ 战斗系统**: 7个组件控制战斗相关属性
- **🍖 食物系统**: 7个组件控制消耗和食物属性
- **📦 存储系统**: 4个组件控制容器和存储
- **🎮 游戏机制**: 9个组件控制特殊游戏机制
- **🗺️ 地图导航**: 5个组件控制地图和导航
- **🎵 音效音乐**: 4个组件控制音效和音乐
- **📚 书籍知识**: 3个组件控制书籍和配方
- **🎨 装饰美化**: 10个组件控制装饰效果
- **🐾 生物变种**: 18个组件控制生物外观
- **🏗️ 方块建筑**: 1个组件控制方块属性
- **🎁 特殊功能**: 3个组件提供特殊功能

这个强大的系统为 Minecraft 插件开发提供了前所未有的灵活性和控制力，让开发者能够创造出真正独特和强大的游戏体验！ 🎮✨
