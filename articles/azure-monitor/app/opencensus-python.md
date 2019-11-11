---
title: 使用 Azure Monitor 监视 Python 应用程序（预览版）| Microsoft Docs
description: 提供有关将 OpenCensus Python 连接到 Azure Monitor 的说明
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lingliw
manager: digimobile
origin.date: 10/11/2019
ms.date: 11/4/2019
ms.author: v-lingwu
ms.openlocfilehash: 629cfca39c69b5bac52b2b9ee798e7d77fcdad79
ms.sourcegitcommit: a89eb0007edd5b4558b98c1748b2bd67ca22f4c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73730526"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>为 Python 应用程序设置 Azure Monitor（预览版）

Azure Monitor 通过与 [OpenCensus](https://opencensus.io) 集成，来支持 Python 应用程序的分布式跟踪、指标收集和日志记录。 本文将逐步介绍设置 OpenCensus for Python 并将监视数据发送到 Azure Monitor 的过程。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/zh-cn/pricing/1rmb-trial-full/?form-type=identityauth)。
- Python 安装。 本文使用 [Python 3.7.0](https://www.python.org/downloads/)，不过更低的版本在经过轻微的更改后也可能适用。



## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.cn/)。

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>在 Azure Monitor 中创建 Application Insights 资源

首先需在 Azure Monitor 中创建一个 Application Insights 资源，该资源将生成检测密钥 (ikey)。 然后使用 ikey 配置 OpenCensus SDK，以将遥测数据发送到 Azure Monitor。

1. 选择“创建资源”   >   “开发人员工具” >   “Application Insights”。

   ![添加 Application Insights 资源](./media/opencensus-python/0001-create-resource.png)

1. 此时会显示一个配置框。 参考下表填写输入字段。

   | 设置        | 值           | 说明  |
   | ------------- |:-------------|:-----|
   | **名称**      | 全局唯一值 | 用于标识所监视的应用的名称 |
   | **资源组**     | MyResourceGroup      | 用于托管 Application Insights 数据的新资源组的名称 |
   | **Location** | 美国东部 | 离你较近的位置或离托管应用的位置较近的位置 |

1. 选择“创建”  。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>使用 OpenCensus Python SDK for Azure Monitor 进行检测

安装 OpenCensus Azure Monitor 导出程序：

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> 命令 `python -m pip install opencensus-ext-azure` 假设已经为 Python 安装设置了一个 `PATH` 环境变量。 如果尚未配置此变量，则需要提供 Python 可执行文件所在位置的完整目录路径。 结果是如下所示的命令：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`

SDK 使用三个 Azure Monitor 导出程序将不同类型的遥测数据发送到 Azure Monitor：跟踪、指标和日志。 有关这些遥测数据类型的详细信息，请参阅[数据平台概述](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 按照以下说明通过三个导出程序发送这些遥测数据类型。

### <a name="trace"></a>跟踪

1. 首先，让我们在本地生成一些跟踪数据。 在 Python IDLE 或所选编辑器中，输入以下代码。

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. 运行代码时，系统会重复提示你输入一个值。 每次输入时，值都会输出到 shell，OpenCensus Python 模块会生成相应的 `SpanData` 片段。 OpenCensus 项目[将跟踪定义为 span 树](https://opencensus.io/core-concepts/tracing/)。
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. 虽然输入值有助于演示，但最终我们希望将 `SpanData` 发出到 Azure Monitor。 根据以下代码示例修改上一步中的代码：

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. 现在当你运行 Python 脚本时，系统仍会提示你输入值，但只有此值输出到 shell 中。 创建的 `SpanData` 将发送到 Azure Monitor。 可以在 `dependencies` 下找到发出的 span 数据。

### <a name="metrics"></a>指标

1. 首先，让我们生成一些本地指标数据。 我们将创建一个简单的指标，用于跟踪用户按 Enter 的次数。

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. 运行该代码时，系统会反复提示你按 Enter。 将创建一个指标用于跟踪按 Enter 的次数。 每次输入都会递增值，并且指标信息将显示在控制台中。 该信息包括当前值，以及更新指标时的当前时间戳。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 虽然输入值有助于演示，但最终我们希望将指标数据发出到 Azure Monitor。 根据以下代码示例修改上一步中的代码：

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. 导出程序按固定的间隔将指标数据发送到 Azure Monitor。 默认间隔为每隔 15 秒。 我们正在跟踪单个指标，因此，在每个间隔将会发送此指标数据及其包含的任何值和时间戳。 可在 `customMetrics` 下找到数据。

### <a name="logs"></a>日志

1. 首先，让我们生成一些本地日志数据。

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  代码将持续请求输入值。 对于每个输入值将发出一个日志条目。

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. 虽然输入值有助于演示，但最终我们希望将指标数据发出到 Azure Monitor。 根据以下代码示例修改上一步中的代码：

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. 导出程序会将日志数据发送到 Azure Monitor。 可在 `traces` 下找到数据。

## <a name="start-monitoring-in-the-azure-portal"></a>开始在 Azure 门户中监视

1. 现在可以在 Azure 门户中重新打开 Application Insights“概述”  窗格，查看当前正在运行的应用程序的相关详细信息。 选择“实时指标流”。 

   ![概述窗格的屏幕截图，其中的“实时指标流”在红框中呈选中状态](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. 返回到“概述”窗格。  选择“应用程序映射”  以获取应用程序组件和调用时间之间依赖关系的可视布局。

   ![基本应用程序映射的屏幕截图](./media/opencensus-python/0007-application-map.png)

   由于我们只跟踪一个方法调用，因此应用程序映射的信息不多。 但是，应用程序映射可以通过缩放将多得多的分布式应用程序可视化：

   ![应用程序映射](media/opencensus-python/application-map.png)

3. 选择“调查性能”，以详细分析性能并确定性能减慢的根本原因。 

   ![性能详细信息的屏幕截图](./media/opencensus-python/0008-performance.png)

4. 若要打开端到端事务详细信息体验，请选择“示例”，然后选择显示在右窗格中的任意示例。  

   虽然我们的示例应用只会显示单个事件，但更复杂的应用程序会让你在探索端到端事务时，可以深入到单个事件的调用堆栈级别。

   ![端到端事务界面的屏幕截图](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>使用查询查看数据

可以通过“日志(分析)”选项卡查看从应用程序发送的遥测数据。 

![概述窗格的屏幕截图，其中的“日志(分析)”在红框中呈选中状态](./media/opencensus-python/0010-logs-query.png)

在“活动”下的列表中： 

- 对于使用 Azure Monitor 跟踪导出程序发送的遥测数据，传入请求显示在 `requests` 下。 传出请求或进程内请求显示在 `dependencies` 下。
- 对于使用 Azure Monitor 指标导出程序发送的遥测数据，发送的指标显示在 `customMetrics` 下。
- 对于使用 Azure Monitor 日志导出程序发送的遥测数据，日志显示在 `traces` 下。 异常显示在 `exceptions` 下。

有关如何使用查询和日志的详细信息，请参阅 [Azure Monitor 中的日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="learn-more-about-opencensus-for-python"></a>详细了解 OpenCensus for Python

* [GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自定义](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>后续步骤

* [应用程序映射](../../azure-monitor/app/app-map.md)
* [端到端性能监视](../../azure-monitor/learn/tutorial-performance.md)




