# **智能银行流水转换系统 - 最终架构规范 (v3.0)**

## **第一章：系统总则与设计哲学**

### **1.1 核心设计原则**
1.  **领域专精原则**：放弃通用PDF转换，专注**中国银行流水**领域，以获取最高准确率。
2.  **配置驱动原则**：系统所有行为（银行识别、解析规则、输出格式）由外部配置文件定义，实现“零代码”扩展。
3.  **分层解耦原则**：严格遵循五层架构，层间通过定义良好的**接口**和**数据契约**通信。
4.  **用户友好原则**：提供“一键式”图形界面，所有技术细节对用户透明。
5.  **可交付原则**：架构设计以最终打包成**独立可执行文件（EXE）** 为交付终点。

### **1.2 核心术语定义**
| 术语 | 精确定义 | 示例/格式 |
| :--- | :--- | :--- |
| **文本块 (TextBlock)** | 从源文件中提取出的、具有统一视觉属性的一段连续文字。 | `{text: “张三”, bbox: (x0,y0,x1,y1), page: 1}` |
| **边界框 (BoundingBox)** | 在标准化页面坐标系中，描述文本块位置的矩形。原点(0,0)为页面**左上角**。 | `(100.5, 200.2, 150.3, 210.8)` 单位：点(point) |
| **流水行 (Transaction)** | 一条完整的、结构化的交易记录。是系统处理的**核心业务对象**。 | 包含日期、金额、对方信息等字段的字典或对象。 |
| **银行模板 (BankTemplate)** | 描述某银行流水PDF/TXT文件固定格式的**配置集合**。 | 一个YAML文件，如 `ICBC.yaml`。 |
| **处理上下文 (Context)** | 在一次转换任务中，贯穿所有处理模块的**共享数据总线**。 | 包含输入路径、配置、中间数据、错误列表等。 |

---

## **第二章：五层架构详述**

### **2.1 应用层 (Application Layer) - 与用户的接触点**
**职责**：接收用户指令，提供交互界面，管理任务生命周期。

| 模块 | 接口/形态 | 输入 | 输出 | 关键设计要点 |
| :--- | :--- | :--- | :--- | :--- |
| **图形界面 (GUI)** | 桌面窗口应用，使用`PyQt/PySide`或`Tkinter`实现。 | 鼠标/键盘事件，用户设置。 | 用户指令，可视化反馈（进度、结果）。 | **三面板布局**：<br>1. **输入面板**：文件拖放区、银行预览。<br>2. **控制面板**：开始/停止按钮、设置折叠区。<br>3. **信息面板**：实时日志、进度条。 |
| **命令行界面 (CLI)** | 控制台程序，支持参数化调用。 | 命令行参数(`--input, --bank`)。 | 标准输出/错误流，退出码。 | 提供`--help`，支持通配符批量处理，日志可重定向至文件。 |
| **任务控制器 (TaskController)** | 单例类，协调GUI/CLI与核心引擎。 | `ConversionRequest`对象。 | `ConversionResult`对象，实时进度事件。 | **异步设计**：必须将耗时转换任务放入独立线程/进程，防止界面冻结。 |

### **2.2 打包与部署层 (Packaging Layer) - 交付封装**
**职责**：将Python代码、依赖库、配置资源封装为可独立分发的应用程序。


#### **2.2.1 兼容性约束 (Compatibility Constraints)**
为保证生成的 `BankStatementConverter.exe` 在 Windows 7 系统上正常运行，**开发与打包环境必须遵守以下铁律**：
1.  **版本铁律**：首选的开发和打包环境为 **Python 3.8.10**。这是确保兼容性的最稳定路径。
2.  **架构铁律**：打包所用操作系统的位数必须与目标 Windows 7 系统的位数一致。推荐在 **Windows 7 虚拟机**中完成最终打包，以完美匹配目标环境。
3.  **依赖铁律**：如果项目因故必须使用 Python 3.9-3.11，则必须将特定的 `api-ms-win-core-path-l1-1-0.dll` 文件作为二进制资源打包。此方案为次选，且不适用于 Python 3.12+。
4.  **验证建议**：最终 EXE 必须在 **纯净的 Windows 7 虚拟机** 中进行验证测试，不可仅在更高版本Windows上测试通过即视为成功。




| 组件 | 定义与规范 |
| :--- | :--- |
| **依赖清单** (`requirements.frozen.txt`) | 使用 `pip freeze > requirements.frozen.txt` 生成的**精确版本**依赖列表，确保构建环境一致性。 |
| **打包规范** (`build_spec.yaml`) | 定义打包行为的配置文件。<br>```yaml<br>app_name: “BankStatementConverter”<br>version: “3.0.0.0”<br>entry_point:<br>  gui: “src/launcher.py:run_gui”<br>  cli: “src/launcher.py:run_cli”<br>builder: “PyInstaller”<br>pyinstaller_opts:<br>  — “—onefile”          # 打包为单个exe<br>  — “—add-data=config;config” # 嵌入配置文件夹<br>  — “—hidden-import=pandas._libs.tslibs.np_datetime”<br>``` |
| **资源嵌入** | `config/`, `assets/` 目录必须被嵌入EXE。运行时，程序需能通过 `sys._MEIPASS` 访问这些资源。 |
| **启动器** (`src/launcher.py`) | 统一的程序入口，负责：<br>1. 初始化路径（处理打包后资源定位）。<br>2. 根据参数决定启动GUI还是CLI模式。 |

### **2.3 业务逻辑层 (Business Logic Layer) - 系统的大脑**
**职责**：执行银行流水转换的核心业务流程，是领域知识的具体实现。

| 引擎 | 输入 | 输出 | 算法/规则定义 |
| :--- | :--- | :--- | :--- |
| **银行识别引擎 (BankDetector)** | `List[TextBlock]` (文件前N行)。 | `(BankCode, ConfidenceScore)`。 | **三级投票制**：<br>1. **关键词匹配**（权重0.4）。<br>2. **表头行正则匹配**（权重0.4）。<br>3. **文档结构特征分析**（权重0.2）。<br>总分>=0.7则确认，否则标记为`UNKNOWN`。 |
| **流水解析引擎 (StatementParser)** | `List[TextBlock]`, `BankTemplate`。 | `List[RawTransaction]`。 | **基于模板的两阶段解析**：<br>**阶段一：定位**。根据模板中的 `transaction_area_markers`，锁定交易表格的起止行。<br>**阶段二：提取**。对区域内每一行文本，依次执行模板中定义的 `column_extractors`，生成原始交易数据。 |
| **智能分类引擎 (TransactionClassifier)** | `List[RawTransaction]`。 | `List[EnhancedTransaction]`（增加`category`, `tags`字段）。 | **规则+统计分类**：<br>1. **规则匹配**：对手方名称、交易类型字段与`keywords.yaml`规则库匹配。<br>2. **金额模式**：特定金额范围可能对应“工资”、“还款”。<br>3. **用户反馈学习**（预留）：将用户手动修正结果记录并用于优化。 |

### **2.4 数据处理层 (Data Processing Layer) - 系统的躯干**
**职责**：提供数据转换、清洗、验证的基础能力，是业务逻辑的支撑。

| 处理器 | 接口定义 (伪代码) | 功能描述 |
| :--- | :--- | :--- |
| **文本提取器 (TextExtractor)** | `def extract(file_path: str) -> List[TextBlock]:` | 统一适配不同的源文件。**TXT文件**：按行读取，模拟坐标。**PDF文件**：调用`pdfplumber`，提取真实坐标。 |
| **数据清洗器 (DataCleaner)** | `def clean(transaction: Dict, rules: List[CleaningRule]) -> Dict:` | 执行模板中定义的清洗规则。如：去除金额中的千位符，统一日期格式，过滤无意义的字符。 |
| **数据验证器 (DataValidator)** | `def validate(transactions: List, rules: List[ValidationRule]) -> List[ValidationError]:` | 执行模板中定义的验证规则。包括**字段级验证**（日期格式）和**业务级验证**（余额连续性、借贷平衡）。 |

**数据处理流水线 (ProcessingPipeline) 的精确状态流**：
```
1. 提取文本块 (Extracted)
2. 识别并分割账户信息区域 (AccountInfoSegmented)
3. 识别并分割交易表格区域 (TransactionTableSegmented)
4. 解析原始交易数据 (RawTransactionsParsed)
5. 数据清洗与标准化 (TransactionsCleaned)
6. 智能分类与增强 (TransactionsEnhanced)
7. 最终质量验证 (ValidationCompleted)
```
*每个状态都对应一个可被`Context`对象记录的数据快照，便于调试和错误恢复。*

### **2.5 基础设施层 (Infrastructure Layer) - 系统的基石**
**职责**：提供跨模块的共享工具、服务和资源管理。

| 组件 | 设计规范 |
| :--- | :--- |
| **配置管理器 (ConfigManager)** | **单例模式**。启动时加载`config/`下所有YAML文件，并提供基于银行代码的配置获取接口：`get_config(bank_code, section)`。支持配置热重载（开发模式）。 |
| **日志记录器 (Logger)** | **分级记录**：`DEBUG`（详细流程）、`INFO`（用户操作）、`WARNING`（可处理异常）、`ERROR`（转换失败）。GUI模式时，INFO及以上日志实时显示在信息面板。 |
| **缓存管理器 (CacheManager)** | 内存缓存，存储：<br>• 已加载的银行模板对象。<br>• 近期处理文件的中间结果（用于预览、重试）。<br>**键**：`f"{file_path}:{config_version}"`。 |
| **文件系统守卫 (FileSystemGuard)** | 负责所有文件操作：检查读写权限、创建必要的临时目录、在转换结束后**安全清理**所有临时中间文件（包括可能的TXT过渡文件）。 |

---

## **第三章：核心数据契约**

### **3.1 贯穿系统的上下文对象 (Context)**
```yaml
# 这是一个逻辑定义，实际为类实例
ConversionContext:
  id: “uuid4”                     # 本次任务的唯一标识
  state: “Extracted”              # 当前处理状态 (见2.4节状态流)
  
  # 输入与配置
  input_file_path: “C:/input.pdf”
  user_settings: { output_path: “…”, mode: “detailed” }
  active_bank_template: BankTemplate  # 当前使用的银行模板对象
  
  # 中间数据 (按状态存储)
  snapshots:
    Extracted: List[TextBlock]
    AccountInfoSegmented: { owner: “张三”, account: “622848…”, … }
    RawTransactionsParsed: List[RawTransaction]
    …
    
  # 输出与报告
  result: ConversionResult
  quality_report: QualityReport
  
  # 错误与通信
  errors: List[SystemError]       # 阻止任务继续的错误
  warnings: List[UserWarning]     # 提示用户注意的警告
  event_bus: EventBus             # 用于发布进度事件 (如: “ProgressUpdated”, 65)
```

### **3.2 银行模板配置规范 (YAML Schema)**
```yaml
# config/banks/ICBC.yaml
bank:
  code: “ICBC”
  name: “中国工商银行”
  inherits_from: “common_base”  # 可继承一个基础模板

# 文档物理结构特征
document:
  encoding: “GB18030”           # 文件编码
  account_info_area:
    start_marker: “户名：”       # 账户信息开始标记
    field_patterns:             # 字段提取正则
      — field: “account_name”
        regex: “户名：\\s*(.+)”
      — field: “account_number”
        regex: “账号：\\s*(\\d+)”

# 交易表格逻辑结构特征
transaction_table:
  start_marker: “交易日期”        # 表格开始行标识
  end_marker: “第\\d+页/共\\d+页” # 表格结束行标识
  header_row_index: 0            # 表头所在行（相对于start_marker）
  
  # 列定义：这是解析的核心
  columns:
    — key: “date”                # 内部字段名
      headers: [“交易日期”, “Date”] # 匹配的表头文字
      type: “date”
      extractor:                 # 如何从一行文本中提取本列
        type: “regex”
        pattern: “^(\\d{4}-\\d{2}-\\d{2})”
        group: 1
    — key: “amount”
      headers: [“交易金额”, “发生额”]
      type: “amount”
      extractor:
        type: “split”           # 按空白字符分割后取第N个
        index: 2
      cleaner: “remove_thousands_separator” # 引用的清洗规则ID

# 后处理规则
post_processing:
  validation_rules:              # 验证规则ID列表
    — “balance_continuity”
    — “date_monotonic”
  classification_rules: “icbc_classification” # 引用的分类规则集
```

---

## **第四章：关键交互流程与状态机**

### **4.1 图形界面（GUI）用户操作流程**
```
[用户启动程序]
        ↓
[显示主窗口，初始化完成]
        ↓
[用户拖拽文件至输入面板]
        ↓
[系统触发自动预处理]
    ├─ 提取前50行文本
    ├─ 运行银行识别引擎
    ├─ 高亮显示识别结果与置信度
    └─ 在预览框显示文件片段
        ↓
[用户点击“开始转换”]
        ↓
[系统创建异步转换任务]
        ↓                (异步)
[任务控制器驱动核心引擎]
        ↓
[实时发布进度事件] → [GUI更新进度条和日志]
        ↓
[任务完成或失败]
        ↓
[GUI显示结果对话框]
    ├─ 成功：显示统计信息，提供“打开文件”、“打开目录”按钮。
    └─ 失败：显示错误摘要，提供“查看详情”、“重试”按钮。
```

### **4.2 核心转换引擎状态机**
```python
# 这是一个精确定义的状态转换图，AI可据此实现
STATE_MACHINE = {
    “IDLE”: {
        “on_start”: (“VALIDATING_INPUT”, validate_input_task)
    },
    “VALIDATING_INPUT”: {
        “success”: (“EXTRACTING_TEXT”, extract_text_task),
        “failure”: (“FAILED”, handle_validation_error)
    },
    “EXTRACTING_TEXT”: {
        “success”: (“DETECTING_BANK”, detect_bank_task),
        “failure”: (“FAILED”, handle_extraction_error)
    },
    “DETECTING_BANK”: {
        “success”: (“PARSING_TRANSACTIONS”, parse_transactions_task),
        “unknown_bank”: (“PROMPT_FOR_BANK”, prompt_user_for_bank_task),
        “failure”: (“FAILED”, handle_detection_error)
    },
    “PARSING_TRANSACTIONS”: {
        “success”: (“ENHANCING_DATA”, enhance_data_task),
        “partial_success”: (“ENHANCING_DATA”, log_warnings_and_continue),
        “failure”: (“ATTEMPTING_RECOVERY”, fallback_parsing_task)
    },
    “ENHANCING_DATA”: { ... },
    “GENERATING_OUTPUT”: { ... },
    “COMPLETED”: { ... },
    “FAILED”: { ... }
}
```
*每个`task`函数接收`ConversionContext`，修改其状态和数据，并返回下一个状态标识。*

---

## **第五章：扩展性、错误与质量保障**

### **5.1 扩展点定义**
系统通过以下方式扩展，**无需修改核心代码**：
1.  **新增银行**：在 `config/banks/` 目录下创建新的YAML模板文件。
2.  **新增清洗/验证规则**：在 `config/rules/` 下定义规则，并在模板中引用。
3.  **新增分类关键词**：编辑 `config/classification/keywords.yaml`。
4.  **更换输出格式**：在 `config/output_formats/` 下添加新的输出处理器配置。

### **5.2 系统化错误处理**
| 错误等级 | 处理策略 | 用户反馈 |
| :--- | :--- | :--- |
| **FatalError** (系统级) | 日志记录，清理资源，立即终止程序。 | 弹窗：“程序遇到意外错误，即将关闭。错误日志已保存。” |
| **ValidationError** (输入/业务) | 记录错误，任务标记为失败，提供修复建议。 | 弹窗：“文件格式不匹配。请确认是否为XX银行流水。错误详情：…” |
| **RecoverableError** (解析过程) | 尝试备用解析方案，记录警告，任务继续。 | 界面日志区显示黄色警告：“第X行解析异常，已采用宽松模式，请核对结果。” |
| **AmbiguityWarning** (内容歧义) | 记录并采用默认策略，任务继续。 | 界面日志区显示蓝色提示：“对手方信息‘XX公司’可能包含账号，请确认。” |

### **5.3 质量保障指标体系**
转换任务结束后，必须生成 `QualityReport`：
```yaml
QualityReport:
  completeness:
    total_lines_parsed: 150
    lines_skipped: 2
    completeness_rate: 98.7%
  accuracy:
    validation_errors: 0
    warnings: 3
    estimated_accuracy: “High” # Low/Medium/High
  consistency:
    balance_calculated_correctly: true
    date_sequence_intact: true
  performance:
    total_time_seconds: 12.3
    file_size_mb: 2.1
    processing_speed_mbps: 0.17
```
*该报告会写入输出Excel的“质量报告”工作表，并在GUI结果对话框中摘要显示。*

---

## **第六章：项目与部署清单**

### **6.1 项目目录结构（开发视图）**
```
BankStatementConverter_PROJECT/
├─── build/                    # 打包输出目录 (不应提交git)
├─── dist/                     # 生成的EXE存放于此
├─── src/                      # 源代码
│    ├─── core/                # 核心引擎 (对应业务逻辑、数据处理层)
│    │    ├─── engine.py       # 状态机与流程控制
│    │    ├─── detectors/      # 识别引擎
│    │    ├─── parsers/        # 解析引擎
│    │    └─── classifiers/    # 分类引擎
│    ├─── infrastructure/      # 基础设施层组件
│    ├─── gui/                 # 应用层-GUI
│    └─── cli.py & launcher.py # 应用层-CLI和启动器
├─── config/                   # 配置文件 (必须打包)
│    ├─── banks/               # 银行模板
│    ├─── rules/               # 清洗、验证规则
│    ├─── classification/      # 分类关键词
│    └─── output_formats/      # 输出格式定义
├─── assets/                   # 图标、字体等资源 (必须打包)
├─── docs/                     # 用户手册 (可打包)
├─── tests/                    # 单元测试
├─── build_spec.yaml           # 打包规范
├─── requirements.txt          # 开发依赖
├─── requirements.frozen.txt   # 生产环境精确依赖
└─── README.md                 # 项目说明
```

### **6.2 最终交付清单（用户获得的EXE包内容）**
当用户运行 `BankStatementConverter.exe` 时，其在临时目录展开的内容必须包含：
```
(临时目录)/
├─── BankStatementConverter.exe          # 主程序
├─── config/                             # 完整配置目录
├─── assets/                             # 完整资源目录
├─── pythonXX.dll                        # Python运行时
└─── 其他必要的动态链接库 (.dll)
```
*程序退出后，该临时目录应由 `FileSystemGuard` 安全清理。*

---

**本规范（v3.0）到此结束。** 它定义了从用户双击图标到获得转换结果的完整过程中，系统每一层的职责、每一个模块的接口、每一个数据的流转。遵循此规范，AI或开发人员可以无歧义地构建出完全符合要求的“智能银行流水转换系统”。