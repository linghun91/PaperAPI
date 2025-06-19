# Paper API 主手副手事件监听

## 概述
本文档总结监听玩家主手和副手物品变更以及按F键变换的各种事件监听方法，包括Bukkit原生API和Paper专属API。

## 1. 按F键交换主副手物品事件

### 1.1 PlayerSwapHandItemsEvent (Bukkit原生)
**包路径**: `org.bukkit.event.player.PlayerSwapHandItemsEvent`

**功能**: 监听玩家使用热键（F键）交换主手和副手物品

**特性**:
- 可取消事件 (Cancellable)
- 可获取和修改交换的物品
- 在物品交换前触发

**主要方法**:
```java
@EventHandler
public void onPlayerSwapHandItems(PlayerSwapHandItemsEvent event) {
    Player player = event.getPlayer();
    ItemStack mainHandItem = event.getMainHandItem();  // 将要切换到主手的物品
    ItemStack offHandItem = event.getOffHandItem();    // 将要切换到副手的物品
    
    // 可以修改交换的物品
    event.setMainHandItem(newMainHandItem);
    event.setOffHandItem(newOffHandItem);
    
    // 可以取消交换
    event.setCancelled(true);
}
```

## 2. 主手物品槽位变更事件

### 2.1 PlayerItemHeldEvent (Bukkit原生)
**包路径**: `org.bukkit.event.player.PlayerItemHeldEvent`

**功能**: 监听玩家切换手持物品槽位（1-9数字键或鼠标滚轮）

**特性**:
- 可取消事件 (Cancellable)
- 提供前一个槽位和新槽位信息
- 在槽位切换前触发

**主要方法**:
```java
@EventHandler
public void onPlayerItemHeld(PlayerItemHeldEvent event) {
    Player player = event.getPlayer();
    int previousSlot = event.getPreviousSlot();  // 之前的槽位 (0-8)
    int newSlot = event.getNewSlot();           // 新的槽位 (0-8)
    
    // 可以取消槽位切换
    event.setCancelled(true);
}
```

## 3. 库存槽位变更事件 (Paper专属)

### 3.1 PlayerInventorySlotChangeEvent (Paper专属)
**包路径**: `io.papermc.paper.event.player.PlayerInventorySlotChangeEvent`

**功能**: 监听玩家库存中任意槽位内容变更（包括主手、副手、装备槽等）

**特性**:
- 不可取消事件
- 提供详细的槽位信息和物品变更信息
- 在槽位内容变更后触发
- 可控制是否触发成就

**主要方法**:
```java
@EventHandler
public void onPlayerInventorySlotChange(PlayerInventorySlotChangeEvent event) {
    Player player = event.getPlayer();
    int rawSlot = event.getRawSlot();                    // 原始槽位号
    int slot = event.getSlot();                          // 转换后的槽位号
    ItemStack oldItem = event.getOldItemStack();         // 变更前的物品
    ItemStack newItem = event.getNewItemStack();         // 变更后的物品
    
    // 控制是否触发成就
    event.setShouldTriggerAdvancements(false);
    
    // 判断是否为主手或副手槽位
    if (slot == player.getInventory().getHeldItemSlot()) {
        // 主手槽位变更
    } else if (rawSlot == 40) {
        // 副手槽位变更 (副手槽位的rawSlot通常是40)
    }
}
```

## 4. 主手偏好设置变更事件

### 4.1 PlayerChangedMainHandEvent (Bukkit原生)
**包路径**: `org.bukkit.event.player.PlayerChangedMainHandEvent`

**功能**: 监听玩家在客户端设置中更改主手偏好（左手/右手）

**特性**:
- 不可取消事件
- 在设置变更后触发

**主要方法**:
```java
@EventHandler
public void onPlayerChangedMainHand(PlayerChangedMainHandEvent event) {
    Player player = event.getPlayer();
    MainHand newMainHand = event.getMainHand();  // 新的主手偏好 (LEFT/RIGHT)
    
    // 获取旧的主手偏好（在事件处理期间仍可用）
    MainHand oldMainHand = player.getMainHand();
}
```

## 5. 手臂挥动事件 (Paper专属)

### 5.1 PlayerArmSwingEvent (Paper专属)
**包路径**: `io.papermc.paper.event.player.PlayerArmSwingEvent`

**功能**: 监听玩家手臂挥动动作，可区分主手和副手

**特性**:
- 可取消事件 (Cancellable)
- 继承自PlayerAnimationEvent
- 提供具体的手部信息

**主要方法**:
```java
@EventHandler
public void onPlayerArmSwing(PlayerArmSwingEvent event) {
    Player player = event.getPlayer();
    EquipmentSlot hand = event.getHand();  // HAND (主手) 或 OFF_HAND (副手)
    
    if (hand == EquipmentSlot.HAND) {
        // 主手挥动
    } else if (hand == EquipmentSlot.OFF_HAND) {
        // 副手挥动
    }
    
    // 可以取消挥动动作
    event.setCancelled(true);
}
```

## 6. 实用工具方法

### 6.1 获取玩家当前手持物品
```java
// 获取主手物品
ItemStack mainHandItem = player.getInventory().getItemInMainHand();

// 获取副手物品
ItemStack offHandItem = player.getInventory().getItemInOffHand();

// 获取主手偏好设置
MainHand mainHandPreference = player.getMainHand();
```

### 6.2 设置玩家手持物品
```java
// 设置主手物品
player.getInventory().setItemInMainHand(itemStack);

// 设置副手物品
player.getInventory().setItemInOffHand(itemStack);
```

## 7. 最佳实践建议

### 7.1 事件选择建议
- **监听F键交换**: 使用 `PlayerSwapHandItemsEvent`
- **监听槽位切换**: 使用 `PlayerItemHeldEvent`
- **监听所有库存变更**: 使用 `PlayerInventorySlotChangeEvent` (Paper专属)
- **监听手部动作**: 使用 `PlayerArmSwingEvent` (Paper专属)
- **监听主手偏好变更**: 使用 `PlayerChangedMainHandEvent`

### 7.2 Paper专属API优势
- `PlayerInventorySlotChangeEvent`: 提供更详细的库存变更信息
- `PlayerArmSwingEvent`: 可区分主手和副手的挥动动作
- 更好的性能和更丰富的功能

### 7.3 兼容性考虑
- Bukkit原生事件在所有服务端实现中都可用
- Paper专属事件仅在Paper及其分支（如Purpur）中可用
- 建议优先使用Paper专属API以获得更好的功能和性能

## 8. 示例：完整的主副手监听器

```java
public class HandItemListener implements Listener {
    
    @EventHandler
    public void onSwapHandItems(PlayerSwapHandItemsEvent event) {
        // F键交换主副手物品
        Player player = event.getPlayer();
        player.sendMessage("你交换了主副手物品！");
    }
    
    @EventHandler
    public void onItemHeld(PlayerItemHeldEvent event) {
        // 切换主手槽位
        Player player = event.getPlayer();
        ItemStack newItem = player.getInventory().getItem(event.getNewSlot());
        if (newItem != null && newItem.getType() != Material.AIR) {
            player.sendMessage("你切换到了: " + newItem.getType().name());
        }
    }
    
    @EventHandler
    public void onInventorySlotChange(PlayerInventorySlotChangeEvent event) {
        // Paper专属：监听所有库存槽位变更
        Player player = event.getPlayer();
        int slot = event.getSlot();
        
        if (slot == player.getInventory().getHeldItemSlot()) {
            // 主手槽位内容变更
            player.sendMessage("主手物品发生变更！");
        } else if (event.getRawSlot() == 40) {
            // 副手槽位内容变更
            player.sendMessage("副手物品发生变更！");
        }
    }
}
```

## 总结

Paper API提供了丰富的事件监听机制来监控玩家的主手和副手物品变更：

1. **Bukkit原生API**提供基础的事件监听功能，兼容性好
2. **Paper专属API**提供更详细和高效的事件监听，功能更强大
3. 根据具体需求选择合适的事件类型
4. 建议在Paper服务端环境下优先使用Paper专属API
