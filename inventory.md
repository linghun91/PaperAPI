# 📦 Paper API Inventory 完整指南

## 📋 目录
- [📦 基础Inventory接口](#-基础inventory接口)
- [🎒 PlayerInventory玩家背包](#-playerinventory玩家背包)
- [📄 ItemStack物品堆叠](#-itemstack物品堆叠)
- [🏷️ ItemMeta物品元数据](#️-itemmeta物品元数据)
- [🔧 InventoryView界面视图](#-inventoryview界面视图)
- [🍳 特殊容器类型](#-特殊容器类型)
- [📜 配方系统](#-配方系统)
- [💡 实用功能示例](#-实用功能示例)

---

## 📦 基础Inventory接口

### 核心方法
```java
// 基本操作
int getSize()                           // 获取背包大小
ItemStack getItem(int index)            // 获取指定位置物品
void setItem(int index, ItemStack item) // 设置指定位置物品
void clear()                           // 清空背包
void clear(int index)                  // 清空指定位置

// 物品管理
HashMap<Integer,ItemStack> addItem(ItemStack... items)     // 添加物品
HashMap<Integer,ItemStack> removeItem(ItemStack... items)  // 移除物品
boolean contains(Material material)                        // 检查是否包含材料
boolean contains(ItemStack item, int amount)              // 检查是否包含指定数量物品

// 查找功能
int first(Material material)           // 查找第一个匹配材料的位置
int firstEmpty()                      // 查找第一个空位置
HashMap<Integer,ItemStack> all(Material material) // 获取所有匹配材料的物品

// 内容操作
ItemStack[] getContents()             // 获取所有内容
void setContents(ItemStack[] items)   // 设置所有内容
ItemStack[] getStorageContents()      // 获取存储内容
void setStorageContents(ItemStack[] items) // 设置存储内容

// 观察者管理
List<HumanEntity> getViewers()        // 获取观察者列表
int close()                          // 关闭背包
InventoryHolder getHolder()          // 获取背包持有者
Location getLocation()               // 获取背包位置
```

### 🎯 实用功能：智能物品管理系统
```java
public class SmartInventoryManager {
    
    // 智能添加物品（自动堆叠）
    public static boolean smartAddItem(Inventory inv, ItemStack item) {
        HashMap<Integer, ItemStack> leftover = inv.addItem(item);
        return leftover.isEmpty();
    }
    
    // 批量整理背包
    public static void organizeInventory(Inventory inv) {
        Map<Material, List<ItemStack>> grouped = new HashMap<>();
        
        // 按材料分组
        for (ItemStack item : inv.getContents()) {
            if (item != null && !item.getType().isAir()) {
                grouped.computeIfAbsent(item.getType(), k -> new ArrayList<>()).add(item);
            }
        }
        
        inv.clear();
        
        // 重新排列
        int slot = 0;
        for (List<ItemStack> items : grouped.values()) {
            for (ItemStack item : items) {
                if (slot < inv.getSize()) {
                    inv.setItem(slot++, item);
                }
            }
        }
    }
}
```

---

## 🎒 PlayerInventory玩家背包

### 装备槽位管理
```java
// 护甲槽位
ItemStack getHelmet()                 // 获取头盔
ItemStack getChestplate()            // 获取胸甲
ItemStack getLeggings()              // 获取护腿
ItemStack getBoots()                 // 获取靴子
void setHelmet(ItemStack helmet)     // 设置头盔
void setChestplate(ItemStack chest)  // 设置胸甲
void setLeggings(ItemStack legs)     // 设置护腿
void setBoots(ItemStack boots)       // 设置靴子

// 手持物品
ItemStack getItemInMainHand()        // 获取主手物品
ItemStack getItemInOffHand()         // 获取副手物品
void setItemInMainHand(ItemStack item) // 设置主手物品
void setItemInOffHand(ItemStack item)  // 设置副手物品

// 快捷栏
int getHeldItemSlot()                // 获取当前选中槽位
void setHeldItemSlot(int slot)       // 设置选中槽位(0-8)

// 装备槽位通用方法
ItemStack getItem(EquipmentSlot slot) // 通过装备槽获取物品
void setItem(EquipmentSlot slot, ItemStack item) // 通过装备槽设置物品
```

### 🛡️ 实用功能：装备管理系统
```java
public class EquipmentManager {
    
    // 自动装备最佳护甲
    public static void equipBestArmor(Player player) {
        PlayerInventory inv = player.getInventory();
        
        ItemStack bestHelmet = findBestArmor(inv, EquipmentSlot.HEAD);
        ItemStack bestChest = findBestArmor(inv, EquipmentSlot.CHEST);
        ItemStack bestLegs = findBestArmor(inv, EquipmentSlot.LEGS);
        ItemStack bestBoots = findBestArmor(inv, EquipmentSlot.FEET);
        
        if (bestHelmet != null) inv.setHelmet(bestHelmet);
        if (bestChest != null) inv.setChestplate(bestChest);
        if (bestLegs != null) inv.setLeggings(bestLegs);
        if (bestBoots != null) inv.setBoots(bestBoots);
    }
    
    // 装备耐久度检查
    public static void checkArmorDurability(Player player) {
        PlayerInventory inv = player.getInventory();
        ItemStack[] armor = inv.getArmorContents();
        
        for (ItemStack piece : armor) {
            if (piece != null && piece.getItemMeta() instanceof Damageable) {
                Damageable meta = (Damageable) piece.getItemMeta();
                int maxDurability = piece.getType().getMaxDurability();
                int damage = meta.getDamage();
                
                double durabilityPercent = (double)(maxDurability - damage) / maxDurability * 100;
                
                if (durabilityPercent < 10) {
                    player.sendMessage("§c警告: " + piece.getType().name() + " 耐久度过低!");
                }
            }
        }
    }
}
```

---

## 📄 ItemStack物品堆叠

### 基础属性
```java
// 构造方法
ItemStack(Material type)                    // 创建物品(数量1)
ItemStack(Material type, int amount)        // 创建指定数量物品
ItemStack(ItemStack stack)                  // 复制物品

// 基本属性
Material getType()                          // 获取材料类型
int getAmount()                            // 获取数量
void setAmount(int amount)                 // 设置数量
int getMaxStackSize()                      // 获取最大堆叠数量

// 物品操作
ItemStack clone()                          // 克隆物品
ItemStack add(int qty)                     // 增加数量
ItemStack subtract(int qty)                // 减少数量
ItemStack asOne()                          // 转为数量1的物品
ItemStack asQuantity(int qty)              // 转为指定数量的物品

// 状态检查
boolean isEmpty()                          // 是否为空
boolean isSimilar(ItemStack stack)        // 是否相似(忽略数量)
boolean equals(Object obj)                 // 是否完全相等
```

### 🔮 附魔系统
```java
// 附魔管理
Map<Enchantment,Integer> getEnchantments()              // 获取所有附魔
int getEnchantmentLevel(Enchantment enchant)            // 获取附魔等级
boolean containsEnchantment(Enchantment enchant)       // 是否包含附魔
void addEnchantment(Enchantment enchant, int level)    // 添加附魔
void addUnsafeEnchantment(Enchantment enchant, int level) // 添加不安全附魔
void removeEnchantment(Enchantment enchant)            // 移除附魔

// 随机附魔
ItemStack enchantWithLevels(int levels, boolean allowTreasure, Random random)

// 物品标志
Set<ItemFlag> getItemFlags()                           // 获取物品标志
void addItemFlags(ItemFlag... flags)                   // 添加物品标志
void removeItemFlags(ItemFlag... flags)                // 移除物品标志
boolean hasItemFlag(ItemFlag flag)                     // 是否有指定标志
```

### 🏷️ 实用功能：物品工厂系统
```java
public class ItemFactory {

    // 创建自定义物品
    public static ItemStack createCustomItem(Material material, String name,
                                            List<String> lore, int amount) {
        ItemStack item = new ItemStack(material, amount);
        ItemMeta meta = item.getItemMeta();

        if (meta != null) {
            meta.setDisplayName(name);
            meta.setLore(lore);
            item.setItemMeta(meta);
        }

        return item;
    }

    // 创建附魔物品
    public static ItemStack createEnchantedItem(Material material,
                                               Map<Enchantment, Integer> enchants) {
        ItemStack item = new ItemStack(material);

        for (Map.Entry<Enchantment, Integer> entry : enchants.entrySet()) {
            item.addUnsafeEnchantment(entry.getKey(), entry.getValue());
        }

        return item;
    }

    // 创建不可破坏物品
    public static ItemStack createUnbreakableItem(Material material, String name) {
        ItemStack item = new ItemStack(material);
        ItemMeta meta = item.getItemMeta();

        if (meta != null) {
            meta.setDisplayName(name);
            meta.setUnbreakable(true);
            meta.addItemFlags(ItemFlag.HIDE_UNBREAKABLE);
            item.setItemMeta(meta);
        }

        return item;
    }
}
```

---

## 🏷️ ItemMeta物品元数据

### 基础元数据
```java
// 显示信息
String getDisplayName()                     // 获取显示名称
void setDisplayName(String name)            // 设置显示名称
List<String> getLore()                      // 获取描述
void setLore(List<String> lore)            // 设置描述
boolean hasDisplayName()                    // 是否有显示名称
boolean hasLore()                          // 是否有描述

// 物品属性
boolean isUnbreakable()                     // 是否不可破坏
void setUnbreakable(boolean unbreakable)    // 设置不可破坏
ItemRarity getRarity()                      // 获取稀有度
boolean hasRarity()                         // 是否有自定义稀有度

// 自定义模型数据
int getCustomModelData()                    // 获取自定义模型数据
void setCustomModelData(Integer data)       // 设置自定义模型数据
boolean hasCustomModelData()                // 是否有自定义模型数据
```

### 🔧 特殊元数据类型

#### 📚 BookMeta (书本)
```java
// 书本内容
String getTitle()                           // 获取标题
void setTitle(String title)                 // 设置标题
String getAuthor()                          // 获取作者
void setAuthor(String author)               // 设置作者
List<String> getPages()                     // 获取页面
void setPages(List<String> pages)           // 设置页面
void addPage(String... pages)               // 添加页面
BookMeta.Generation getGeneration()         // 获取版本
void setGeneration(BookMeta.Generation gen) // 设置版本
```

#### 🧪 PotionMeta (药水)
```java
// 药水效果
PotionData getBasePotionData()              // 获取基础药水数据
void setBasePotionData(PotionData data)     // 设置基础药水数据
List<PotionEffect> getCustomEffects()       // 获取自定义效果
boolean addCustomEffect(PotionEffect effect, boolean overwrite) // 添加自定义效果
boolean removeCustomEffect(PotionEffectType type) // 移除自定义效果
Color getColor()                            // 获取颜色
void setColor(Color color)                  // 设置颜色
```

#### 💀 SkullMeta (头颅)
```java
// 头颅所有者
String getOwner()                           // 获取所有者
boolean setOwner(String owner)              // 设置所有者
OfflinePlayer getOwningPlayer()             // 获取所有者玩家
boolean setOwningPlayer(OfflinePlayer player) // 设置所有者玩家
PlayerProfile getPlayerProfile()            // 获取玩家档案
void setPlayerProfile(PlayerProfile profile) // 设置玩家档案
```

#### 🎆 FireworkMeta (烟花)
```java
// 烟花效果
List<FireworkEffect> getEffects()           // 获取效果列表
void addEffect(FireworkEffect effect)       // 添加效果
void addEffects(FireworkEffect... effects)  // 添加多个效果
void clearEffects()                         // 清除所有效果
int getPower()                              // 获取威力
void setPower(int power)                    // 设置威力
```

---

## 🔧 InventoryView界面视图

### 基础视图操作
```java
// 背包访问
Inventory getTopInventory()                 // 获取上层背包
Inventory getBottomInventory()              // 获取下层背包
HumanEntity getPlayer()                     // 获取玩家
InventoryType getType()                     // 获取背包类型

// 物品操作
ItemStack getItem(int slot)                 // 获取指定槽位物品
void setItem(int slot, ItemStack item)      // 设置指定槽位物品
ItemStack getCursor()                       // 获取光标物品
void setCursor(ItemStack item)              // 设置光标物品

// 槽位转换
int convertSlot(int rawSlot)                // 转换槽位ID
Inventory getInventory(int rawSlot)         // 获取槽位对应背包
InventoryType.SlotType getSlotType(int slot) // 获取槽位类型

// 视图控制
void open()                                 // 打开视图
void close()                               // 关闭视图
int countSlots()                           // 获取总槽位数
Component title()                          // 获取标题
```

### 🎨 实用功能：GUI创建系统
```java
public class GUIManager {

    // 创建自定义GUI
    public static Inventory createCustomGUI(String title, int size) {
        return Bukkit.createInventory(null, size, title);
    }

    // 创建分页GUI
    public static void createPagedGUI(Player player, List<ItemStack> items,
                                     String title, int itemsPerPage) {
        int pages = (int) Math.ceil((double) items.size() / itemsPerPage);
        int currentPage = 0;

        Inventory gui = Bukkit.createInventory(null, 54, title + " (第" + (currentPage + 1) + "页)");

        // 填充物品
        for (int i = 0; i < Math.min(itemsPerPage, items.size()); i++) {
            gui.setItem(i, items.get(i));
        }

        // 添加导航按钮
        if (currentPage > 0) {
            gui.setItem(45, createButton(Material.ARROW, "§a上一页"));
        }
        if (currentPage < pages - 1) {
            gui.setItem(53, createButton(Material.ARROW, "§a下一页"));
        }

        player.openInventory(gui);
    }

    private static ItemStack createButton(Material material, String name) {
        ItemStack button = new ItemStack(material);
        ItemMeta meta = button.getItemMeta();
        meta.setDisplayName(name);
        button.setItemMeta(meta);
        return button;
    }
}
```

---

## 🍳 特殊容器类型

### 🔥 FurnaceInventory (熔炉)
```java
// 熔炉槽位
ItemStack getSmelting()                     // 获取冶炼物品
ItemStack getFuel()                         // 获取燃料
ItemStack getResult()                       // 获取结果
void setSmelting(ItemStack stack)           // 设置冶炼物品
void setFuel(ItemStack stack)              // 设置燃料
void setResult(ItemStack stack)            // 设置结果
```

### 🧪 BrewerInventory (酿造台)
```java
// 酿造台槽位
ItemStack getIngredient()                   // 获取材料
ItemStack getFuel()                         // 获取燃料(烈焰粉)
void setIngredient(ItemStack ingredient)    // 设置材料
void setFuel(ItemStack fuel)               // 设置燃料
```

### ⚒️ AnvilInventory (铁砧)
```java
// 铁砧槽位
ItemStack getFirstItem()                    // 获取第一个物品
ItemStack getSecondItem()                   // 获取第二个物品
ItemStack getResult()                       // 获取结果
void setFirstItem(ItemStack firstItem)      // 设置第一个物品
void setSecondItem(ItemStack secondItem)    // 设置第二个物品
void setResult(ItemStack result)           // 设置结果
String getRenameText()                      // 获取重命名文本
int getRepairCost()                        // 获取修复费用
void setRepairCost(int levels)             // 设置修复费用
int getMaximumRepairCost()                 // 获取最大修复费用
void setMaximumRepairCost(int levels)      // 设置最大修复费用
```

### 🎯 实用功能：自动化容器管理
```java
public class ContainerManager {

    // 自动熔炉管理
    public static void manageFurnace(FurnaceInventory furnace,
                                   List<ItemStack> smeltables,
                                   List<ItemStack> fuels) {
        // 添加可冶炼物品
        if (furnace.getSmelting() == null || furnace.getSmelting().getType().isAir()) {
            for (ItemStack item : smeltables) {
                if (item != null && !item.getType().isAir()) {
                    furnace.setSmelting(item);
                    break;
                }
            }
        }

        // 添加燃料
        if (furnace.getFuel() == null || furnace.getFuel().getType().isAir()) {
            for (ItemStack fuel : fuels) {
                if (fuel != null && fuel.getType().isFuel()) {
                    furnace.setFuel(fuel);
                    break;
                }
            }
        }
    }

    // 自动收集熔炉产物
    public static List<ItemStack> collectFurnaceResults(FurnaceInventory furnace) {
        List<ItemStack> results = new ArrayList<>();
        ItemStack result = furnace.getResult();

        if (result != null && !result.getType().isAir()) {
            results.add(result.clone());
            furnace.setResult(null);
        }

        return results;
    }
}
```

---

## 📜 配方系统

### 🔨 ShapedRecipe (有形状配方)
```java
// 创建有形状配方
ShapedRecipe recipe = new ShapedRecipe(key, result);
recipe.shape("ABC", "DEF", "GHI");          // 设置形状
recipe.setIngredient('A', Material.STONE);  // 设置材料映射

// 配方组
recipe.setGroup("custom_group");            // 设置配方组
recipe.setCategory(CraftingBookCategory.MISC); // 设置分类
```

### 🥄 ShapelessRecipe (无形状配方)
```java
// 创建无形状配方
ShapelessRecipe recipe = new ShapelessRecipe(key, result);
recipe.addIngredient(Material.STONE);       // 添加材料
recipe.addIngredient(2, Material.WOOD);     // 添加指定数量材料
```

### 🔥 CookingRecipe (烹饪配方)
```java
// 熔炉配方
FurnaceRecipe furnaceRecipe = new FurnaceRecipe(key, result, input, experience, cookingTime);

// 高炉配方
BlastingRecipe blastingRecipe = new BlastingRecipe(key, result, input, experience, cookingTime);

// 烟熏炉配方
SmokingRecipe smokingRecipe = new SmokingRecipe(key, result, input, experience, cookingTime);

// 营火配方
CampfireRecipe campfireRecipe = new CampfireRecipe(key, result, input, experience, cookingTime);
```

### 🎯 实用功能：动态配方管理
```java
public class RecipeManager {

    // 注册自定义配方
    public static void registerCustomRecipes(Plugin plugin) {
        // 创建自定义剑配方
        NamespacedKey key = new NamespacedKey(plugin, "custom_sword");
        ItemStack result = new ItemStack(Material.DIAMOND_SWORD);

        ShapedRecipe recipe = new ShapedRecipe(key, result);
        recipe.shape(" D ", " D ", " S ");
        recipe.setIngredient('D', Material.DIAMOND);
        recipe.setIngredient('S', Material.STICK);

        Bukkit.addRecipe(recipe);
    }

    // 移除配方
    public static void removeRecipe(NamespacedKey key) {
        Bukkit.removeRecipe(key);
    }

    // 获取所有配方
    public static List<Recipe> getAllRecipes() {
        List<Recipe> recipes = new ArrayList<>();
        Iterator<Recipe> iter = Bukkit.recipeIterator();

        while (iter.hasNext()) {
            recipes.add(iter.next());
        }

        return recipes;
    }
}
```

---

## 💡 实用功能示例

### 🎁 物品给予系统
```java
public class ItemGiveSystem {

    // 安全给予物品
    public static boolean giveItem(Player player, ItemStack item) {
        PlayerInventory inv = player.getInventory();

        // 检查背包空间
        if (hasSpace(inv, item)) {
            inv.addItem(item);
            return true;
        } else {
            // 背包满了，掉落到地面
            player.getWorld().dropItemNaturally(player.getLocation(), item);
            player.sendMessage("§c背包已满，物品已掉落到地面！");
            return false;
        }
    }

    // 检查背包空间
    private static boolean hasSpace(Inventory inv, ItemStack item) {
        HashMap<Integer, ItemStack> leftover = inv.addItem(item.clone());
        return leftover.isEmpty();
    }

    // 批量给予物品
    public static void giveItems(Player player, List<ItemStack> items) {
        for (ItemStack item : items) {
            giveItem(player, item);
        }
    }
}
```

### 🔍 物品搜索系统
```java
public class ItemSearchSystem {

    // 在背包中搜索物品
    public static List<ItemStack> searchItems(Inventory inv, String keyword) {
        List<ItemStack> results = new ArrayList<>();

        for (ItemStack item : inv.getContents()) {
            if (item != null && matchesKeyword(item, keyword)) {
                results.add(item);
            }
        }

        return results;
    }

    // 关键词匹配
    private static boolean matchesKeyword(ItemStack item, String keyword) {
        // 检查材料名称
        if (item.getType().name().toLowerCase().contains(keyword.toLowerCase())) {
            return true;
        }

        // 检查显示名称
        if (item.hasItemMeta() && item.getItemMeta().hasDisplayName()) {
            String displayName = item.getItemMeta().getDisplayName();
            if (displayName.toLowerCase().contains(keyword.toLowerCase())) {
                return true;
            }
        }

        // 检查描述
        if (item.hasItemMeta() && item.getItemMeta().hasLore()) {
            for (String lore : item.getItemMeta().getLore()) {
                if (lore.toLowerCase().contains(keyword.toLowerCase())) {
                    return true;
                }
            }
        }

        return false;
    }
}
```

### 📊 背包统计系统
```java
public class InventoryStats {

    // 获取背包统计信息
    public static Map<String, Object> getInventoryStats(Inventory inv) {
        Map<String, Object> stats = new HashMap<>();

        int totalItems = 0;
        int uniqueItems = 0;
        int emptySlots = 0;
        Map<Material, Integer> itemCounts = new HashMap<>();

        for (ItemStack item : inv.getContents()) {
            if (item == null || item.getType().isAir()) {
                emptySlots++;
            } else {
                totalItems += item.getAmount();
                itemCounts.merge(item.getType(), item.getAmount(), Integer::sum);
            }
        }

        uniqueItems = itemCounts.size();

        stats.put("totalItems", totalItems);
        stats.put("uniqueItems", uniqueItems);
        stats.put("emptySlots", emptySlots);
        stats.put("usedSlots", inv.getSize() - emptySlots);
        stats.put("itemCounts", itemCounts);

        return stats;
    }

    // 显示统计信息
    public static void showStats(Player player, Inventory inv) {
        Map<String, Object> stats = getInventoryStats(inv);

        player.sendMessage("§6=== 背包统计 ===");
        player.sendMessage("§a总物品数: " + stats.get("totalItems"));
        player.sendMessage("§a独特物品: " + stats.get("uniqueItems"));
        player.sendMessage("§a已用槽位: " + stats.get("usedSlots") + "/" + inv.getSize());
        player.sendMessage("§a空闲槽位: " + stats.get("emptySlots"));
    }
}
```

---

## 🎯 高级功能

### 🔄 物品序列化系统
```java
public class ItemSerialization {

    // 序列化物品到Base64
    public static String serializeItem(ItemStack item) {
        try {
            ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
            BukkitObjectOutputStream dataOutput = new BukkitObjectOutputStream(outputStream);
            dataOutput.writeObject(item);
            dataOutput.close();
            return Base64.getEncoder().encodeToString(outputStream.toByteArray());
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    // 从Base64反序列化物品
    public static ItemStack deserializeItem(String data) {
        try {
            ByteArrayInputStream inputStream = new ByteArrayInputStream(Base64.getDecoder().decode(data));
            BukkitObjectInputStream dataInput = new BukkitObjectInputStream(inputStream);
            ItemStack item = (ItemStack) dataInput.readObject();
            dataInput.close();
            return item;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

### 🎨 物品展示系统
```java
public class ItemDisplaySystem {

    // 创建物品展示GUI
    public static void showItemDisplay(Player player, List<ItemStack> items, String title) {
        int size = Math.min(54, ((items.size() + 8) / 9) * 9);
        Inventory gui = Bukkit.createInventory(null, size, title);

        for (int i = 0; i < Math.min(items.size(), size); i++) {
            gui.setItem(i, items.get(i));
        }

        player.openInventory(gui);
    }

    // 创建物品预览
    public static ItemStack createPreview(ItemStack original) {
        ItemStack preview = original.clone();
        ItemMeta meta = preview.getItemMeta();

        if (meta != null) {
            List<String> lore = meta.hasLore() ? meta.getLore() : new ArrayList<>();
            lore.add("");
            lore.add("§7▶ 点击查看详情");
            meta.setLore(lore);
            preview.setItemMeta(meta);
        }

        return preview;
    }
}
```

---

## 📝 总结

Paper API的Inventory系统提供了强大而灵活的物品和背包管理功能：

### 🌟 核心特性
- **完整的背包操作** - 增删改查、批量操作
- **丰富的物品元数据** - 显示名称、描述、附魔、自定义数据
- **多样的容器类型** - 玩家背包、特殊容器、自定义GUI
- **强大的配方系统** - 有形状/无形状配方、烹饪配方
- **灵活的视图管理** - 多层背包、槽位转换、事件处理

### 🎯 实用场景
- **🎁 物品管理系统** - 自动整理、智能分发、批量操作
- **🛍️ 商店系统** - 商品展示、购买界面、库存管理
- **🎒 背包扩展** - 虚拟背包、分类存储、快速访问
- **🔧 工具系统** - 自定义工具、特殊功能、耐久管理
- **🎮 游戏机制** - 任务奖励、成就系统、等级装备

### 💎 特殊功能
- **🔮 Tooltip系统** - 自定义物品提示信息
- **📦 DataComponent** - 现代化数据组件系统
- **🎨 物品展示** - 高级GUI界面设计
- **🔄 序列化** - 物品数据持久化存储
- **📊 统计分析** - 背包使用情况分析

通过合理运用这些API，可以创建出功能丰富、用户友好的物品管理系统，大大提升玩家的游戏体验！
