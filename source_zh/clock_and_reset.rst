时钟与复位
==================

时钟
-------------------------------------------------
时钟是触发器、锁存器和存储器中常用的信号。

时钟信号建议使用公认的缩写 **clk** 命名。

当使用多个时钟时，需要添加后缀以区分彼此。

需要提到的是，后缀应该清晰且有意义。

例如：

.. code-block:: systemverilog 

    input  logic    clk_slc    , // 功能后缀，系统级缓存时钟
    input  logic    clk_dmc    , // 功能后缀，DDR 内存控制器时钟
    input  logic    clk_cfg    , // 配置后缀
    input  logic    clk_dbg    , // 调试后缀
    input  logic    ......

此外，关于时钟有一些注意事项。

1. **需要动态调频的时钟应由专用的无毛刺电路处理。它必须是一个通用 IP，不要自己搭建。**
2. **对于时钟，用户直接编写的 "&" 和 "|" 会被综合为有毛刺的逻辑。**



复位
-------------------------------------------------
与时钟类似，复位是触发器、锁存器和存储器中另一个常用信号。

复位信号建议使用公认的缩写 **rst** 命名。

鉴于复位大多为低电平有效，上述缩写优化为 **rst_n**。"_n" 代表低电平有效。

与时钟一样，当使用多个复位时，需要添加后缀。

.. code-block:: systemverilog 

    input  logic    rst_n_slc    , // 功能后缀，系统级缓存复位
    input  logic    rst_n_dmc    , // 功能后缀，DDR 内存控制器复位
    input  logic    rst_n_hwe    , // 调试后缀，调试 IP 复位
    input  logic    rst_n_sw     , // 软件后缀，软件复位
    input  logic    ......

对于复位的触发模式，可以是异步或同步的：

.. code-block:: systemverilog 

    // 异步触发
    always_ff @(posedge clk or negedge rst_n) begin
        if(~rst_n) begin
            ......
        end
        else begin
            ......
        end
    end
    
    // 同步触发
    always_ff @(posedge clk) begin
        if(~rst_n) begin
            ......
        end
        else begin
            ......
        end
    end

**注意：复位应遵循同步释放原则。**
