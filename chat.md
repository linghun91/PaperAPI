# 📝 Paper API Chat 聊天系统完整指南

## 🎯 概述
Paper API 提供了强大的聊天系统，支持现代化的 Adventure 组件、自定义渲染器、事件处理和多种消息发送方式。

---

## 🏗️ 核心架构

### 📦 主要包结构
- `io.papermc.paper.chat` - 聊天渲染器
- `io.papermc.paper.event.player` - 聊天事件
- `org.bukkit.command.CommandSender` - 消息发送接口
- `org.bukkit.entity.Player` - 玩家聊天功能
- `org.bukkit.Server` - 服务器广播功能

---

## 🎨 聊天渲染器 (ChatRenderer)

### 🔧 ChatRenderer 接口
```java
@FunctionalInterface
public interface ChatRenderer {
    Component render(Player source, Component sourceDisplayName, 
                    Component message, Audience viewer);
    
    static ChatRenderer defaultRenderer();
    static ChatRenderer viewerUnaware(ChatRenderer.ViewerUnaware renderer);
}
```

**功能特性：**
- 🎯 **自定义消息渲染** - 完全控制聊天消息的显示格式
- 👥 **观众感知** - 可以为不同观众显示不同内容
- 🔄 **函数式接口** - 支持 Lambda 表达式和方法引用
- 🎨 **Adventure 组件** - 支持富文本、颜色、点击事件等

### 🎭 ChatRenderer.ViewerUnaware
```java
public interface ViewerUnaware {
    Component render(Player source, Component sourceDisplayName, Component message);
}
```

**使用场景：**
- 📢 **统一消息** - 所有玩家看到相同内容
- ⚡ **性能优化** - 只渲染一次，减少计算开销
- 🎯 **简单场景** - 不需要个性化显示的聊天

---

## 📨 消息发送系统

### 🎯 CommandSender 方法

#### 🔤 基础文本消息
```java
// 发送普通文本
void sendMessage(String message);
void sendMessage(String... messages);

// 发送纯文本（无格式化）
void sendPlainMessage(String message);

// 发送 MiniMessage 格式
void sendRichMessage(String message);
void sendRichMessage(String message, TagResolver... resolvers);
```

#### 🎨 Adventure 组件消息
```java
// 发送 Adventure 组件
void sendMessage(Component message);
void sendMessage(Identity identity, Component message, MessageType type);
```

### 👤 Player 特有功能

#### 💬 聊天相关方法
```java
// 玩家发送聊天消息
void chat(String msg);

// 聊天补全建议
void addCustomChatCompletions(Collection<String> completions);
void removeCustomChatCompletions(Collection<String> completions);

// 显示名称
Component displayName();
void displayName(Component displayName);

// 玩家列表名称
Component playerListName();
void playerListName(Component name);
```

#### 🎮 动作栏和标题
```java
// 动作栏消息
void sendActionBar(Component message);

// 标题显示
void showTitle(Title title);
void sendTitlePart(TitlePart<T> part, T value);
```

### 🌐 Server 广播功能

#### 📢 全服广播
```java
// 广播给所有玩家
int broadcast(Component message);

// 权限广播
int broadcast(Component message, String permission);

// 创建命令发送者
CommandSender createCommandSender(Consumer<? super Component> feedback);
```

---

## 🎪 聊天事件系统

### ⚡ AsyncChatEvent (推荐)
```java
@EventHandler
public void onAsyncChat(AsyncChatEvent event) {
    Player player = event.getPlayer();
    Component message = event.message();
    Component originalMessage = event.originalMessage();
    ChatRenderer renderer = event.renderer();
    Set<Audience> viewers = event.viewers();
    SignedMessage signedMessage = event.signedMessage();
    
    // 修改消息
    event.message(Component.text("修改后的消息"));
    
    // 修改渲染器
    event.renderer(ChatRenderer.defaultRenderer());
    
    // 修改观众
    viewers.removeIf(audience -> /* 条件 */);
    
    // 取消事件
    event.setCancelled(true);
}
```

### 🐌 ChatEvent (已弃用)
```java
@Deprecated // 会阻塞主线程，建议使用 AsyncChatEvent
@EventHandler
public void onChat(ChatEvent event) {
    // 同 AsyncChatEvent 的 API
}
```

### 📋 AbstractChatEvent 基础功能
```java
// 获取和设置消息
Component message();
void message(Component message);

// 获取原始消息（不受修改影响）
Component originalMessage();

// 获取和设置渲染器
ChatRenderer renderer();
void renderer(ChatRenderer renderer);

// 获取观众集合
Set<Audience> viewers();

// 获取签名消息
SignedMessage signedMessage();

// 取消状态
boolean isCancelled();
void setCancelled(boolean cancel);
```

---

## 🎨 实用功能示例

### 🌈 自定义聊天格式
```java
public class CustomChatRenderer implements ChatRenderer {
    @Override
    public Component render(Player source, Component sourceDisplayName, 
                           Component message, Audience viewer) {
        return Component.text()
            .append(Component.text("["))
            .append(sourceDisplayName)
            .append(Component.text("] "))
            .append(message)
            .color(NamedTextColor.GRAY)
            .build();
    }
}
```

### 🎯 权限聊天频道
```java
@EventHandler
public void onChat(AsyncChatEvent event) {
    String msg = PlainTextComponentSerializer.plainText()
        .serialize(event.message());
    
    if (msg.startsWith("@admin ")) {
        // 管理员频道
        event.viewers().removeIf(audience -> 
            !(audience instanceof Player player) || 
            !player.hasPermission("chat.admin"));
        
        event.renderer((source, displayName, message, viewer) ->
            Component.text("[ADMIN] ")
                .color(NamedTextColor.RED)
                .append(displayName)
                .append(Component.text(": "))
                .append(message));
    }
}
```

### 📱 聊天过滤器
```java
@EventHandler
public void onChat(AsyncChatEvent event) {
    String message = PlainTextComponentSerializer.plainText()
        .serialize(event.message());
    
    // 敏感词过滤
    if (containsBadWords(message)) {
        event.setCancelled(true);
        event.getPlayer().sendMessage(
            Component.text("消息包含敏感词！")
                .color(NamedTextColor.RED));
        return;
    }
    
    // 防刷屏
    if (isSpamming(event.getPlayer())) {
        event.setCancelled(true);
        return;
    }
}
```

---

## 🎪 有趣功能实现

### 🎭 **角色扮演聊天** - 沉浸式体验
- 根据玩家职业显示不同聊天前缀
- 支持表情动作命令 (/me, /do)
- 距离聊天系统（只有附近玩家能听到）

### 🌍 **多语言聊天** - 国际化支持  
- 自动翻译聊天消息
- 根据玩家语言设置显示本地化内容
- 支持多种字符集和表情符号

### 🎨 **富文本聊天** - 视觉增强
- MiniMessage 格式支持
- 渐变色文字效果
- 可点击链接和悬停提示
- 自定义字体和样式

### 🔒 **私密聊天** - 安全通信
- 私聊消息加密
- 聊天频道系统
- 好友聊天功能
- 群组聊天管理

### 🎮 **游戏化聊天** - 互动体验
- 聊天等级系统
- 表情包和贴纸
- 聊天成就系统
- 消息点赞功能

### 🛡️ **管理工具** - 服务器管理
- 实时聊天监控
- 自动违规检测
- 聊天记录存储
- 批量消息管理

---

## ⚠️ 重要注意事项

### 🔄 事件选择
- ✅ **推荐使用 AsyncChatEvent** - 异步处理，不阻塞服务器
- ❌ **避免使用 ChatEvent** - 同步处理，可能影响性能

### 🎯 性能优化
- 🚀 **使用 ViewerUnaware** - 统一消息时减少渲染次数
- 💾 **缓存渲染结果** - 避免重复计算
- ⚡ **异步处理** - 复杂逻辑放在异步线程

### 🔒 安全考虑
- 🛡️ **输入验证** - 防止恶意消息注入
- 🚫 **权限检查** - 确保功能访问控制
- 📝 **日志记录** - 记录重要聊天事件

---

## 🔧 高级功能详解

### 🎪 **聊天渲染器高级用法**

#### 🎨 条件渲染器
```java
public class ConditionalChatRenderer implements ChatRenderer {
    @Override
    public Component render(Player source, Component sourceDisplayName,
                           Component message, Audience viewer) {
        if (viewer instanceof Player player) {
            // VIP 玩家看到特殊格式
            if (player.hasPermission("chat.vip")) {
                return Component.text()
                    .append(Component.text("⭐ ", NamedTextColor.GOLD))
                    .append(sourceDisplayName)
                    .append(Component.text(": ", NamedTextColor.WHITE))
                    .append(message)
                    .build();
            }

            // 普通玩家看到标准格式
            return Component.text()
                .append(sourceDisplayName)
                .append(Component.text(": ", NamedTextColor.GRAY))
                .append(message)
                .build();
        }

        return ChatRenderer.defaultRenderer()
            .render(source, sourceDisplayName, message, viewer);
    }
}
```

#### 🌈 动态颜色渲染器
```java
public class RainbowChatRenderer implements ChatRenderer {
    private final List<TextColor> colors = Arrays.asList(
        NamedTextColor.RED, NamedTextColor.GOLD, NamedTextColor.YELLOW,
        NamedTextColor.GREEN, NamedTextColor.AQUA, NamedTextColor.BLUE,
        NamedTextColor.LIGHT_PURPLE
    );

    @Override
    public Component render(Player source, Component sourceDisplayName,
                           Component message, Audience viewer) {
        String text = PlainTextComponentSerializer.plainText().serialize(message);
        Component.Builder builder = Component.text();

        for (int i = 0; i < text.length(); i++) {
            TextColor color = colors.get(i % colors.size());
            builder.append(Component.text(text.charAt(i), color));
        }

        return Component.text()
            .append(sourceDisplayName)
            .append(Component.text(": "))
            .append(builder.build())
            .build();
    }
}
```

### 📱 **消息类型和格式**

#### 🎯 消息类型枚举
```java
public enum ChatMessageType {
    CHAT,           // 普通聊天
    SYSTEM,         // 系统消息
    GAME_INFO,      // 游戏信息
    SAY_COMMAND,    // /say 命令
    MSG_COMMAND_INCOMING,  // 私聊接收
    MSG_COMMAND_OUTGOING,  // 私聊发送
    TEAM_MSG_COMMAND_INCOMING,  // 团队消息接收
    TEAM_MSG_COMMAND_OUTGOING,  // 团队消息发送
    EMOTE_COMMAND   // 表情命令
}
```

#### 🎨 Adventure 组件构建
```java
// 基础组件
Component message = Component.text("Hello World!")
    .color(NamedTextColor.GREEN)
    .decorate(TextDecoration.BOLD);

// 复合组件
Component complex = Component.text()
    .append(Component.text("[", NamedTextColor.GRAY))
    .append(Component.text("INFO", NamedTextColor.BLUE))
    .append(Component.text("] ", NamedTextColor.GRAY))
    .append(Component.text("服务器消息", NamedTextColor.WHITE))
    .build();

// 可点击组件
Component clickable = Component.text("点击访问官网")
    .color(NamedTextColor.BLUE)
    .decorate(TextDecoration.UNDERLINED)
    .clickEvent(ClickEvent.openUrl("https://example.com"))
    .hoverEvent(HoverEvent.showText(Component.text("点击打开网站")));

// 悬停提示
Component hover = Component.text("悬停查看详情")
    .hoverEvent(HoverEvent.showText(
        Component.text()
            .append(Component.text("玩家信息:\n", NamedTextColor.GOLD))
            .append(Component.text("等级: 50\n", NamedTextColor.WHITE))
            .append(Component.text("金币: 1000", NamedTextColor.YELLOW))
            .build()
    ));
```

### 🎮 **实际应用案例**

#### 🏆 **等级聊天系统**
```java
@EventHandler
public void onChat(AsyncChatEvent event) {
    Player player = event.getPlayer();
    int level = getPlayerLevel(player);

    // 根据等级设置聊天颜色和前缀
    TextColor levelColor = getLevelColor(level);
    String prefix = getLevelPrefix(level);

    event.renderer((source, displayName, message, viewer) -> {
        return Component.text()
            .append(Component.text(prefix, levelColor))
            .append(Component.text(" "))
            .append(displayName)
            .append(Component.text(": ", NamedTextColor.WHITE))
            .append(message.color(levelColor))
            .build();
    });
}

private TextColor getLevelColor(int level) {
    if (level >= 100) return NamedTextColor.GOLD;
    if (level >= 50) return NamedTextColor.LIGHT_PURPLE;
    if (level >= 25) return NamedTextColor.BLUE;
    if (level >= 10) return NamedTextColor.GREEN;
    return NamedTextColor.WHITE;
}

private String getLevelPrefix(int level) {
    if (level >= 100) return "[传奇]";
    if (level >= 50) return "[大师]";
    if (level >= 25) return "[专家]";
    if (level >= 10) return "[熟练]";
    return "[新手]";
}
```

#### 🌍 **区域聊天系统**
```java
@EventHandler
public void onChat(AsyncChatEvent event) {
    Player player = event.getPlayer();
    Location location = player.getLocation();
    String region = getRegionName(location);

    // 只有同区域的玩家能看到消息
    event.viewers().removeIf(audience -> {
        if (!(audience instanceof Player other)) return true;
        return !getRegionName(other.getLocation()).equals(region);
    });

    // 添加区域前缀
    event.renderer((source, displayName, message, viewer) -> {
        return Component.text()
            .append(Component.text("[" + region + "] ", NamedTextColor.AQUA))
            .append(displayName)
            .append(Component.text(": "))
            .append(message)
            .build();
    });
}
```

#### 💬 **私聊系统**
```java
public class PrivateMessageManager {
    private final Map<UUID, UUID> lastMessaged = new HashMap<>();

    public void sendPrivateMessage(Player sender, Player recipient, String message) {
        Component senderMsg = Component.text()
            .append(Component.text("[你 -> ", NamedTextColor.GRAY))
            .append(recipient.displayName())
            .append(Component.text("] ", NamedTextColor.GRAY))
            .append(Component.text(message, NamedTextColor.WHITE))
            .build();

        Component recipientMsg = Component.text()
            .append(Component.text("[", NamedTextColor.GRAY))
            .append(sender.displayName())
            .append(Component.text(" -> 你] ", NamedTextColor.GRAY))
            .append(Component.text(message, NamedTextColor.WHITE))
            .build();

        sender.sendMessage(senderMsg);
        recipient.sendMessage(recipientMsg);

        // 记录最后聊天对象
        lastMessaged.put(sender.getUniqueId(), recipient.getUniqueId());
        lastMessaged.put(recipient.getUniqueId(), sender.getUniqueId());
    }

    public void replyToLastMessage(Player sender, String message) {
        UUID lastRecipientId = lastMessaged.get(sender.getUniqueId());
        if (lastRecipientId == null) {
            sender.sendMessage(Component.text("没有可回复的消息！", NamedTextColor.RED));
            return;
        }

        Player recipient = Bukkit.getPlayer(lastRecipientId);
        if (recipient == null || !recipient.isOnline()) {
            sender.sendMessage(Component.text("玩家不在线！", NamedTextColor.RED));
            return;
        }

        sendPrivateMessage(sender, recipient, message);
    }
}
```

---

## �️ 最佳实践和技巧

### ⚡ **性能优化技巧**

#### 🚀 缓存渲染结果
```java
public class CachedChatRenderer implements ChatRenderer {
    private final Map<String, Component> cache = new ConcurrentHashMap<>();
    private final ChatRenderer delegate;

    public CachedChatRenderer(ChatRenderer delegate) {
        this.delegate = delegate;
    }

    @Override
    public Component render(Player source, Component sourceDisplayName,
                           Component message, Audience viewer) {
        String cacheKey = generateCacheKey(source, sourceDisplayName, message, viewer);

        return cache.computeIfAbsent(cacheKey, k ->
            delegate.render(source, sourceDisplayName, message, viewer));
    }

    private String generateCacheKey(Player source, Component sourceDisplayName,
                                   Component message, Audience viewer) {
        return source.getUniqueId() + ":" +
               PlainTextComponentSerializer.plainText().serialize(sourceDisplayName) + ":" +
               PlainTextComponentSerializer.plainText().serialize(message) + ":" +
               (viewer instanceof Player ? ((Player) viewer).getUniqueId() : "console");
    }
}
```

#### 🎯 批量消息处理
```java
public class BatchMessageSender {
    private final Map<Audience, List<Component>> pendingMessages = new HashMap<>();
    private final BukkitTask flushTask;

    public BatchMessageSender(Plugin plugin) {
        // 每 tick 批量发送消息
        this.flushTask = Bukkit.getScheduler().runTaskTimer(plugin, this::flush, 1L, 1L);
    }

    public void queueMessage(Audience audience, Component message) {
        pendingMessages.computeIfAbsent(audience, k -> new ArrayList<>()).add(message);
    }

    private void flush() {
        for (Map.Entry<Audience, List<Component>> entry : pendingMessages.entrySet()) {
            Audience audience = entry.getKey();
            List<Component> messages = entry.getValue();

            if (!messages.isEmpty()) {
                // 合并多条消息
                Component combined = Component.join(JoinConfiguration.newlines(), messages);
                audience.sendMessage(combined);
                messages.clear();
            }
        }
    }
}
```

### 🔒 **安全和验证**

#### 🛡️ 消息过滤器
```java
public class ChatSecurityFilter {
    private final Set<String> bannedWords;
    private final Pattern urlPattern = Pattern.compile(
        "https?://[\\w\\-._~:/?#\\[\\]@!$&'()*+,;=%]+");

    public ChatSecurityFilter(Set<String> bannedWords) {
        this.bannedWords = bannedWords.stream()
            .map(String::toLowerCase)
            .collect(Collectors.toSet());
    }

    public FilterResult filterMessage(Player player, String message) {
        String lowerMessage = message.toLowerCase();

        // 检查敏感词
        for (String bannedWord : bannedWords) {
            if (lowerMessage.contains(bannedWord)) {
                return FilterResult.blocked("消息包含敏感词: " + bannedWord);
            }
        }

        // 检查 URL（如果玩家没有权限）
        if (!player.hasPermission("chat.url") && urlPattern.matcher(message).find()) {
            return FilterResult.blocked("您没有权限发送链接");
        }

        // 检查消息长度
        if (message.length() > 256) {
            return FilterResult.blocked("消息过长，最多256个字符");
        }

        // 检查重复字符
        if (hasExcessiveRepeating(message)) {
            return FilterResult.blocked("请不要发送重复字符");
        }

        return FilterResult.allowed(message);
    }

    private boolean hasExcessiveRepeating(String message) {
        int maxRepeating = 5;
        int count = 1;
        char lastChar = 0;

        for (char c : message.toCharArray()) {
            if (c == lastChar) {
                count++;
                if (count > maxRepeating) return true;
            } else {
                count = 1;
                lastChar = c;
            }
        }
        return false;
    }

    public static class FilterResult {
        private final boolean allowed;
        private final String message;
        private final String reason;

        private FilterResult(boolean allowed, String message, String reason) {
            this.allowed = allowed;
            this.message = message;
            this.reason = reason;
        }

        public static FilterResult allowed(String message) {
            return new FilterResult(true, message, null);
        }

        public static FilterResult blocked(String reason) {
            return new FilterResult(false, null, reason);
        }

        // getters...
    }
}
```

#### 🚫 防刷屏系统
```java
public class AntiSpamManager {
    private final Map<UUID, SpamData> playerData = new ConcurrentHashMap<>();
    private final long cooldownMs;
    private final int maxMessagesPerMinute;

    public AntiSpamManager(long cooldownMs, int maxMessagesPerMinute) {
        this.cooldownMs = cooldownMs;
        this.maxMessagesPerMinute = maxMessagesPerMinute;
    }

    public boolean canSendMessage(Player player) {
        UUID playerId = player.getUniqueId();
        long now = System.currentTimeMillis();

        SpamData data = playerData.computeIfAbsent(playerId, k -> new SpamData());

        // 检查冷却时间
        if (now - data.lastMessageTime < cooldownMs) {
            return false;
        }

        // 清理过期的消息记录
        data.messageTimes.removeIf(time -> now - time > 60000); // 1分钟

        // 检查频率限制
        if (data.messageTimes.size() >= maxMessagesPerMinute) {
            return false;
        }

        // 记录消息
        data.lastMessageTime = now;
        data.messageTimes.add(now);

        return true;
    }

    private static class SpamData {
        long lastMessageTime = 0;
        final List<Long> messageTimes = new ArrayList<>();
    }
}
```

### 📊 **聊天统计和分析**

#### 📈 聊天数据收集器
```java
public class ChatAnalytics {
    private final Map<UUID, PlayerChatStats> playerStats = new ConcurrentHashMap<>();

    @EventHandler
    public void onChat(AsyncChatEvent event) {
        Player player = event.getPlayer();
        String message = PlainTextComponentSerializer.plainText()
            .serialize(event.message());

        PlayerChatStats stats = playerStats.computeIfAbsent(
            player.getUniqueId(), k -> new PlayerChatStats());

        stats.incrementMessageCount();
        stats.addCharacterCount(message.length());
        stats.updateLastMessageTime();

        // 分析消息情感（示例）
        SentimentType sentiment = analyzeSentiment(message);
        stats.recordSentiment(sentiment);
    }

    public Component generateStatsReport(Player player) {
        PlayerChatStats stats = playerStats.get(player.getUniqueId());
        if (stats == null) {
            return Component.text("暂无聊天数据", NamedTextColor.GRAY);
        }

        return Component.text()
            .append(Component.text("=== 聊天统计 ===\n", NamedTextColor.GOLD))
            .append(Component.text("消息总数: " + stats.getMessageCount() + "\n"))
            .append(Component.text("字符总数: " + stats.getCharacterCount() + "\n"))
            .append(Component.text("平均消息长度: " + stats.getAverageMessageLength() + "\n"))
            .append(Component.text("最后发言: " + formatTime(stats.getLastMessageTime())))
            .build();
    }

    private SentimentType analyzeSentiment(String message) {
        // 简单的情感分析示例
        String lower = message.toLowerCase();
        if (lower.contains("开心") || lower.contains("哈哈") || lower.contains("😊")) {
            return SentimentType.POSITIVE;
        } else if (lower.contains("生气") || lower.contains("讨厌") || lower.contains("😠")) {
            return SentimentType.NEGATIVE;
        }
        return SentimentType.NEUTRAL;
    }

    public enum SentimentType {
        POSITIVE, NEGATIVE, NEUTRAL
    }
}
```

### 🎨 **高级组件技巧**

#### 🌈 渐变色文字
```java
public class GradientTextBuilder {
    public static Component createGradient(String text, TextColor startColor, TextColor endColor) {
        Component.Builder builder = Component.text();

        for (int i = 0; i < text.length(); i++) {
            float ratio = (float) i / (text.length() - 1);
            TextColor color = interpolateColor(startColor, endColor, ratio);
            builder.append(Component.text(text.charAt(i), color));
        }

        return builder.build();
    }

    private static TextColor interpolateColor(TextColor start, TextColor end, float ratio) {
        int startRed = start.red();
        int startGreen = start.green();
        int startBlue = start.blue();

        int endRed = end.red();
        int endGreen = end.green();
        int endBlue = end.blue();

        int red = (int) (startRed + (endRed - startRed) * ratio);
        int green = (int) (startGreen + (endGreen - startGreen) * ratio);
        int blue = (int) (startBlue + (endBlue - startBlue) * ratio);

        return TextColor.color(red, green, blue);
    }
}
```

#### 📱 交互式消息
```java
public class InteractiveMessageBuilder {
    public static Component createPaginatedMessage(List<String> content, int page, int itemsPerPage) {
        int totalPages = (int) Math.ceil((double) content.size() / itemsPerPage);
        int startIndex = (page - 1) * itemsPerPage;
        int endIndex = Math.min(startIndex + itemsPerPage, content.size());

        Component.Builder builder = Component.text();

        // 页面内容
        for (int i = startIndex; i < endIndex; i++) {
            builder.append(Component.text(content.get(i) + "\n"));
        }

        // 分页控制
        builder.append(Component.text("\n--- 第 " + page + "/" + totalPages + " 页 ---\n", NamedTextColor.GRAY));

        // 上一页按钮
        if (page > 1) {
            builder.append(Component.text("[上一页] ", NamedTextColor.BLUE)
                .clickEvent(ClickEvent.runCommand("/page " + (page - 1)))
                .hoverEvent(HoverEvent.showText(Component.text("点击查看上一页"))));
        }

        // 下一页按钮
        if (page < totalPages) {
            builder.append(Component.text("[下一页]", NamedTextColor.BLUE)
                .clickEvent(ClickEvent.runCommand("/page " + (page + 1)))
                .hoverEvent(HoverEvent.showText(Component.text("点击查看下一页"))));
        }

        return builder.build();
    }
}
```

---

## �📚 相关资源

- 📖 [Adventure 文档](https://docs.advntr.dev/)
- 🎨 [MiniMessage 格式](https://docs.advntr.dev/minimessage/)
- 🔧 [Paper API 文档](https://jd.papermc.io/paper/1.21.5/)
- 🎯 [事件系统指南](https://docs.papermc.io/paper/dev/events)
- 🌈 [Adventure 组件指南](https://docs.advntr.dev/text.html)
- 🎪 [聊天渲染器示例](https://github.com/PaperMC/Paper/tree/master/paper-api/src/main/java/io/papermc/paper/chat)
- 🔒 [安全开发指南](https://docs.papermc.io/paper/dev/security)
- ⚡ [性能优化建议](https://docs.papermc.io/paper/dev/performance)
