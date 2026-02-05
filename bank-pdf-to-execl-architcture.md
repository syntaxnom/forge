# **智能银行流水转换系统 - 模块化精研与生产就绪架构 (v4.0)**

## **第一章：系统总则**

### **1.1 核心设计哲学**
本架构在V3.0基础上，深度融合 **“模块化插件”** 、 **“分层配置”** 与 **“生产环境约束”** 三大支柱，致力于构建一个：
1.  **可维护**：通过“识别稳定与变化，分离共性与个性”，使系统易于理解和修改。
2.  **可扩展**：新增银行、算法、规则无需修改核心引擎，通过配置和插件实现。
3.  **可交付**：明确的生产约束确保最终成果（EXE）能在包括Windows 7在内的目标环境中稳定运行。
4.  **可操作**：架构定义精确到接口与数据契约，可供AI或开发者无损转换为代码。

### **1.2 关键术语定义**
| 术语 | 精确定义 |
| :--- | :--- |
| **处理组件** (`ProcessingComponent`) | 实现标准接口、完成单一职责（如清洗、验证）的可插拔单元。是系统功能的“乐高积木”。 |
| **三层模板** | 管理配置共性与个性的体系：**核心基类**（宪法）、**区域模板**（共性抽象）、**具体银行模板**（个性差异）。 |
| **插件化流水线** | 由`ProcessingComponent`实例动态组装而成的处理序列，其结构和组件由银行模板配置决定。 |
| **规则引擎** | 一个专用的`ProcessingComponent`，它加载外部YAML规则文件，执行业务逻辑（如分类），实现逻辑与代码分离。 |
| **生产约束** | 为确保最终EXE在**Windows 7**及更高版本系统上运行，必须在开发、打包阶段遵守的**强制性技术规则**。 |

---

## **第二章：五层架构详述**

### **2.1 应用层 (Application Layer)**
**职责**：接收用户指令，提供交互界面，管理任务生命周期。
*   **模块**：`图形界面(GUI)`、`命令行界面(CLI)`、`任务控制器(TaskController)`。
*   **关键契约**：`ConversionRequest`（用户请求对象）、`ConversionResult`（任务结果对象）。
*   **生产约束**：GUI框架（如PyQt）的版本选择需兼容Python 3.8。

### **2.2 业务逻辑层 (Business Logic Layer)**
**职责**：驱动核心转换流程，是领域知识的总控中心。
*   **模块**：
    *   `转换引擎(ConversionEngine)`：状态机的驱动者，按`STATE_MACHINE`定义协调各模块。
    *   `银行识别引擎(BankDetector)`：分析文本，匹配银行模板。
    *   `智能分类引擎(TransactionClassifier)`：总控分类，可能调用规则引擎组件。
*   **关键契约**：`ProcessingContext`（贯穿始终的上下文对象，承载所有中间数据与状态）。

### **2.3 数据处理层 (Data Processing Layer)**
**职责**：提供数据转换、清洗、验证等基础能力的“武器库”，全部模块化。
*   **核心机制**：**插件化流水线工厂 (`PipelineFactory`)**。
    1.  **组件接口**：所有组件必须实现`ProcessingComponent`接口，提供`initialize`, `execute`方法，并拥有全局唯一的`component_id`。
    2.  **组件注册表** (`ComponentRegistry`)：系统启动时，所有组件类在此注册。工厂通过`component_id`从此获取组件类。
    3.  **动态组装**：工厂读取银行模板中的`execution_pipeline`配置，实例化组件，注入参数，组装成可执行的流水线对象。
*   **模块**：所有`提取器(Extractor)`、`清洗器(Cleaner)`、`验证器(Validator)`、`增强器(Enricher)`均以`ProcessingComponent`形式存在，存放于`components/`目录下。

### **2.4 配置与资源层 (Configuration & Resources Layer)**
**职责**：管理系统所有静态知识、规则和模板，是“数据驱动”的核心体现。

#### **2.4.1 三层模板配置体系**
```
config/
├── templates/
│   ├── _core.base.yaml       # L1: 核心基类 (所有银行的“宪法”)
│   └── regions/              # L2: 区域/类型模板
│       ├── chinese_state_owned.yaml
│       └── credit_card.yaml
├── banks/                    # L3: 具体银行模板 (ICBC.yaml, CMB.yaml...)
├── rules/                    # 规则引擎使用的YAML规则集
└── build_spec.yaml           # 打包规范
```
*   **加载与合并**：`ConfigManager`在加载`ICBC.yaml`时，自动递归合并其继承链（L3 -> L2 -> L1）上的所有配置，形成该银行的完整、扁平化配置对象。

#### **2.4.2 外部化规则引擎**
*   **定义**：规则集是YAML文件，包含一系列`condition`（条件）和`action`（动作）。
*   **执行**：`rule_engine`组件加载指定规则集，对交易数据按优先级评估条件，执行匹配的动作（如设置分类、添加标签）。
*   **价值**：业务专家可通过修改YAML规则来调整系统行为，无需开发介入。

### **2.5 打包与部署层 (Packaging & Deployment Layer)**
**职责**：将系统封装为符合**生产约束**的、可独立分发的应用程序。**这是确保Windows 7兼容性的关键层。**

#### **2.5.1 生产兼容性约束 (铁律)**
在开始任何开发与打包工作前，必须确立并遵守以下约束：

| 约束维度 | 具体要求 (必须遵守) | 理由与依据 |
| :--- | :--- | :--- |
| **Python版本** | **必须使用 Python 3.8.x (推荐3.8.10) 进行开发和最终打包。** | Python 3.9+ 移除了对Windows 7的核心API支持。3.8是官方支持Win7的最后一个主版本。 |
| **系统架构** | **打包环境必须与最低目标系统架构一致。** 若要支持32位Win7，则必须在**32位操作系统**上使用**32位Python 3.8**进行打包。 | 64位环境打包的EXE无法在32位系统运行。 |
| **依赖补丁** | **若坚持使用Python 3.9, 3.10, 3.11 (不推荐)**，则必须将修补DLL (`api-ms-win-core-path-l1-1-0.dll`) 通过`--add-binary`打包。**严禁使用Python 3.12+**。 | Python 3.9-3.11在Win7上运行需此DLL。Python 3.12使用了Win7不存在的内核API，无法补救。 |
| **验证环境** | 最终EXE必须在**已安装SP1及关键更新**的Windows 7虚拟机中进行验收测试。 | 确保在真实目标环境中的兼容性。 |

#### **2.5.2 打包规范 (`build_spec.yaml`)**
```yaml
# 位于项目根目录，是打包操作的唯一依据
app:
  name: “BankPDF-to-excel”
  version: “4.0.0.0”
  description: “智能银行流水转换系统”

# 生产约束声明
compatibility:
  min_windows_version: “6.1” # Windows 7 内部版本号
  required_python: “3.8.10”
  arch: “x86” # 或 “x86_64”，必须明确

# 构建配置
build:
  tool: “PyInstaller”
  entry_point:
    gui: “src/launcher.py:run_gui”
    cli: “src/launcher.py:run_cli”
  options:
    - “—onefile”
    - “—name=${app.name}”
    - “—add-data=config;config”          # 嵌入整个配置目录
    - “—add-data=assets;assets”
    - “—hidden-import=openpyxl.cell._writer” # 解决常见隐藏导入
    # 如果使用Python 3.9-3.11，启用下行：
    # - “—add-binary=./vendor/api-ms-win-core-path-l1-1-0.dll;.”
  excludes: [“tkinter”] # 排除不必要的库以减少体积
```

#### **2.5.3 项目交付结构**
```
BankPDF-to-excel/          # 项目根目录
├── src/                         # 源代码
│   ├── core/                    # 核心引擎 (业务逻辑层)
│   ├── components/              # 所有处理组件 (数据处理层)
│   │   ├── extractors/
│   │   ├── cleaners/            # 例如：remove_thousand_separator.py
│   │   └── ...
│   ├── infrastructure/          # 基础设施 (配置管理、日志等)
│   └── launcher.py              # 应用启动器 (应用层)
├── config/                      # 配置与资源层 (全部内容)
│   ├── templates/               # 三层模板
│   ├── banks/
│   ├── rules/
│   └── build_spec.yaml
├── assets/                      # 图标、字体等
├── vendor/                      # 第三方补丁文件 (如兼容性DLL)
├── requirements.frozen.txt      # 在 Python 3.8.10 环境下生成的精确依赖
└── README.md                    # 必须包含“生产兼容性约束”章节
```

## **第三章：核心数据契约与流程**

### **3.1 处理上下文 (`ProcessingContext`)**
```python
# 逻辑定义，实际为类实例
class ProcessingContext:
    # 标识与状态
    session_id: str               # 本次任务唯一ID
    current_state: str            # 对应状态机 (如 “PARSING_TRANSACTIONS”)
    
    # 输入与配置
    source_file_path: Path
    detected_bank_code: str
    bank_config: Dict             # 合并后的完整银行模板
    
    # 中间数据快照 (按状态存储)
    snapshots: Dict[str, Any]     # 例如：snapshots[“Extracted”] = List[TextBlock]
    
    # 输出与报告
    result: ConversionResult
    quality_metrics: Dict
    
    # 错误与通信
    errors: List[SystemError]
    warnings: List[UserWarning]
    event_bus: EventBus           # 用于发布进度事件
```

### **3.2 组件执行流水线**
```
[用户发起请求]
        ↓
[任务控制器] 创建初始化的 `ProcessingContext`
        ↓
[转换引擎] 根据状态机，调用 `PipelineFactory`
        ↓
[PipelineFactory] 读取 `context.bank_config` 中的 `execution_pipeline`
        ↓                       (动态组装)
[工厂] 从 `ComponentRegistry` 查找组件类，实例化，组装成 `Pipeline` 对象
        ↓
[转换引擎] 将 `context` 传入 `Pipeline.execute()`
        ↓
[Pipeline] 按序调用每个 `ProcessingComponent.execute(context)` 
        ↓                       (每个组件读写 `context.snapshots`)
[Pipeline] 执行完毕，`context` 中填充结果数据
        ↓
[转换引擎] 状态机推进，进入下一个阶段（如验证、分类），重复流水线组装过程
        ↓
[任务完成] 生成最终报告，清理临时资源
```

## **第四章：扩展性与维护指南**

### **4.1 如何新增一家银行**
1.  **定位区域模板**：确定新银行所属类别（如`chinese_joint_stock`）。
2.  **创建银行模板**：在`config/banks/`下创建`NEWBANK.yaml`，`inherits_from`区域模板。
3.  **编写差异配置**：在`differences`下仅配置与区域模板不同的部分（如个性字段、专属清洗器）。
4.  **注册专属组件**：如需，在`components/`下开发专属组件类，并用`@ComponentRegistry.register()`装饰器注册。
5.  **测试**：无需修改核心代码，系统自动识别新模板。

### **4.2 如何新增一个通用功能（如新的清洗规则）**
1.  **开发组件**：在`components/cleaners/`下创建新类，实现标准接口。
2.  **注册组件**：用装饰器注册，获得`component_id`。
3.  **配置使用**：在任何银行的模板`execution_pipeline`中，加入该`component_id`即可。

## **第五章：质量保障与交付清单**

### **5.1 最终交付物验证清单**
当运行 `BankPDF-to-excel.exe` 时，必须满足：
- [ ] **环境**：可在全新安装的 **Windows 7 SP1** 及 **Windows 10/11** 上运行，无任何Python环境依赖。
- [ ] **功能**：能正确加载`config/`内所有模板，识别并解析至少3家不同银行的示例流水文件。
- [ ] **完整性**：输出Excel包含交易明细、账户信息、质量报告等多工作表。
- [ ] **性能**：处理一份1000行交易的流水文件，时间在30秒内。
- [ ] **资源**：程序退出后，无残留进程或临时文件。

### **5.2 架构自检清单**
- [ ] **是否所有可变业务逻辑**（分类、校验）都已外部化为`config/rules/`下的YAML文件？
- [ ] **是否所有处理步骤**都已实现为`components/`下的独立`ProcessingComponent`？
- [ ] **是否所有银行共性**都已提炼至`config/templates/regions/`下的区域模板？
- [ ] **打包规范**`build_spec.yaml`中的`compatibility`和`arch`设置是否与目标环境严格一致？

---

**本架构规范 (v4.0) 到此结束。** 它定义了一个从用户交互到最终交付，完全模块化、配置驱动且严格满足生产环境约束的完整系统蓝图。任何遵循此规范中接口定义、数据契约、配置结构和生产约束的实现，都将是一个可维护、可扩展且兼容Windows 7的“智能银行流水转换系统”。