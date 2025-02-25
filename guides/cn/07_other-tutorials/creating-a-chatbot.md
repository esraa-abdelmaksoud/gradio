# 如何创建一个聊天机器人

Tags: NLP, TEXT, CHAT
Related spaces: https://huggingface.co/spaces/gradio/chatbot_streaming, https://huggingface.co/spaces/project-baize/Baize-7B, 
## 简介

聊天机器人在自然语言处理 (NLP) 研究和工业界被广泛使用。由于聊天机器人是直接由客户和最终用户使用的，因此验证聊天机器人在面对各种输入提示时的行为是否符合预期至关重要。

通过使用 `gradio`，您可以轻松构建聊天机器人模型的演示，并与用户共享，或使用直观的聊天机器人图形界面自己尝试。

本教程将展示如何使用 Gradio 制作几种不同类型的聊天机器人用户界面：首先是一个简单的文本显示界面，其次是一个用于流式文本响应的界面，最后一个是可以处理媒体文件的聊天机器人。我们创建的聊天机器人界面将如下所示：

$ 演示 _ 聊天机器人 _ 流式

**先决条件**：我们将使用 `gradio.Blocks` 类来构建我们的聊天机器人演示。
如果您对此还不熟悉，可以[先阅读 Blocks 指南](https://gradio.app/quickstart/#blocks-more-flexibility-and-control)。同时，请确保您使用的是**最新版本**的 Gradio：`pip install --upgrade gradio`。

## 简单聊天机器人演示

让我们从重新创建上面的简单演示开始。正如您可能已经注意到的，我们的机器人只是随机对任何输入回复 " 你好吗？"、" 我爱你 " 或 " 我非常饿 "。这是使用 Gradio 创建此演示的代码：

$ 代码 _ 简单聊天机器人

这里有三个 Gradio 组件：

* 一个 `Chatbot`，其值将整个对话的历史记录作为用户和机器人之间的响应对列表存储。
* 一个文本框，用户可以在其中键入他们的消息，然后按下 Enter/ 提交以触发聊天机器人的响应
* 一个 `ClearButton` 按钮，用于清除文本框和整个聊天机器人的历史记录

我们有一个名为 `respond()` 的函数，它接收聊天机器人的整个历史记录，附加一个随机消息，等待 1 秒，然后返回更新后的聊天历史记录。`respond()` 函数在返回时还清除了文本框。

当然，实际上，您会用自己更复杂的函数替换 `respond()`，该函数可能调用预训练模型或 API 来生成响应。

$ 演示 _ 简单聊天机器人

## 为聊天机器人添加流式响应

我们可以通过几种方式来改进上述聊天机器人的用户体验。首先，我们可以流式传输响应，以便用户不必等待太长时间才能生成消息。其次，我们可以让用户的消息在聊天历史记录中立即出现，同时聊天机器人的响应正在生成。以下是实现这一点的代码：

$code_chatbot_streaming

当用户提交他们的消息时，您会注意到我们现在使用 `.then()` 与三个事件事件 *链* 起来：

1. 第一个方法 `user()` 用用户消息更新聊天机器人并清除输入字段。此方法还使输入字段处于非交互状态，以防聊天机器人正在响应时用户发送另一条消息。由于我们希望此操作立即发生，因此我们设置 `queue=False`，以跳过任何可能的队列。聊天机器人的历史记录附加了`(user_message, None)`，其中的 `None` 表示机器人未作出响应。

2. 第二个方法 `bot()` 使用机器人的响应更新聊天机器人的历史记录。我们不是创建新消息，而是将先前创建的 `None` 消息替换为机器人的响应。最后，我们逐个字符构造消息并 `yield` 正在构建的中间输出。Gradio 会自动将带有 `yield` 关键字的任何函数 [转换为流式输出接口](/key-features/#iterative-outputs)。

3. 第三个方法使输入字段再次可以交互，以便用户可以向机器人发送另一条消息。

当然，实际上，您会用自己更复杂的函数替换 `bot()`，该函数可能调用预训练模型或 API 来生成响应。

最后，我们通过运行 `demo.queue()` 启用排队，这对于流式中间输出是必需的。您可以通过滚动到本页面顶部的演示来尝试改进后的聊天机器人。

## 添加 Markdown、图片、音频或视频

`gr.Chatbot` 组件支持包含加粗、斜体和代码等一部分 Markdown 功能。例如，我们可以编写一个函数，以粗体回复用户的消息，类似于 **That's cool!**，如下所示：

```py
def bot(history):
    response = "**That's cool!**"
    history[-1][1] = response
    return history
```

此外，它还可以处理图片、音频和视频等媒体文件。要传递媒体文件，我们必须将文件作为两个字符串的元组传递，如`(filepath, alt_text)` 所示。`alt_text` 是可选的，因此您还可以只传入只有一个元素的元组`(filepath,)`，如下所示：

```python
def add_file(history, file):
    history = history + [((file.name,), None)]
    return history
```

将所有这些放在一起，我们可以创建一个*多模态*聊天机器人，其中包含一个文本框供用户提交文本，以及一个文件上传按钮供提交图像 / 音频 / 视频文件。余下的代码看起来与之前的代码几乎相同：

$code_chatbot_multimodal
$demo_chatbot_multimodal

你完成了！这就是构建聊天机器人模型界面所需的所有代码。最后，我们将结束我们的指南，并提供一些在 Spaces 上运行的聊天机器人的链接，以让你了解其他可能性：

* [project-baize/Baize-7B](https://huggingface.co/spaces/project-baize/Baize-7B)：一个带有停止生成和重新生成响应功能的样式化聊天机器人。 
* [MAGAer13/mPLUG-Owl](https://huggingface.co/spaces/MAGAer13/mPLUG-Owl)：一个多模态聊天机器人，允许您对响应进行投票。
