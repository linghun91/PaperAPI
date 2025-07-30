# CursorMenuPlugin - 3D光标菜单系统

一个创新的 Minecraft 插件，为玩家提供沉浸式的3D界面体验，通过光标系统实现全息交互。

## 核心功能实现分析

### 1. 3D光标菜单系统

#### 实现原理
插件通过创建多个实体组合来构建3D界面：

- **ArmorStand（盔甲架）**: 作为光标的载体，不可见但可移动
- **ItemDisplay（物品展示）**: 显示光标的视觉效果（如箭头）
- **TextDisplay（文本展示）**: 显示菜单选项文本

#### 关键API使用
```java
// 创建光标盔甲架
ArmorStand cursor = (ArmorStand)location.getWorld().spawn(location, ArmorStand.class);
cursor.setGravity(false);
cursor.setVisible(false);
cursor.setMarker(true);

// 创建物品展示作为光标
ItemDisplay itemDisplay = (ItemDisplay)location.getWorld().spawn(location, ItemDisplay.class);
itemDisplay.setItemStack(cursorItem);
itemDisplay.setBillboard(Display.Billboard.CENTER);

// 创建文本展示
TextDisplay textDisplay = (TextDisplay)world.spawn(textLocation, TextDisplay.class);
textDisplay.setText(menuText);
textDisplay.setBillboard(Display.Billboard.CENTER);
```

#### 光标跟随算法
```java
// 根据玩家视角计算光标位置
Vector dir = base.getDirection().normalize();
Vector right = new Vector(-dir.getZ(), 0.0, dir.getX()).normalize();
Vector up = dir.getCrossProduct(right).multiply(-1);

// 将视角角度转换为屏幕坐标
double screenX = (double)yaw / (90.0 / maxX);
double screenY = (double)pitch / (90.0 / maxY);

// 计算最终光标位置
Vector offset = right.multiply(screenX).add(up.multiply(-screenY));
Location hudPos = base.clone().add(dir.multiply(distance)).add(offset);
```

### 2. 相机控制系统

#### 实现方式
使用 ProtocolLib 发送相机控制数据包，实现第三人称视角：

```java
// 发送相机控制包
PacketContainer packet = protocolManager.createPacket(PacketType.Play.Server.CAMERA);
packet.getIntegers().write(0, entity.getEntityId());
protocolManager.sendServerPacket(player, packet);
```

#### 玩家固定机制
- 创建一个不可见的猪实体作为座椅
- 将玩家骑乘在猪上，限制移动
- 设置玩家不可见和无碰撞

```java
Pig pig = (Pig)player.getWorld().spawn(location, Pig.class);
pig.setAI(false);
pig.setInvisible(true);
pig.addPassenger(player);
player.setInvisible(true);
player.setCollidable(false);
```

### 3. 文本显示管理

#### TextDisplay 实体特性
- 支持彩色文本和格式化
- 可设置透明背景
- 支持 Billboard 模式（始终面向玩家）
- 支持动画效果

#### 动画系统实现
```java
// 旋转动画
Transformation t = display.getTransformation();
t.getLeftRotation().rotationYXZ(0.0f, angle, 0.0f);
display.setTransformation(t);

// 上下浮动动画
double y = start.getY() + 2.0 * progress;
t.getTranslation().set(0, y - display.getLocation().getY(), 0);
display.setTransformation(t);
```

### 4. 物品显示系统

#### ItemDisplay 功能
- 显示自定义模型数据的物品
- 支持发光效果
- 可设置缩放和旋转
- 跟随玩家视角移动

```java
ItemDisplay display = (ItemDisplay)world.spawn(location, ItemDisplay.class);
display.setItemStack(itemStack);
display.setGlowing(true);
display.setViewRange(100.0f);

// 设置缩放
Transformation tr = display.getTransformation();
tr.getScale().set(scale, scale, scale);
display.setTransformation(tr);
```

### 5. 3D全息交互处理

#### 交互检测原理
1. **拦截USE_ENTITY数据包**: 使用 ProtocolLib 拦截玩家的交互数据包
2. **范围检测**: 获取光标附近的实体
3. **类型判断**: 检查是否为 TextDisplay 实体
4. **命令执行**: 根据自定义名称执行对应功能

```java
// 注册数据包监听器
protocolManager.addPacketListener(new PacketAdapter(plugin, PacketType.Play.Client.USE_ENTITY) {
    public void onPacketReceiving(PacketEvent event) {
        Player player = event.getPlayer();
        ItemDisplay cursor = playerItemDisplays.get(player);
        
        // 获取光标附近的实体
        List<Entity> nearbyEntities = cursor.getNearbyEntities(1.0, 0.3, 1.0);
        
        for (Entity entity : nearbyEntities) {
            if (entity instanceof TextDisplay) {
                TextDisplay textDisplay = (TextDisplay)entity;
                String key = textDisplay.getCustomName();
                // 执行对应菜单项的命令
                MenuLayout layout = sectionManager.getLayout(key);
                layout.runCommand(player);
            }
        }
    }
});
```

#### 碰撞检测优化
- 使用 `getNearbyEntities()` 方法进行高效的范围查询
- 通过自定义名称快速识别可交互元素
- 一次点击只处理第一个检测到的元素

## 技术特点

### 性能优化
1. **实体管理**: 严格的生命周期管理，避免内存泄漏
2. **异步处理**: 使用 MorePaperLib 进行区域特定的任务调度
3. **视距控制**: 仅向特定玩家显示实体，减少网络开销

### 兼容性设计
1. **版本兼容**: 使用反射处理不同版本的API差异
2. **插件集成**: 支持 PlaceholderAPI 变量替换
3. **多语言支持**: 内置中文、英文、俄文语言文件

### 安全特性
1. **权限控制**: 每个菜单和按钮都可设置权限
2. **状态恢复**: 退出菜单时恢复玩家原始状态
3. **错误处理**: 完善的异常捕获和日志记录

## 配置结构

### 菜单配置 (config.yml)
```yaml
menu:
  example:
    camera-position:
      world: world
      x: 0.0
      y: 100.0
      z: 0.0
      distance: 3.0
      yaw: 0.0
      pitch: 0.0
    layout:
      button1:
        name: "&a选项1"
        x: -1.0
        y: 0.0
        z: 0.0
        command:
          - "say 点击了选项1"
```

### 光标配置
```yaml
cursor-item:
  material: ARROW
  custom-model-data: 1001
  scale: 1.0
  z-offset: 0.0
  max-x: 2.0
  max-y: 1.5
```

## 总结

该插件通过巧妙组合 Minecraft 的多种实体类型和 ProtocolLib 的数据包控制，实现了一个完整的3D交互界面系统。主要创新点包括：

1. **沉浸式体验**: 通过相机控制创造第三人称界面视角
2. **直观交互**: 光标跟随视线移动，点击即可触发
3. **灵活配置**: 支持自定义布局、动画和样式
4. **高性能**: 优化的实体管理和事件处理

这种实现方式为 Minecraft 插件开发提供了新的界面交互思路，突破了传统GUI的限制。