时序逻辑
==================

在 SystemVerilog 中，引入了关键字 **always_ff** 来表示时序逻辑。

推荐使用 **always_ff** 来表达时序逻辑。

**此外，时序过程块中只能使用非阻塞赋值。**

例如：

.. code-block:: systemverilog 

    // 使用异步复位的数据路径翻转
    always_ff @(posedge clk or negedge rst_n) begin
        if(~rst_n)
            data_d <= {DATA_WIDTH{1'b0}} ;
        else if(data_en)
            data_d <= data ;
    end
    
    
对于 **always_ff** 过程块，建议省略最后的 **else**。

这样，当有三个或四个以上的寄存器由同一个使能信号驱动时，EDA 工具会自动插入/推断时钟门控单元。

然而，如果你写了最后的 **else** 分支（data_d <= data_d），一些工具也能插入/推断。但我们仍然建议省略它。

对于时序逻辑中的复位，请注意：

1. **如果需要同步复位，请在 "always_ff" 行中省略 "negedge rst_n"。** 例如：

.. code-block:: systemverilog 

    // 使用同步复位的数据路径翻转
    always_ff @(posedge clk) begin
        if(~rst_n)
            data_d <= {DATA_WIDTH{1'b0}} ;
        else if(data_en)
            data_d <= data ;
    end
 
2. **在时序逻辑中，复位不是必须的，在数据路径中可以省略以获得更高速度和更小面积。** 例如：

.. code-block:: systemverilog 

    // 不带复位的数据路径翻转
    always_ff @(posedge clk) begin
        if(data_en)
            data_d <= data ;
    end
