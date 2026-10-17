## 一、渲染流水线（Render Pipeline）

### 1.1 渲染流水线概述

##### 概念
- **是什么**：渲染流水线是React Native将React逻辑代码转换为屏幕上原生视图的一系列有序加工处理过程，包括**渲染（Render）**、**提交（Commit）**和**挂载（Mount）**三大阶段。
- **为什么需要**：React代码是声明式的组件树，而原生平台需要的是真实的视图树和布局坐标。渲染流水线作为桥梁，负责完成从"JSX描述"到"原生像素"的转换。
- **解决什么问题**：
  - 将React的虚拟DOM转化为平台特定的原生视图（UIView/ViewGroup）
  - 在多线程环境中保证数据一致性和线程安全
  - 通过批量更新和差异计算优化渲染性能

##### 原理
- **底层实现**：渲染流水线由C++核心实现（新架构Fabric），通过JSI（JavaScript Interface）与JavaScript层通信，通过平台适配层与iOS/Android原生层交互。
- **工作流程**：
  1. **渲染阶段**：在JavaScript线程执行React组件逻辑，生成React元素树，同时同步在C++层创建对应的影子节点，组装成影子树
  2. **提交阶段**：在后台线程执行，包含两个子操作：
     - **布局计算**：调用Yoga引擎计算每个影子节点的位置和尺寸
     - **树提升**：将新影子树标记为"下一棵待挂载的树"
  3. **挂载阶段**：在UI线程执行，包含三个子步骤：
     - **树对比**：计算新树与旧树的差异，生成原子变更操作列表
     - **树提升**：将"下一棵树"提升为"已渲染树"
     - **视图挂载**：在原生平台执行变更操作，创建/更新/删除原生视图

- **关键技术点**：
  - **线程隔离**：三个阶段可在不同线程执行，UI线程只负责最终的视图挂载
  - **不可变性**：影子树不可变，更新时通过克隆创建新树
  - **批量操作**：通过树对比将多次更新聚合成原子操作列表，减少UI线程调用

##### 使用方法
- **基本用法**：开发者正常编写React组件，渲染流水线自动执行，无需手动干预
- **常见场景**：
  - 首次渲染应用
  - 组件状态更新（setState）
  - 屏幕旋转或窗口大小变化
- **最佳实践**：
  - 合理拆分组件，避免过深的组件嵌套
  - 使用React.memo或PureComponent减少不必要的重渲染
  - 避免在渲染阶段执行耗时操作

##### 注意事项
- **常见错误**：在渲染阶段执行副作用操作（应在useEffect中执行）
- **性能问题**：
  - 过深的组件树会增加影子节点数量，影响树对比性能
  - 频繁的状态更新会导致频繁的克隆操作
- **兼容性**：新架构（Fabric）正在积极推广中，部分旧版API可能存在差异

##### 代码示例
```jsx
// 开发者编写的React组件
function MyComponent() {
  const [color, setColor] = useState('red');
  
  return (
    <View style={{ margin: 10 }}>
      <View 
        style={{ 
          backgroundColor: color, 
          width: 100, 
          height: 100 
        }} 
        onPress={() => setColor('blue')}
      />
      <Text>Hello World</Text>
    </View>
  );
}

// 渲染流水线自动处理：
// 1. 渲染阶段：创建React元素树 → 创建影子树（ViewShadowNode, TextShadowNode）
// 2. 提交阶段：Yoga计算布局（计算margin、width、height等）
// 3. 挂载阶段：创建UIView/ViewGroup，设置frame和属性，添加子视图
```


### 1.2 初始渲染流程

##### 概念
- **是什么**：应用首次启动或全新屏幕打开时，从零开始创建完整UI的渲染过程。
- **为什么需要**：建立UI的初始状态，将组件树映射为屏幕上的可视元素。
- **解决什么问题**：完成从无到有的UI构建，建立影子树与原生视图的对应关系。

##### 原理
- **底层实现**：React递归调用组件函数或render方法，直至所有复合组件被解析为宿主组件（如View、Text）。
- **工作流程**：
  1. React执行组件代码，生成React宿主组件元素树
  2. 每创建一个宿主组件元素，同步创建对应的影子节点
  3. 根据元素父子关系建立影子节点间的父子关系，形成完整的影子树
  4. 提交阶段进行布局计算
  5. 挂载阶段进行树对比（此时旧树为空），生成全量创建视图的变更操作
- **关键技术点**：
  - **同步创建**：影子节点的创建与React元素树的生成同步进行，确保数据一致性
  - **复合组件跳过**：只有宿主组件（如View、Text）才会创建影子节点，复合组件（如MyComponent）不直接对应影子节点
  - **首次挂载优化**：初始渲染时"先前渲染的树"为空，树对比直接生成创建所有视图的操作

##### 注意事项
- **性能考量**：初始渲染需要创建完整的视图层级，避免在根组件执行过重的同步计算
- **数据准备**：确保初始渲染所需的数据已提前加载

##### 代码示例
```jsx
// 初始渲染过程示意
function App() {
  return (
    <View>                    // ← 创建 ViewShadowNode (Node 1)
      <Text>Hello</Text>      // ← 创建 TextShadowNode (Node 2)
    </View>
  );
}

// 生成的影子树结构：
// Node 1 (ViewShadowNode)
//   └── Node 2 (TextShadowNode)

// 挂载阶段生成的原子操作：
// [
//   CreateView(Node 1, { ...props }),
//   CreateView(Node 2, { text: "Hello" }),
//   InsertView(Node 1, Node 2)
// ]
```


### 1.3 状态更新流程

##### 概念
- **是什么**：当组件state或props发生变化时，更新已有UI的渲染过程。
- **为什么需要**：响应用户交互或数据变化，更新屏幕显示。
- **解决什么问题**：在保证线程安全的前提下，高效地更新部分UI，避免全量重绘。

##### 原理
- **底层实现**：基于不可变数据和结构共享，仅克隆受影响的节点路径。
- **工作流程**：
  1. **渲染阶段**：
     - React创建包含新状态的React元素树
     - 克隆受变更影响的每个React元素和影子节点（从变更节点到根节点的路径）
     - 未变化的节点在新旧树之间共享
  2. **提交阶段**：
     - 布局计算（可能因父节点布局变化导致共享节点被克隆）
     - 树提升（新树 → 下一棵树）
  3. **挂载阶段**：
     - 树对比：计算旧树(T)与新树(T')的差异
     - 生成最小化的原子变更操作（如UpdateView）
     - 视图挂载：在UI线程执行变更操作
- **关键技术点**：
  - **结构共享**：未变化的子树在新旧树间共享，减少内存和计算开销
  - **最小化更新**：树对比只生成必要的变更操作，避免更新未变化视图
  - **可中断渲染**：渲染阶段可被高优先级事件中断，优先响应用户输入

##### 代码示例
```jsx
// 初始渲染
function MyComponent() {
  return (
    <View>                    // Node 1
      <View                   // Node 2
        style={{backgroundColor: 'red'}}  // Node 3
      />
      <View                   // Node 4
        style={{backgroundColor: 'blue'}} // 未变化
      />
    </View>
  );
}

// 状态更新：将Node 3的背景从'red'改为'yellow'
// 渲染阶段执行的克隆操作：
// CloneNode(Node 3, {backgroundColor: 'yellow'}) → Node 3'
// CloneNode(Node 2) → Node 2'
// AppendChild(Node 2', Node 3')
// AppendChild(Node 2', Node 4)  // Node 4 被共享，未克隆
// CloneNode(Node 1) → Node 1'
// AppendChild(Node 1', Node 2')

// 挂载阶段生成的原子操作：
// [UpdateView(Node 3, {backgroundColor: 'yellow'})]
```


## 二、Yoga布局引擎

### 2.1 Yoga引擎概述

##### 概念
- **是什么**：Yoga是一个用C++编写的高性能、跨平台布局引擎，实现了CSS Flexbox规范，是React Native的布局计算核心。
- **为什么需要**：
  - 跨平台一致性：同一套Flexbox样式在iOS和Android上产生相同的布局结果
  - 性能要求：布局计算频繁发生，需要高效算法
  - 平台解耦：布局逻辑与原生渲染分离，便于维护和优化
- **解决什么问题**：将Flexbox样式转换为每个组件在屏幕上的精确坐标和尺寸（x, y, width, height）。

##### 原理
- **底层实现**：Yoga维护一棵由`yoga::Node`构成的节点树，每个节点存储样式输入（style_）和布局输出（layout_）。
- **工作流程**：
  1. **输入接收**：从影子节点接收Flexbox样式属性（flexDirection、justifyContent、margin等）
  2. **约束传入**：接收根节点的布局约束（可用宽度/高度）
  3. **递归计算**：从根节点开始，递归计算每个节点的位置和尺寸
  4. **缓存利用**：检查缓存，相同约束条件下直接返回上次计算结果
  5. **平台回调**：对于Text等特殊组件，调用平台特定测量函数
  6. **结果输出**：返回每个节点的最终布局信息（x, y, width, height）
- **关键技术点**：
  - **多级缓存**：每个节点缓存最多8个历史测量结果，避免重复计算
  - **像素网格对齐**：根据设备像素密度（pointScaleFactor）将坐标对齐到像素网格，防止模糊
  - **两遍算法**：处理flex-grow/flex-shrink时采用两遍分布算法，处理min/max约束

##### 使用方法
- **基本用法**：通过React Native样式系统间接使用，无需直接调用Yoga API
- **常见场景**：所有需要布局计算的组件（View、Text、Image等）
- **最佳实践**：
  - 优先使用Flexbox布局而非绝对定位
  - 避免动态计算样式，尽量使用静态样式
  - 合理使用minHeight/maxHeight等约束属性

##### 注意事项
- **性能问题**：过度复杂的嵌套Flexbox可能导致计算复杂度增加
- **兼容性**：Yoga实现的Flexbox与Web CSS Flexbox存在细微差异
- **内存占用**：大量动态样式变化会导致缓存失效，增加计算开销

##### 代码示例
```jsx
// Yoga通过样式系统间接使用
<View style={{
  flexDirection: 'row',        // Yoga处理主轴方向
  justifyContent: 'center',    // Yoga处理主轴对齐
  alignItems: 'stretch',       // Yoga处理交叉轴对齐
  margin: 10,                  // Yoga处理外边距
  padding: 15,                 // Yoga处理内边距
  width: '50%',                // Yoga处理百分比宽度
  aspectRatio: 16/9            // Yoga处理宽高比
}}>
  {/* 子组件 */}
</View>

// Yoga最终输出的布局信息（示意）
// {
//   left: 10,
//   top: 10,
//   width: 375,
//   height: 210.94
// }
```


### 2.2 Yoga核心数据结构

##### 概念
- **是什么**：Yoga内部用于存储样式输入和布局输出的数据结构和组织方式。
- **为什么需要**：高效地存储和处理大量节点的样式信息和计算结果。
- **解决什么问题**：提供布局计算所需的数据基础，支持缓存和克隆操作。

##### 原理
- **底层实现**：
  - **yoga::Node**：核心节点结构，包含：
    - `style_`：样式输入（flex属性、尺寸、边距等）
    - `layout_`：布局输出（位置、尺寸）
    - `children_`：子节点列表
    - `owner_`：父节点
    - `measureFunc_`：自定义测量函数（用于Text等）
    - `config_`：全局配置（像素密度等）
  - **yoga::Style**：存储所有Flexbox相关属性，通过StyleValuePool优化内存
  - **yoga::LayoutResults**：存储计算结果，包含8个槽位的测量缓存
- **工作流程**：
  1. 影子节点创建时，同时创建对应的yoga::Node
  2. 样式通过setter方法传递到style_
  3. 布局计算后将结果存入layout_
  4. 缓存命中时直接从cachedMeasurements读取

##### 代码示例
```cpp
// Yoga节点结构示意（C++伪代码）
class Node {
  Style style_;                    // 样式输入
  LayoutResults layout_;            // 布局输出
  std::vector<Node*> children_;     // 子节点
  Node* owner_;                     // 父节点
  YGMeasureFunc measureFunc_;       // 测量回调
  const Config* config_;            // 全局配置
};

class LayoutResults {
  float dimensions_[2];             // 最终宽高
  float measuredDimensions_[2];     // 测量宽高（未舍入）
  float position_[4];               // left, top, right, bottom
  CachedMeasurement cachedMeasurements_[8]; // 缓存槽
};
```


### 2.3 Flexbox算法实现

##### 概念
- **是什么**：Yoga对CSS Flexbox规范的完整C++实现，包括主轴/交叉轴布局、剩余空间分配、对齐方式等。
- **为什么需要**：将声明式的Flexbox样式转换为确定性的布局计算结果。
- **解决什么问题**：处理复杂的布局场景，如多行flex-wrap、剩余空间分配、min/max约束等。

##### 原理
- **关键技术点**：
  - **Flex Basis计算**：确定项目在主轴上的初始大小，优先级：flex-basis > width/height > 内容固有尺寸
  - **Flex Line计算**：对于多行容器，决定项目如何分配到不同行，记录每行的总flex-grow/flex-shrink因子
  - **两遍分配算法**：
    - 第一遍：识别违反min/max约束的项目，冻结其尺寸
    - 第二遍：在剩余弹性项目间按比例分配剩余空间
  - **主轴对齐**：根据justifyContent属性（flex-start、center、space-between等）定位项目
  - **交叉轴对齐**：根据alignItems/alignSelf处理单行内对齐，根据alignContent处理多行间分布
  - **绝对定位**：相对于包含块（不一定是直接父节点）定位，支持inset属性

##### 代码示例
```jsx
// 复杂Flexbox布局示例
<View style={{
  flexDirection: 'row',
  flexWrap: 'wrap',
  justifyContent: 'space-between',
  alignItems: 'center',
  height: 300
}}>
  <View style={{ width: 100, height: 50 }} />     // 固定尺寸
  <View style={{ flex: 1, height: 50 }} />        // 弹性项目
  <View style={{ flex: 2, minWidth: 150 }} />     // 弹性项目带约束
</View>

// Yoga计算过程：
// 1. 确定可用宽度 = 父容器宽度
// 2. 计算flex basis：第一个固定100，后两个flex:1和flex:2
// 3. 检查换行：若总宽度超出，则换行处理
// 4. 第一遍分配：检查minWidth约束
// 5. 第二遍分配：剩余空间按flex比例分配
// 6. 交叉轴对齐：所有项目在行内垂直居中
```


## 三、影子树（Shadow Tree）

### 3.1 影子树概述

##### 概念
- **是什么**：影子树是位于C++层的轻量级数据结构树，由影子节点（Shadow Node）组成，与React元素树中的宿主组件一一对应。
- **为什么需要**：
  - **性能考虑**：直接操作原生视图昂贵，影子树作为轻量级代理，在后台高效计算
  - **线程安全**：通过不可变数据保证多线程环境下的安全访问
  - **批量更新**：通过树对比将多次更新聚合成原子操作
- **解决什么问题**：作为JavaScript层和原生平台层之间的"隔离层"和"协调者"，解耦JS和原生世界。

##### 原理
- **底层实现**：C++对象，每个宿主组件对应一个影子节点（如ViewShadowNode、TextShadowNode）。
- **工作流程**：
  1. **创建**：在渲染阶段，React每创建一个宿主组件元素，渲染器同步创建对应的影子节点
  2. **组装**：根据元素间的父子关系，建立影子节点间的父子关系
  3. **关联Yoga**：每个非虚拟影子节点内部关联一个Yoga节点，样式传递给Yoga
  4. **布局**：提交阶段，Yoga计算后，布局结果写回影子节点
  5. **挂载**：挂载阶段，影子节点的信息用于创建/更新原生视图
- **关键技术点**：
  - **不可变性**：影子树不可变，更新时通过克隆创建新树，保证线程安全
  - **JSI指针**：React Fiber节点通过JSI持有指向影子节点的C++指针
  - **结构共享**：更新时仅克隆受影响节点路径，未变化子树共享

##### 使用方法
- **基本用法**：开发者编写React组件，影子树自动维护，无需直接操作
- **常见场景**：所有UI渲染场景
- **最佳实践**：
  - 理解仅布局节点优化，避免不必要的包裹View
  - 使用React DevTools查看组件树，优化层级

##### 注意事项
- **内存占用**：大型应用影子节点可达600-1000个（扁平化前）
- **调试难度**：影子树在C++层，调试需要原生开发工具
- **版本差异**：新架构（Fabric）中影子树实现有较大变化

##### 代码示例
```jsx
// React组件
function App() {
  return (
    <View>                    // ← ViewShadowNode
      <Text>Hello</Text>      // ← TextShadowNode
    </View>
  );
}

// 影子树结构（C++层）
// class ViewShadowNode : public ShadowNode {
//   YogaNode yogaNode_;
//   Props props_;
// };
// 
// class TextShadowNode : public ShadowNode {
//   YogaNode yogaNode_;
//   Props props_;
//   std::string text_;
// };
```


### 3.2 影子节点类型

##### 概念
- **是什么**：影子树中包含不同类型的节点，以适应不同的组件特性和优化需求。
- **为什么需要**：并非所有React组件都需要对应一个原生视图，通过节点类型区分实现性能优化。
- **解决什么问题**：减少原生视图数量，优化渲染性能。

##### 原理
- **节点类型**：
  | 节点类型 | 描述 | 平台实现 | 示例 |
  |---------|------|---------|------|
  | **常规影子节点** | 对应一个会在屏幕上创建原生视图的组件 | RCTShadowView(iOS), ReactShadowNodeImpl(Android) | View、Image |
  | **虚拟节点** | 没有直接原生视图对应，只为布局或逻辑存在 | RCTVirtualTextShadowView | Text组件中的嵌套Text |
  | **仅布局节点** | 只起布局容器作用，无背景色/事件等原生视图必需属性 | 通过isLayoutOnly标志识别 | 纯容器View |
  | **根影子节点** | 布局计算的入口节点 | RCTRootShadowView | 应用根视图 |
- **工作流程**：
  1. 创建影子节点时根据组件类型和属性确定节点类型
  2. 挂载阶段的树对比中，仅布局节点被扁平化，子视图直接挂载到父视图
  3. 虚拟节点不参与原生视图创建

##### 代码示例
```jsx
// 仅布局节点示例
function MyComponent() {
  return (
    <View>                          // 根节点（常规节点）
      <View style={{margin: 10}}>   // 仅布局节点（无背景色、无事件）
        <View style={{margin: 5}}>  // 仅布局节点
          <Text>Hello</Text>        // 常规节点
        </View>
      </View>
    </View>
  );
}

// 扁平化后，仅布局节点不创建原生视图
// 实际原生视图层级：
// RootView
//   └── TextView (直接作为RootView的子视图，但位置受margin影响)
```


### 3.3 影子树与Yoga的集成

##### 概念
- **是什么**：影子节点与Yoga节点的关联方式和协作机制。
- **为什么需要**：影子树负责管理组件结构和属性，Yoga负责布局计算，两者需要紧密集成。
- **解决什么问题**：将样式从JS传递到布局引擎，并将计算结果传递回渲染系统。

##### 原理
- **底层实现**：每个非虚拟影子节点内部拥有一个Yoga节点（yoga::Node）。
- **工作流程**：
  1. **样式传递**：当组件样式变化时，影子节点调用setter方法（setPadding、setFlexDirection等）将样式值传递给Yoga节点
  2. **布局触发**：提交阶段，影子树根节点调用Yoga的calculateLayout()入口函数
  3. **递归计算**：Yoga遍历其节点树进行布局计算
  4. **结果回传**：Yoga计算完成后，每个影子节点从对应的Yoga节点读取布局结果（LayoutMetrics）
  5. **布局应用**：布局结果用于挂载阶段的视图创建和更新
- **关键技术点**：
  - **YogaLayoutableShadowNode**：负责样式转换和结果传递的桥梁类
  - **applyAliasedProps**：处理CSS逻辑属性（如marginInlineStart → marginStart）
  - **layoutMetricsFromYogaNode**：将Yoga结果转换为React Native的LayoutMetrics

##### 代码示例
```cpp
// 影子节点与Yoga节点集成示意
class YogaLayoutableShadowNode : public ShadowNode {
  yoga::Node yogaNode_;
  
  void setPadding(Edge edge, Float value) {
    yogaNode_.setPadding(edge, value);
  }
  
  LayoutMetrics getLayoutMetrics() {
    LayoutMetrics metrics;
    metrics.frame.size.width = yogaNode_.getLayout().dimensions[0];
    metrics.frame.size.height = yogaNode_.getLayout().dimensions[1];
    metrics.frame.origin.x = yogaNode_.getLayout().position[0];
    metrics.frame.origin.y = yogaNode_.getLayout().position[1];
    return metrics;
  }
};
```


## 四、线程模型（Threading Model）

### 4.1 核心线程

##### 概念
- **是什么**：React Native渲染器将渲染管线的工作分配给多个线程执行的多线程架构。
- **为什么需要**：将耗时计算从UI线程移出，保证UI流畅性；通过线程隔离实现并发处理。
- **解决什么问题**：避免JavaScript执行和布局计算阻塞UI渲染，保持60fps的流畅体验。

##### 原理
- **底层实现**：基于C++的线程安全设计，通过不可变数据结构保证线程安全。
- **核心线程**：
  | 线程 | 职责 | 可执行阶段 |
  |------|------|-----------|
  | **UI线程（主线程）** | 唯一可以操作和创建宿主视图的线程 | 挂载阶段（视图挂载） |
  | **JavaScript线程** | React代码执行、React元素树创建 | 渲染阶段、部分提交阶段 |
  | **后台线程** | 布局计算、树对比 | 提交阶段（布局计算、树对比） |
- **关键技术点**：
  - **const正确性**：C++强制不可变性，保证多线程读安全
  - **线程切换**：渲染器根据场景在不同线程间调度任务
  - **可中断渲染**：渲染阶段可被高优先级事件中断

##### 注意事项
- **线程阻塞**：长时间JS执行会阻塞渲染阶段，导致掉帧
- **UI线程负担**：过多的视图创建/更新会加重UI线程负担

##### 代码示例
```jsx
// 渲染阶段在JS线程执行
// 提交阶段可能在后台线程执行
// 挂载阶段在UI线程执行

// 高优先级事件（如用户点击）可中断渲染
function handlePress() {
  // 此事件在UI线程触发
  // 可中断正在进行的渲染，优先处理点击
  setState(newState);
}
```


### 4.2 渲染场景与中断机制

##### 概念
- **是什么**：根据事件优先级不同，渲染管线可在不同线程执行，并可被中断恢复的机制。
- **为什么需要**：优先响应用户输入，保证交互流畅性。
- **解决什么问题**：在保证数据一致性的前提下，允许高优先级事件抢占CPU资源。

##### 原理
- **渲染场景**：
  1. **JS线程渲染**（最常见）：大部分渲染管线在JS线程执行
  2. **UI线程渲染**：UI线程高优先级事件触发时，渲染管线同步在UI线程执行
  3. **默认中断**：低优先级事件在UI线程执行时，可中断渲染，合并状态后继续在JS线程执行
  4. **离散事件中断**：高优先级事件中断渲染，合并状态后同步在UI线程执行
  5. **C++状态更新**：从UI线程发起的更新，跳过渲染阶段
- **关键技术点**：
  - **中断恢复**：渲染阶段可中断，中断后合并新状态再恢复
  - **优先级调度**：根据事件类型决定渲染执行的线程和同步/异步方式

##### 代码示例
```jsx
// 离散事件中断示意
function ScrollView() {
  const [items, setItems] = useState([...]);
  
  // 滚动事件（高优先级）
  const onScroll = useCallback(() => {
    // UI线程触发，可中断正在进行的渲染
    setScrollPosition(event.nativeEvent.contentOffset.y);
  }, []);
  
  // 数据加载（低优先级）
  const loadMore = useCallback(() => {
    // 后台加载，不影响UI
    fetchMoreData().then(newItems => {
      setItems([...items, ...newItems]); // 可能被中断
    });
  }, [items]);
  
  return <FlatList onScroll={onScroll} data={items} />;
}
```


## 五、视图扁平化（View Flattening）

### 5.1 视图扁平化机制

##### 概念
- **是什么**：React Native渲染器的一项优化技术，在挂载阶段的树对比过程中，将"仅布局节点"合并或展平，减少实际创建的原生视图数量。
- **为什么需要**：React的声明式组件模型容易产生过深的仅布局节点嵌套，每个节点如果都创建原生视图会严重影响性能。
- **解决什么问题**：减少原生视图层级深度，降低内存占用和UI线程负担，提升渲染性能。

##### 原理
- **底层实现**：集成在渲染器的树对比（Tree Diffing）阶段，用C++实现。
- **工作流程**：
  1. **识别**：在树对比过程中识别仅布局节点（仅有布局样式，无背景色、无事件、无opacity等）
  2. **合并**：将仅布局节点的样式合并到其父节点或子节点
  3. **扁平化**：不为仅布局节点创建原生视图，其子视图直接挂载到父视图下
  4. **结果**：原生视图层级比影子树层级浅
- **关键技术点**：
  - **判定条件**：节点是否仅有margin、padding、position等布局属性，无backgroundColor、opacity、onPress等
  - **生产测试**：影子节点从600-1000个减少到约200个原生视图
  - **视觉不变**：扁平化后屏幕视觉效果不变

##### 使用方法
- **基本用法**：自动生效，无需手动配置
- **最佳实践**：
  - 避免不必要的View包装，尽量使用Fragment（<></>）
  - 如果只需要布局容器，不要添加背景色、圆角等装饰属性
  - 使用StyleSheet.create创建样式，便于优化

##### 注意事项
- **调试注意**：原生视图层级少于组件层级，可能影响原生调试工具中的视图查找
- **限制条件**：某些属性（如zIndex、opacity、elevation）会阻止扁平化

##### 代码示例
```jsx
// 优化前：3个原生视图
function Unoptimized() {
  return (
    <View style={{margin: 10}}>           // 仅布局节点 → 会被扁平化
      <View style={{padding: 5}}>         // 仅布局节点 → 会被扁平化
        <Text>Hello World</Text>          // 常规节点
      </View>
    </View>
  );
}
// 实际原生视图：RootView + TextView

// 优化后：2个原生视图（如果一定要用View包裹）
function Optimized() {
  return (
    <>
      <View style={{margin: 10, padding: 5}}> // 合并样式
        <Text>Hello World</Text>
      </View>
    </>
  );
}
// 实际原生视图：RootView + TextView（但TextView位置受margin/padding影响）

// 阻止扁平化的场景
function WithDecoration() {
  return (
    <View style={{margin: 10}}>           // 有backgroundColor，不会被扁平化
      <View style={{                     
        padding: 5,
        backgroundColor: 'red'            // 装饰属性，不会被扁平化
      }}>
        <Text>Hello World</Text>
      </View>
    </View>
  );
}
// 实际原生视图：RootView + View(backgroundColor) + TextView
```


## 六、结构共享（Structural Sharing）

### 6.1 结构共享机制

##### 概念
- **是什么**：在状态更新时，React Native渲染器通过克隆受影响节点路径，并让新旧树共享未变化子树的优化技术。
- **为什么需要**：影子树不可变，每次更新都需要创建新树，如果全量复制会导致巨大内存和性能开销。
- **解决什么问题**：在保证不可变性的前提下，最小化内存分配和计算开销。

##### 原理
- **底层实现**：基于引用共享的树形数据结构，克隆操作只复制路径节点。
- **工作流程**：
  1. **变更识别**：确定哪个节点因状态更新需要变化
  2. **路径克隆**：克隆从变更节点到根节点的整条路径上的所有节点
  3. **共享子树**：未变化的子树在新旧树间共享（引用相同）
  4. **新树组装**：将克隆的路径节点与共享的子节点组装成新树
- **关键技术点**：
  - **克隆操作**：CloneNode创建节点的浅拷贝，保留对未变化子节点的引用
  - **写时复制**：只有真正需要修改的节点才创建新副本
  - **引用计数**：C++通过智能指针管理共享节点的生命周期

##### 代码示例
```jsx
// 初始树 T
// Node 1 (root)
// ├── Node 2
// │   ├── Node 3 (将变更)
// │   └── Node 4 (不变)
// └── Node 5 (不变)

// 状态更新：修改Node 3
// 克隆操作：
// CloneNode(Node 3, newProps) → Node 3'
// CloneNode(Node 2) → Node 2'（因为子节点变化）
// AppendChild(Node 2', Node 3')
// AppendChild(Node 2', Node 4)  // Node 4 共享，未克隆
// CloneNode(Node 1) → Node 1'
// AppendChild(Node 1', Node 2')
// AppendChild(Node 1', Node 5)  // Node 5 共享，未克隆

// 新树 T'
// Node 1' (新克隆)
// ├── Node 2' (新克隆)
// │   ├── Node 3' (新克隆，有新props)
// │   └── Node 4 (共享，来自旧树)
// └── Node 5 (共享，来自旧树)
```

---

## 参考资料

1. React Native官方文档 - Render, Commit, and Mount 
2. React Native官方文档 - Threading Model 
3. React Native官方文档 - View Flattening 
4. React Native中文网 - 渲染、提交和挂载 
5. DeepWiki - Yoga Layout Engine 
6. DeepWiki - Shadow Tree and Layout 