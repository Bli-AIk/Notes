编辑器拓展笔记
杂项：
1.Editor目录
- Unity特殊目录
- 放在此目录中的脚本、素材都不会最终打包进游戏包体中
- 在Editor目录中也不会打包进游戏包体中
- Editor目录就是Unity提供给开发者用来存放编辑器扩展代码的

2.可以在带有MenuItem静态类的static构造方法中做一些矫正和初始化工作（例如修复可勾选menuitem选项编译后状态异常的问题，见课8后半部分）

3.IMGUI:  即时模式GUI渲染。
EditorWindow可以作为IMGUI的载体之一，IMGUI也有其他的载体。

4.使用region可以在IDE内折叠代码。

```csharp
#region
#endregion
```

5.简易窗体框架思路（工具栏切换窗口内容）
(1)使用Toolbar作为页面切换的入口
(2)使用Enum设置页面Id与名称
(3)使用switch语句检测enum，根据检测执行不同的函数，在不同的函数内具体配置窗口内容。

6.UnityEditor.EditorWindow下的一些方法：
OnEnable()在打开窗口时会执行。

7.编辑器脚本可以引用运行时脚本，反之不可


MenuItem: 编辑器菜单栏拓展
使用方式：
1.直接执行函数
[MenuItem("path")] 作函数头缀，在菜单栏对应路径生成选项，选择后执行函数。

2.打开目录（文件管理器打开）
API: UnityEditor.EditorUtility.ReveallnFinder("path");

3.可勾选的MenuItem（布尔）
API:
Menu.SetChecked("menupath", true/false);
"menupath"需要输入对应的menu路径（相当于1的path）
后一个传入布尔值变量或者值。
实质上是设置了对应menu的“被勾选”状态。

4.MenuItem快捷键

使用方式: 在path后（字符串内）写快捷键编码，如：[MenuItem("path _c")] （设定c键为快捷键）。
快捷键字符前必须有一个空格。写path_c没用。

快捷键可以组合使用，如下。
写%e 就是?Ctrl+e
%#t 就是Ctrl+shift+t
以此类推。

使用#LEFT等字符，可以映射到shift-left。详见官方文档。

自定义快捷键可能会和unity自带的快捷键冲突，冲突会有提示信息。

5.MenuItem可用验证
用人话说就是弄这种效果，达到条件才能点：

使用方式：
(1)先正常的使用[MenuItem("path")] 
(2)再写个方法作为验证方法，但头缀需要写[MenuItem("path", validate = true)]，且该方法需要return bool。（return的值决定能否点击）
这个方法的名称可以与原方法不同。最简洁的区分方式是在原方法名称后加Validate。
如：Origin()     OriginValidate()
（validate即为验证之意）

6.MenuItem的复用
复用，即重复使用相同效果的代码。
API:UnityEditor.EditorApplication.ExecuteMenuItem("Path");
它主要用于无法/不方便直接调用函数的情况，使用该方法后会调用使用对应path的函数。

7.小结：MenuItem在官方文档内有描述，可查阅。


EditorWindow (IMGUI前置准备)
快速入门:
API:
(1)继承 UnityEditor.EditorWindow
(2)使用 GetWindow<WindowName>().Show; 打开编辑器窗口
(3)在 OnGUI() 函数内，使用IMGUI的 API渲染/绘制窗口
总结：继承－打开－绘制

补充：步骤(2)仅包含“打开窗口”这一个步骤。如果需要设置菜单栏，还得使用之前说的MenuItem等方式进行设置。
步骤(3)则是对具体窗口的布局进行绘制，它使用一个单独的函数OnGUI()，其中使用诸如GUILayout.Label("text"); 的方式绘制。

IMGUI（即时模式GUI）：
1.GUILayout常用渲染组件
Unity提供了四种IMGUI渲染api: 
GUILayout, GUI, EditorGUILayout, EditorGUI.
前两个支持运行时，后两个仅编辑器；
Layout支持自动布局，其余需要设置坐标尺寸。

(1)Label：文本标签
示例：GUILayout.Label("text");
效果即显示对应的文本
(2)TextField/TextArea/PasswordField：输入框
输入框需要配合一个string变量使用（进行赋值），以存储输入内容。
示例：
string text;
text = GUILayout.TextField(text);

三者区别：
TextField是单行的文本。
TextArea可以回车，支持多行。
PasswordField类似于密码，输入时会显示掩码，需要额外传入掩码，如text = GUILayout.PasswordField(text,  '*');

如果报错空指针异常，那么初始字符串需要给默认值。PasswordField必须有默认值。

(3)Button/RepeatButton: 按钮
Button会返回布尔值，可以配合if使用。
示例：
if (GUILayout.Button("Button"))
{
Debug.log("Button Clicked");
}

RepeatButton类似于Button，但按下，抬起时都会输出。（Button只在按下时输出）

(4)Horizontal/Vertical: 方向布局
设置方向布局可以调整ui的布局位置，例如把一个Label和一个TextField放在同一行。

示例：横向布局
string text;
GUILayout.BeginHorizontal();//开始横向布局
{
GUILayout.Label("Text:");
text = GUILayout.TextField(text);
}
GUILayout.EndHorizontal();//结束横向布局

该示例中的代码块为提高代码可读性而添加。
此外，可以通过GUILayout.BeginHorizontal("box");在排列的同时设置一些预设风格。"box"会在对应区域的部分添加一个深背景色用以区分。

(5)Box: 自动布局框
示例: GUILayout.Box("box");
box存在一个较深的背景色。

(6)ScrollView：滚动视图
写法类似横向布局/纵向布局，但它需要传入一个Vector2使用。
滚动视图会在显示尺寸不够时允许使用滚轮/右侧ScrollView调节视图。
示例：
Vector2 mScrollPosition;
mScrollPosition = GUILayout.BeginScrollView(mScrollPosition);
{
...
}
GUILayout.EndScrollView();

(7)HorizontalSlider/VerticalSlider: 滑动条
会且仅会绘制一个横向/纵向滑动条。
示例（value后的0是最小值，1是最大值，可改）：
float value;
value = GUILayout.HorizontalSlider(value, 0, 1);

(8)Area: GUI区域
当绘制非自动布局（非Layout）的内容时，可以配合Area使用。
示例：
GUILayout.BeginArea(new Rect(0, 0, 100, 100));
{
GUI.Label(new Rect(0, 0, 20, 20), "text");
}
GUILayout.EndArea();

(9)Window: 窗口
仅能在游戏运行时内渲染
示例：GUILayout.Window(1, new Rect(0, 0, 100, 100), id =>
{

}, "text");
其中，第一个传入的数据是id（1）

(10)Toolbar: 工具栏
工具栏需要一个当前选中的索引值（int），还需要一个字符串数组。
工具栏显示的效果就是若干个按钮排成一行，可以选中其一。
示例：
int index;
index = GUILayout.Toolbar(index, new []{"1","2","3","4","5"});

(11)Toggle: 打开/关闭的开关按钮
效果就是一个可以勾选的选项。
示例：
bool toogle;
toogle = GUILayout.Toggle(Toggle, "Text");

(12)Space/FlexibleSpace: 空白
就是插入空白。
示例：
GUILayout.Label("text1");
GUILayout.Space(10f);//默认10像素，可自定义
GUILayout.Label("text2");

FlexibleSpace可用于横向布局中，插入在两个组件之间，会把它们分别挤压至左侧/右侧。

(13)Width/Height/MinWidth/MinHeight/MaxWidth/MaxHeight: 宽高控制
可和FlexibleSpace配合使用。
示例：
GUILayout.Button("Button", GUILayout.Width(150), GUILayout.Height(150));

Min/Max设置的是最小/最大范围，主要作用于窗口拉伸时的显示。

(14)SelectionGrid: 选择网格
需要传入一个selectedGrid(int)，一个字符串数组（也可图片），横向数量（int）
会绘制网格状的选项列表，类似于加了换行的工具栏，根据横向数量换行。
示例：
int selectedGrid;
selectedGrid = GUILayout.SelectionGrid(selectedGrid, new []{"1","2","3","4","5"}, 2);

2.一些影响GUILayout渲染的API（可查阅相关文档）
(1)GUI.enabled 是否开启交互 需要布尔赋值
GUI.enabled 的禁用范围是从你将其设置为 false 开始的行，到你再次将其设置为 true 的行。

(2)GUIUtility.RotateAroundPivot(角度float, 轴点Vector2)
根据坐标轴旋转接下来的GUI组件
轴点以左上角为原点，右x下y递增

(3)GUIUtility.ScaleAroundPivot(Scale Vector2, 轴点Vector2)
根据一个点进行缩放

(4)GUI.color
设置颜色。直接赋值颜色即可。

3.GUI常用API
总体上类似GUILayout，但是需要指定尺寸和位置。
通过Rect(x, y, width, height)进行设置。坐标以左上角为原点，x向右，y向下。尺寸设置width与height。
一些API，例如ScrollView，在使用时要注意计算区域的坐标长宽。

此外还有一个GUI Group:
GUI.BeginGroup(rect, "text");
...
GUI.EndGroup();

4.运行时（Runtime）载体
在MonoBehaviour的OnGUI()方法中绘制。
在游戏（game）视图内显示。
运行时载体可以渲染窗口。
运行时仅支持GUI与GUILayout。

5.EditorGUI
常用API较多，详见API手册。
(1)EditorGUI.LabelField(rect, "text"); 
只读文本

(2)DisabledGroup 可禁用控件
效果是使某些控件无法更改（灰掉）
示例：
EditorGUI.BeginDisabledGroup(bool);
{
...
} 
EditorGUI.EndDisabledGroup();

(3)Toggle 类似于GUI的Toggle。
ToggleLeft 将Toggle的对勾框渲染在字符左侧。

(4)TextField  TextArea PasswordField文本输入框
在unity2018版本，只有Editor的输入框可以进行复制粘贴，输入中文等操作。
PasswordField不用设置掩码，默认为“*”

(5)Button 按钮
一般来说用GUI的Button即可，但在Editor中，还提供了DropdownButton和LinkButton。
DropdownButton：对鼠标按下做出反应，显示下拉菜单。（类似菜单栏的按钮）
LinkButton：类似超链接的按钮。

(6)HelpBox
可以设置信息的类型，输出内容带图标，类似于控制台传出信息的样式。

(7)ColorField 输入颜色

(8)BoundsField 输入Bound，编辑Center与Extent。(均为Vector3)
BoundsIntField 同理，不过是BoundInt

(9)CurveField 曲线，使用AnimationCurve类型，需要默认值

(10)DelayedDoubleField

()BeginProperty 属性封装器
