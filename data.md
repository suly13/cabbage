# 菜薹/芥蓝表型系统数据流图 v2（GitHub Mermaid 修正版）

> 当前口径：
> - 输入点云已经去噪、已尺度归一、已按 `Scalar_field` 标注茎/叶/花
> - 阶段 10 / 20 / 30 的叶长 / 叶宽 / 叶面积共用同一流程
> - 参考叶当前统一按 `最大面积叶片` 近似 `最大完整叶`
> - 阶段 10 并行输出子叶大小 / 子叶颜色
> - 抽薹期日期型性状不在本图中
> - 开花期不做颜色

```mermaid
flowchart TD
    A["用户创建样本\nsample_id + stage 10/20/30/40"] --> B["导入数据"]
    B --> B1["点云 pcd/ply\n已去噪 + 已尺度归一 + Scalar_field"]
    B --> B2["照片 jpg/png"]

    B1 --> C["P00 SampleLoader + ScalarFieldReader"]
    B2 --> C

    C --> D["P01 LeafInstanceSeg\n从叶类点云切分到每一片叶"]
    C --> E["P03 MainStemExtractor\n从茎点云提取主薹"]
    C --> F["I00 ImagePreprocess"]

    D --> G["P02 LargestAreaLeafSelector"]
    F --> H["I01 MaskBuilder"]
    H --> I["I02 ColorScorer"]

    subgraph Stage10["阶段 10 幼苗期"]
        G --> T1["LeafMorphologyEngine"]
        H --> T2["子叶 ROI"]
        I --> T3["子叶颜色"]
        T1 --> T4["叶长 / 叶宽 / 叶面积"]
        D --> T5["叶数量"]
        T2 --> T6["子叶大小"]
        C --> T7["基础全株几何"]
    end

    subgraph Stage20["阶段 20 生长期"]
        G --> S1["LeafMorphologyEngine"]
        S1 --> S2["叶长 / 叶宽 / 叶面积"]
        D --> S3["叶数量"]
        I --> S4["叶片颜色"]
        C --> S5["生长习性"]
        C --> S6["全株几何"]
        G --> S7["叶片先端形状 / 叶缘波状 / 叶面泡状"]
    end

    subgraph Stage30["阶段 30 抽薹期"]
        G --> U1["LeafMorphologyEngine"]
        U1 --> U2["叶长 / 叶宽 / 叶面积"]
        D --> U3["叶数量"]
        E --> U4["薹长 / 薹粗"]
        I --> U5["叶片颜色 扩展"]
        C --> U6["生长习性 扩展 / 全株几何"]
    end

    subgraph Stage40["阶段 40 开花期"]
        E --> V1["花茎高度 / 花茎粗度"]
        D --> V2["叶数量"]
        C --> V3["全株几何"]
    end

    T3 --> R["统一结果汇总 ResultWriter"]
    T4 --> R
    T5 --> R
    T6 --> R
    T7 --> R
    S2 --> R
    S3 --> R
    S4 --> R
    S5 --> R
    S6 --> R
    S7 --> R
    U2 --> R
    U3 --> R
    U4 --> R
    U5 --> R
    U6 --> R
    V1 --> R
    V2 --> R
    V3 --> R

    R --> Z["导出 CSV / JSON / UI"]
```
