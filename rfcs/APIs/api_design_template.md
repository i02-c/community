# 标题 paddleo.manim.dataset 设计文档

标题如：paddle.manim.dataset 设计文档
|API名称 | paddle.manim | 
|---|---|
|提交作者<input type="checkbox" class="rowselector hidden"> | 皮鹏 | 
|提交时间<input type="checkbox" class="rowselector hidden"> | 2022-09-12 | 
|版本号 | paddle.manim.V1.0 | 
|依赖飞桨版本<input type="checkbox" class="rowselector hidden"> | paddleV2.0| 
|文件名 | 提交的markdown设计文档文件名称，如：20200913_paddle.manim_design_for_dataset.md<br> | 


# 一、概述
## 1、相关。
为paddle增加gromacs的c接口
## 2、功能目标
实现二阶到三阶的积分计算
实现三节到二阶的微分计算
通过计算能够对gromacs的测试
## 3、意义
为paddle增添gromacs的c接口，填补二阶三阶相应计算空缺。

# 二、飞桨现状
对飞桨框架目前支持此功能的现状调研，
飞桨现有deepmd·kit为paddle.tensor接口提供分子动力学的相关计算，但计算速度和推理效率较GROMACS仍有较大差距
且在模型粒度，gromacs接口拥有较为成熟的体系，为paddle科学计算赋能

# 三、业内方案调研
tensor flow可实现一些较为简单的模型推理，但对于三阶计算，复杂体系无论是模型推理还是泛化能力都略逊一筹
而gromacs拥有更加简易便捷的环境配合超算更强悍的计算性能。

# 五、设计思路与实现方案

## 命名与参数设计
参考：[飞桨API 设计及命名规范](https://www.paddlepaddle.org.cn/documentation/docs/zh/develop/dev_guides/api_contributing_guides/api_design_guidelines_standard_cn.html)
## 底层OP设计、
建立笛卡尔坐标系，时间问题示例如下
class PlotFunctions(GraphScene):
    CONFIG = {
        "x_min" : 0,
        "x_max" : 10.3,
        "x_axis_width": 9,
        "x_tick_frequency": 1,
        "x_labeled_nums" :range(0,11,2),
        "x_axis_label": "$x$",
        "y_min" : 0,
        "y_max" : 2,
        "y_axis_height": 6,
        "y_tick_frequency": 1,
        "y_labeled_nums" :range(0, 3, 1),
        "exclude_zero_label": True,
        "graph_origin" : ORIGIN ,
        "function_color" : RED ,
        "axes_color" : BLUE,
        "graph_origin": 2 * DOWN + 4 * LEFT,
    }   
    def construct(self):
        self.setup_axes(animate=True)
        func_graph=self.get_graph(self.func_to_graph,self.function_color)
        func_graph2=self.get_graph(self.func_to_graph2)
        vert_line = self.get_vertical_line_to_graph(TAU,func_graph,color=YELLOW)
        graph_lab = self.get_graph_label(func_graph, label = "\\cos(x)", x_val=10)
        graph_lab2= self.get_graph_label(func_graph2,label = "\\sin(x)", x_val=-10, direction=UP/2)
        two_pi = TexMobject("x = 2 \\pi")
        label_coord = self.input_to_graph_point(TAU,func_graph)
        two_pi.next_to(label_coord,RIGHT+UP)

        self.play(ShowCreation(func_graph, run_time = 5),ShowCreation(func_graph2, run_time = 5))
        self.play(ShowCreation(vert_line), ShowCreation(graph_lab), ShowCreation(graph_lab2),ShowCreation(two_pi))

    def func_to_graph(self,x):
        return np.cos(x)

    def func_to_graph2(self,x):
        return np.sin(x)
## API实现方案
#pragma once\n\n#ifndef 
  IMAGE_API\n#define 
  IMAGE_API\n\nextern \"C\"\n{\n\t// 
  初始化\n\t__declspec(dllexport) 
 int EnvInit();\n\n\t//图像推理\n\t__declspec(dllexport) 
 int ImageProcess(char* pImgIn, char* pImgOut,int height,int width);\n}\n#endif\n1
gromacs的一些结构文件啥的
以及paddle.IO和cmake的协议啥的输入输出略

#pragma once

#ifndef IMAGE_API
#define IMAGE_API

extern "C"
{
 // 初始化
 __declspec(dllexport) int EnvInit();

 // 图像推理
 __declspec(dllexport) int ImageProcess(char* pImgIn, char* pImgOut,int height,int width);
}
#endif

归一化和聚类分析略
中心回归，均值方差略

# 六、测试和验收的考量
参考：[新增API 测试及验收规范](https://www.paddlepaddle.org.cn/documentation/docs/zh/develop/dev_guides/api_contributing_guides/api_accpetance_criteria_cn.html)
以水分子为例测试不同位置的坐标
# 七、可行性分析和排期规划
时间和开发排期规划，主要milestone
大概得一两个月
1 对三阶计算相关支持，实现gromacs的cmake接口
2 增添一些奇异值分解相关实现
3引入一些复平面轨迹分析？建轴。。略
4还没想到

# 八、影响面
需要进一步讨论的问题，开放性问题，有争议问题；对其他模块是否有影响
开放问题 在模型超算平台上不同的训练和测试策略啥的
paddle.manim是否有更多表现，比如一些酶寻找氢键断裂，或是寻找最小分子结构之类的，或是较为简单的水基化合物那种。。
例如 BKUNYUN BSCC.....和更加厉害的训练策略方案 
长期工作。。。

# 名词解释
略·
# 附件及参考资料
略
