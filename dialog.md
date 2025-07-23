# Minecraft Paper Dialog API 完整指南

## 概述

Minecraft Paper 在版本 1.21.7+ 中引入了强大的 Dialog API，允许插件开发者创建自定义对话框界面。这个 API 提供了丰富的功能，包括文本输入、按钮操作、物品展示等，为服务器提供了更好的用户交互体验。

## 核心概念

### Dialog 系统架构

Dialog API 基于以下核心组件构建：

1. **DialogRegistryEntry** - 对话框注册条目，包含对话框的基础信息和类型
2. **DialogBase** - 对话框基础配置，定义标题、内容和行为
3. **DialogType** - 对话框类型，决定对话框的布局和功能
4. **DialogBody** - 对话框主体内容，可以是文本或物品
5. **DialogInput** - 对话框输入组件，支持多种输入类型
6. **ActionButton** - 操作按钮，用于触发各种动作

## API 详细参考

### DialogRegistryEntry

`DialogRegistryEntry` 是对话框的注册条目，包含对话框的基本信息。

#### 主要方法

```java
// 获取对话框基础配置
DialogBase base()

// 获取对话框类型
DialogType type()
```

#### Builder 模式

```java
DialogRegistryEntry.Builder builder = DialogRegistryEntry.builder();
```

### DialogBase

`DialogBase` 定义了对话框的基础属性和行为。

#### 核心属性

- **title()** - 对话框标题 (Component)
- **externalTitle()** - 外部标题，用于按钮显示 (Component, 可选)
- **canCloseWithEscape()** - 是否可以用 ESC 键关闭 (boolean)
- **pause()** - 是否在单人游戏中暂停游戏 (boolean)
- **afterAction()** - 对话框关闭后的动作 (DialogAfterAction)
- **body()** - 对话框主体内容列表 (List<DialogBody>)
- **inputs()** - 对话框输入组件列表 (List<DialogInput>)

#### 创建方法

```java
// 使用 Builder 模式
DialogBase.Builder builder = DialogBase.builder(Component.text("对话框标题"));

// 直接创建
DialogBase dialog = DialogBase.create(
    Component.text("标题"),           // 标题
    Component.text("外部标题"),       // 外部标题 (可选)
    true,                           // 可以用 ESC 关闭
    false,                          // 不暂停游戏
    DialogBase.DialogAfterAction.CLOSE, // 关闭后动作
    List.of(body),                  // 主体内容
    List.of(input)                  // 输入组件
);
```

#### DialogAfterAction 枚举

- **CLOSE** - 关闭对话框
- **KEEP_OPEN** - 保持对话框打开

### DialogType

`DialogType` 定义了对话框的类型和布局。

#### 可用类型

##### 1. NoticeType - 通知对话框

```java
// 默认通知对话框
NoticeType notice = DialogType.notice();

// 自定义按钮的通知对话框
ActionButton okButton = ActionButton.builder(Component.text("确定")).build();
NoticeType customNotice = DialogType.notice(okButton);
```

##### 2. ConfirmationType - 确认对话框

```java
ActionButton yesButton = ActionButton.builder(Component.text("是")).build();
ActionButton noButton = ActionButton.builder(Component.text("否")).build();
ConfirmationType confirmation = DialogType.confirmation(yesButton, noButton);
```

##### 3. MultiActionType - 多操作对话框

```java
List<ActionButton> actions = List.of(
    ActionButton.builder(Component.text("选项1")).build(),
    ActionButton.builder(Component.text("选项2")).build(),
    ActionButton.builder(Component.text("选项3")).build()
);

// 使用 Builder
MultiActionType.Builder builder = DialogType.multiAction(actions);

// 直接创建
ActionButton exitButton = ActionButton.builder(Component.text("退出")).build();
MultiActionType multiAction = DialogType.multiAction(actions, exitButton, 2); // 2列布局
```

##### 4. DialogListType - 对话框列表

```java
RegistrySet<Dialog> dialogs = ...; // 对话框集合

// 使用 Builder
DialogListType.Builder builder = DialogType.dialogList(dialogs);

// 直接创建
ActionButton exitButton = ActionButton.builder(Component.text("返回")).build();
DialogListType dialogList = DialogType.dialogList(
    dialogs,     // 对话框集合
    exitButton,  // 退出按钮
    3,          // 3列布局
    200         // 按钮宽度
);
```

##### 5. ServerLinksType - 服务器链接对话框

```java
ActionButton exitButton = ActionButton.builder(Component.text("关闭")).build();
ServerLinksType serverLinks = DialogType.serverLinks(
    exitButton,  // 退出按钮
    2,          // 2列布局
    150         // 按钮宽度
);
```

### DialogBody

`DialogBody` 定义对话框的主体内容。

#### PlainMessageDialogBody - 纯文本内容

```java
// 基础文本内容
PlainMessageDialogBody textBody = DialogBody.plainMessage(
    Component.text("这是对话框的文本内容")
);

// 指定宽度的文本内容
PlainMessageDialogBody textBodyWithWidth = DialogBody.plainMessage(
    Component.text("这是对话框的文本内容"),
    300  // 宽度
);
```

#### ItemDialogBody - 物品展示内容

```java
ItemStack item = new ItemStack(Material.DIAMOND_SWORD);

// 使用 Builder
ItemDialogBody.Builder builder = DialogBody.item(item);

// 直接创建
PlainMessageDialogBody description = DialogBody.plainMessage(Component.text("物品描述"));
ItemDialogBody itemBody = DialogBody.item(
    item,         // 要展示的物品
    description,  // 描述文本 (可选)
    true,        // 显示装饰
    true,        // 显示工具提示
    64,          // 宽度
    64           // 高度
);
```

### DialogInput

`DialogInput` 提供各种用户输入组件。

#### TextDialogInput - 文本输入

```java
// 使用 Builder
TextDialogInput.Builder builder = DialogInput.text("player_name", Component.text("玩家名称"));

// 直接创建
TextDialogInput textInput = DialogInput.text(
    "player_name",              // 输入键名
    300,                       // 宽度
    Component.text("玩家名称"), // 标签
    true,                      // 显示标签
    "默认值",                   // 初始值
    50,                        // 最大长度
    null                       // 多行选项 (可选)
);
```

#### BooleanDialogInput - 布尔值输入

```java
// 使用 Builder
BooleanDialogInput.Builder builder = DialogInput.bool("enable_pvp", Component.text("启用PVP"));

// 直接创建
BooleanDialogInput boolInput = DialogInput.bool(
    "enable_pvp",              // 输入键名
    Component.text("启用PVP"),  // 标签
    false,                     // 初始值
    "enabled",                 // true时的值
    "disabled"                 // false时的值
);
```

#### NumberRangeDialogInput - 数值范围输入

```java
// 使用 Builder
NumberRangeDialogInput.Builder builder = DialogInput.numberRange(
    "player_count", 
    Component.text("玩家数量"), 
    1.0f, 
    100.0f
);

// 直接创建
NumberRangeDialogInput numberInput = DialogInput.numberRange(
    "player_count",            // 输入键名
    200,                       // 宽度
    Component.text("玩家数量"), // 标签
    "玩家数量: %s",             // 标签格式
    1.0f,                      // 最小值
    100.0f,                    // 最大值
    10.0f,                     // 初始值 (可选)
    1.0f                       // 步长 (可选)
);
```

#### SingleOptionDialogInput - 单选输入

```java
List<SingleOptionDialogInput.OptionEntry> options = List.of(
    SingleOptionDialogInput.OptionEntry.create("easy", Component.text("简单")),
    SingleOptionDialogInput.OptionEntry.create("normal", Component.text("普通")),
    SingleOptionDialogInput.OptionEntry.create("hard", Component.text("困难"))
);

// 使用 Builder
SingleOptionDialogInput.Builder builder = DialogInput.singleOption(
    "difficulty", 
    Component.text("难度"), 
    options
);

// 直接创建
SingleOptionDialogInput singleOption = DialogInput.singleOption(
    "difficulty",              // 输入键名
    250,                       // 宽度
    options,                   // 选项列表
    Component.text("难度"),     // 标签
    true                       // 显示标签
);
```

### ActionButton

`ActionButton` 定义对话框中的操作按钮。

#### 基本创建

```java
// 使用 Builder
ActionButton.Builder builder = ActionButton.builder(Component.text("确定"));

// 直接创建
ActionButton button = ActionButton.create(
    Component.text("确定"),        // 按钮标签
    Component.text("点击确定"),    // 工具提示 (可选)
    100,                         // 宽度
    action                       // 关联动作 (可选)
);
```

#### 按钮属性

- **label()** - 按钮标签 (Component)
- **tooltip()** - 工具提示 (Component, 可选)
- **width()** - 按钮宽度 (int, 范围: 1-1024)
- **action()** - 关联的动作 (DialogAction, 可选)

### DialogAction

`DialogAction` 定义按钮点击时执行的动作。

#### StaticAction - 静态动作

```java
ClickEvent clickEvent = ClickEvent.openUrl("https://example.com");
DialogAction.StaticAction staticAction = DialogAction.staticAction(clickEvent);
```

#### CommandTemplateAction - 命令模板动作

```java
// 使用输入变量的命令模板
DialogAction.CommandTemplateAction commandAction = DialogAction.commandTemplate(
    "gamemode $(game_mode) $(player_name)"
);
```

#### CustomClickAction - 自定义点击动作

```java
// 使用回调函数
DialogActionCallback callback = (player, inputs) -> {
    // 自定义处理逻辑
    player.sendMessage("按钮被点击了！");
};
ClickCallback.Options options = ClickCallback.Options.builder().build();
DialogAction.CustomClickAction customAction = DialogAction.customClick(callback, options);

// 使用标识符和数据
Key actionId = Key.key("myplugin", "custom_action");
BinaryTagHolder data = BinaryTagHolder.of("{\"type\":\"custom\"}");
DialogAction.CustomClickAction customAction2 = DialogAction.customClick(actionId, data);
```

## 实际应用示例

### 示例1：服务器设置对话框

```java
public class ServerSettingsDialog {
    
    public static DialogRegistryEntry createSettingsDialog() {
        // 创建输入组件
        TextDialogInput serverName = DialogInput.text(
            "server_name",
            Component.text("服务器名称")
        ).initial("我的服务器").build();
        
        BooleanDialogInput enablePvp = DialogInput.bool(
            "enable_pvp",
            Component.text("启用PVP")
        ).initial(true).build();
        
        NumberRangeDialogInput maxPlayers = DialogInput.numberRange(
            "max_players",
            Component.text("最大玩家数"),
            1.0f,
            100.0f
        ).initial(20.0f).build();
        
        // 创建主体内容
        PlainMessageDialogBody description = DialogBody.plainMessage(
            Component.text("配置您的服务器设置")
        );
        
        // 创建操作按钮
        ActionButton saveButton = ActionButton.builder(Component.text("保存"))
            .action(DialogAction.commandTemplate("server config save $(server_name) $(enable_pvp) $(max_players)"))
            .build();
            
        ActionButton cancelButton = ActionButton.builder(Component.text("取消"))
            .action(DialogAction.staticAction(ClickEvent.runCommand("/dialog close")))
            .build();
        
        // 创建对话框基础
        DialogBase base = DialogBase.builder(Component.text("服务器设置"))
            .externalTitle(Component.text("设置"))
            .canCloseWithEscape(true)
            .pause(false)
            .afterAction(DialogBase.DialogAfterAction.CLOSE)
            .body(List.of(description))
            .inputs(List.of(serverName, enablePvp, maxPlayers))
            .build();
        
        // 创建对话框类型
        MultiActionType dialogType = DialogType.multiAction(
            List.of(saveButton, cancelButton),
            null,
            2
        );
        
        // 创建注册条目
        return DialogRegistryEntry.builder()
            .base(base)
            .type(dialogType)
            .build();
    }
}
```

### 示例2：物品商店对话框

```java
public class ItemShopDialog {
    
    public static DialogRegistryEntry createShopDialog() {
        // 创建物品展示
        ItemStack diamond = new ItemStack(Material.DIAMOND, 1);
        ItemDialogBody diamondDisplay = DialogBody.item(diamond)
            .description(DialogBody.plainMessage(Component.text("稀有的钻石，价格：100金币")))
            .showDecorations(true)
            .showTooltip(true)
            .width(64)
            .height(64)
            .build();
        
        // 创建数量选择
        NumberRangeDialogInput quantity = DialogInput.numberRange(
            "quantity",
            Component.text("购买数量"),
            1.0f,
            64.0f
        ).initial(1.0f).step(1.0f).build();
        
        // 创建购买按钮
        ActionButton buyButton = ActionButton.builder(Component.text("购买"))
            .tooltip(Component.text("点击购买选定数量的物品"))
            .action(DialogAction.commandTemplate("shop buy diamond $(quantity)"))
            .build();
        
        ActionButton closeButton = ActionButton.builder(Component.text("关闭"))
            .build();
        
        // 创建对话框
        DialogBase base = DialogBase.builder(Component.text("物品商店"))
            .body(List.of(diamondDisplay))
            .inputs(List.of(quantity))
            .build();
        
        ConfirmationType dialogType = DialogType.confirmation(buyButton, closeButton);
        
        return DialogRegistryEntry.builder()
            .base(base)
            .type(dialogType)
            .build();
    }
}
```

## 最佳实践

### 1. 输入验证

始终验证用户输入，特别是在命令模板中使用变量时：

```java
// 在命令模板中使用输入验证
DialogAction.CommandTemplateAction safeCommand = DialogAction.commandTemplate(
    "teleport $(player_name) $(x) $(y) $(z)"
);
```

### 2. 用户体验优化

- 提供清晰的标签和工具提示
- 合理设置输入范围和默认值
- 使用适当的对话框类型

### 3. 错误处理

```java
DialogActionCallback errorHandlingCallback = (player, inputs) -> {
    try {
        // 处理用户输入
        String playerName = inputs.get("player_name");
        if (playerName == null || playerName.trim().isEmpty()) {
            player.sendMessage(Component.text("请输入有效的玩家名称").color(NamedTextColor.RED));
            return;
        }
        // 执行操作
    } catch (Exception e) {
        player.sendMessage(Component.text("操作失败：" + e.getMessage()).color(NamedTextColor.RED));
    }
};
```

### 4. 国际化支持

使用翻译键而不是硬编码文本：

```java
Component title = Component.translatable("dialog.settings.title");
Component label = Component.translatable("dialog.settings.server_name");
```

## 注意事项

1. **实验性API**：Dialog API 目前标记为 `@Experimental`，在未来版本中可能会有变化
2. **版本兼容性**：确保使用 Paper 1.21.7+ 版本
3. **性能考虑**：避免创建过于复杂的对话框，影响客户端性能
4. **权限管理**：合理控制对话框的访问权限

## 高级功能

### 多行文本输入

`TextDialogInput` 支持多行文本输入功能：

```java
TextDialogInput.MultilineOptions multilineOptions = TextDialogInput.MultilineOptions.create(
    5,     // 最大行数
    true   // 自动换行
);

TextDialogInput multilineInput = DialogInput.text(
    "description",
    400,
    Component.text("描述"),
    true,
    "请输入描述...",
    500,
    multilineOptions
);
```

### 动态对话框内容

使用自定义回调创建动态内容：

```java
DialogActionCallback dynamicCallback = (player, inputs) -> {
    String selectedOption = inputs.get("option");
    switch (selectedOption) {
        case "teleport":
            // 显示传送对话框
            showTeleportDialog(player);
            break;
        case "inventory":
            // 显示背包管理对话框
            showInventoryDialog(player);
            break;
        default:
            player.sendMessage(Component.text("未知选项"));
    }
};
```

### 条件显示

根据玩家权限或状态动态调整对话框内容：

```java
public static DialogRegistryEntry createConditionalDialog(Player player) {
    List<ActionButton> actions = new ArrayList<>();

    // 基础按钮
    actions.add(ActionButton.builder(Component.text("查看信息")).build());

    // 管理员专用按钮
    if (player.hasPermission("admin.manage")) {
        actions.add(ActionButton.builder(Component.text("管理设置"))
            .action(DialogAction.commandTemplate("admin settings"))
            .build());
    }

    // VIP专用按钮
    if (player.hasPermission("vip.features")) {
        actions.add(ActionButton.builder(Component.text("VIP功能"))
            .action(DialogAction.commandTemplate("vip menu"))
            .build());
    }

    return createDialogWithActions(actions);
}
```

## 事件处理

### 对话框事件监听

```java
@EventHandler
public void onDialogAction(DialogActionEvent event) {
    Player player = event.getPlayer();
    String actionId = event.getActionId();
    Map<String, String> inputs = event.getInputs();

    switch (actionId) {
        case "player_settings":
            handlePlayerSettings(player, inputs);
            break;
        case "server_config":
            handleServerConfig(player, inputs);
            break;
    }
}

private void handlePlayerSettings(Player player, Map<String, String> inputs) {
    String nickname = inputs.get("nickname");
    boolean enableNotifications = Boolean.parseBoolean(inputs.get("notifications"));

    // 应用设置
    player.setDisplayName(Component.text(nickname));
    // 保存通知设置...

    player.sendMessage(Component.text("设置已保存！").color(NamedTextColor.GREEN));
}
```

### 输入验证事件

```java
@EventHandler
public void onDialogInputValidation(DialogInputValidationEvent event) {
    String inputKey = event.getInputKey();
    String value = event.getValue();

    switch (inputKey) {
        case "player_name":
            if (!isValidPlayerName(value)) {
                event.setCancelled(true);
                event.setErrorMessage("无效的玩家名称");
            }
            break;
        case "coordinates":
            if (!isValidCoordinates(value)) {
                event.setCancelled(true);
                event.setErrorMessage("坐标格式错误");
            }
            break;
    }
}
```

## 样式和主题

### 自定义样式

```java
// 创建带有自定义样式的对话框
DialogBase styledDialog = DialogBase.builder(Component.text("样式对话框"))
    .externalTitle(Component.text("🎨 样式设置"))
    .body(List.of(
        DialogBody.plainMessage(
            Component.text("选择您喜欢的主题颜色")
                .color(NamedTextColor.GOLD)
                .decorate(TextDecoration.BOLD)
        )
    ))
    .build();
```

### 图标和装饰

```java
// 使用物品作为图标
ItemStack icon = new ItemStack(Material.NETHER_STAR);
ItemMeta meta = icon.getItemMeta();
meta.displayName(Component.text("特殊功能").color(NamedTextColor.LIGHT_PURPLE));
icon.setItemMeta(meta);

ItemDialogBody iconBody = DialogBody.item(icon)
    .showDecorations(true)
    .width(32)
    .height(32)
    .build();
```

## 数据持久化

### 保存对话框状态

```java
public class DialogStateManager {
    private final Map<UUID, DialogState> playerStates = new HashMap<>();

    public void saveDialogState(Player player, Map<String, String> inputs) {
        DialogState state = new DialogState();
        state.setInputs(inputs);
        state.setTimestamp(System.currentTimeMillis());

        playerStates.put(player.getUniqueId(), state);

        // 保存到数据库或文件
        saveToDatabase(player.getUniqueId(), state);
    }

    public DialogState loadDialogState(Player player) {
        return playerStates.get(player.getUniqueId());
    }
}

public class DialogState {
    private Map<String, String> inputs;
    private long timestamp;

    // getters and setters...
}
```

### 配置文件集成

```java
public class DialogConfigManager {
    private FileConfiguration config;

    public DialogRegistryEntry loadDialogFromConfig(String dialogId) {
        ConfigurationSection section = config.getConfigurationSection("dialogs." + dialogId);
        if (section == null) return null;

        String title = section.getString("title", "对话框");
        boolean canClose = section.getBoolean("can-close", true);
        boolean pause = section.getBoolean("pause", false);

        DialogBase.Builder builder = DialogBase.builder(Component.text(title))
            .canCloseWithEscape(canClose)
            .pause(pause);

        // 加载输入组件
        ConfigurationSection inputsSection = section.getConfigurationSection("inputs");
        if (inputsSection != null) {
            List<DialogInput> inputs = loadInputsFromConfig(inputsSection);
            builder.inputs(inputs);
        }

        return DialogRegistryEntry.builder()
            .base(builder.build())
            .type(loadDialogTypeFromConfig(section))
            .build();
    }
}
```

## 性能优化

### 对话框缓存

```java
public class DialogCache {
    private final Map<String, DialogRegistryEntry> cache = new ConcurrentHashMap<>();
    private final long CACHE_EXPIRY = 300000; // 5分钟

    public DialogRegistryEntry getDialog(String id, Supplier<DialogRegistryEntry> supplier) {
        return cache.computeIfAbsent(id, k -> {
            DialogRegistryEntry dialog = supplier.get();
            // 设置过期时间
            scheduleExpiry(id);
            return dialog;
        });
    }

    private void scheduleExpiry(String id) {
        Bukkit.getScheduler().runTaskLater(plugin, () -> cache.remove(id), CACHE_EXPIRY / 50);
    }
}
```

### 异步处理

```java
public class AsyncDialogHandler {

    public void handleDialogActionAsync(Player player, String actionId, Map<String, String> inputs) {
        Bukkit.getScheduler().runTaskAsynchronously(plugin, () -> {
            try {
                // 执行耗时操作
                String result = performDatabaseOperation(inputs);

                // 回到主线程更新UI
                Bukkit.getScheduler().runTask(plugin, () -> {
                    player.sendMessage(Component.text("操作完成：" + result));
                });
            } catch (Exception e) {
                Bukkit.getScheduler().runTask(plugin, () -> {
                    player.sendMessage(Component.text("操作失败：" + e.getMessage())
                        .color(NamedTextColor.RED));
                });
            }
        });
    }
}
```

## 调试和测试

### 调试工具

```java
public class DialogDebugger {

    public static void debugDialog(DialogRegistryEntry dialog) {
        System.out.println("=== Dialog Debug Info ===");
        System.out.println("Title: " + PlainTextComponentSerializer.plainText()
            .serialize(dialog.base().title()));
        System.out.println("Can close with escape: " + dialog.base().canCloseWithEscape());
        System.out.println("Pause game: " + dialog.base().pause());
        System.out.println("Body count: " + dialog.base().body().size());
        System.out.println("Input count: " + dialog.base().inputs().size());

        for (DialogInput input : dialog.base().inputs()) {
            System.out.println("Input key: " + input.key());
        }
    }
}
```

### 单元测试

```java
public class DialogTest {

    @Test
    public void testDialogCreation() {
        DialogBase dialog = DialogBase.builder(Component.text("测试对话框"))
            .canCloseWithEscape(true)
            .build();

        assertNotNull(dialog);
        assertEquals("测试对话框", PlainTextComponentSerializer.plainText()
            .serialize(dialog.title()));
        assertTrue(dialog.canCloseWithEscape());
    }

    @Test
    public void testInputValidation() {
        TextDialogInput input = DialogInput.text("test", Component.text("测试"))
            .maxLength(10)
            .build();

        assertEquals("test", input.key());
        assertEquals(10, input.maxLength());
    }
}
```

## 总结

Minecraft Paper 的 Dialog API 为插件开发者提供了强大的用户界面创建能力。通过合理使用各种组件和类型，可以创建出功能丰富、用户友好的对话框界面，大大提升服务器的交互体验。

### 关键要点

1. **模块化设计**：Dialog API 采用模块化设计，各组件职责明确
2. **Builder 模式**：大量使用 Builder 模式，提供灵活的配置选项
3. **类型安全**：使用密封接口确保类型安全
4. **扩展性**：支持自定义动作和回调，满足复杂需求

### 开发建议

- 始终关注用户体验，提供清晰的界面和合理的默认值
- 做好错误处理和输入验证，确保对话框的稳定性和安全性
- 合理使用缓存和异步处理，优化性能
- 充分利用事件系统，实现复杂的交互逻辑
- 考虑国际化支持，使用翻译键而不是硬编码文本

记住 Dialog API 目前仍标记为实验性功能，在生产环境中使用时要做好向后兼容性的准备。
