结构体
==========

在 SystemVerilog 中，引入了 struct 来将一些信号打包成一个整体信号。

- **优点** 

1. 可以显著减少代码长度。

2. 提高代码可读性。

3. 显著降低代码维护成本。

- **定义** 

struct 的定义如下所示：

.. code-block:: systemverilog 

    // AXI AW 通道
    typedef struct packed {
        logic [AXI_AW_ID_WIDTH-1:0]      id    ;
        logic [AXI_AW_ADDR_WIDTH-1:0]    addr  ;
        logic [AXI_AW_LEN_WIDTH-1:0]     len   ;
        logic [AXI_AW_SIZE_WIDTH-1:0]    size  ;
        logic [1:0]                      burst ;
        logic                            lock  ;
        logic [3:0]                      cache ;
        logic [2:0]                      prot  ;
        logic [3:0]                      qos   ;
        axi_aw_userbit_pack              user  ;  // 另一个 struct
    } axi_aw_pack;
    
在此示例中，可以看出 struct 可以嵌套。"axi_aw_pack" 的 struct 嵌套了 "axi_aw_userbit_pack" 的 struct。

此外，如果需要删除或添加某个字段，或更改其宽度，只需更新 struct 定义即可，而不需要更新所有涉及该字段的代码。这就是维护成本方面的优势。

- **调用** 

struct 可以用于定义 IO 和内部信号。例如：

.. code-block:: systemverilog 

    // reg slice 定义
    // 支持三种模式：前向模式 (4'b0001)、后向模式 (4'b0010)、全模式 (4'b0100) 和旁路模式 (4'b1000)
    module reg_slice#(
        parameter logic [2:0]   FUNC_MODE = 4'b0001
        parameter type          PLD_TYPE  = axi_aw_pack
    )(
        input  logic          clk  ,
        input  logic          rst_n  ,
        input  logic          s_vld  ,
        output logic          s_rdy  ,
        input  PLD_TYPE       s_pld  ,  // 用于 IO
        output logic          m_vld  ,
        input  logic          m_rdy  ,
        output PLD_TYPE       m_pld     // 用于 IO
    );
        ......
        PLD_TYPE          pld_buffer ;  // 用于内部信号
        ......
        
        // 主要代码开始
        ......
        // 主要代码结束

    endmodule
    
    // 实例化 1：前向模式
    reg_slice #(
        .FUNC_MODE    (4'b0001          ),
        .PLD_TYPE     (axi_aw_pack      ))  // 以参数形式传入
    u_rs_gpu_aw(
        .clk          (clk              ),
        .rst_n        (rst_n            ),
        .s_vld        (gpu_aw_vld       ),
        .s_rdy        (gpu_aw_rdy       ),
        .s_pld        (gpu_aw_pld       ),
        .m_vld        (gpu_aw_vld_rs    ),
        .m_rdy        (gpu_aw_rdy_rs    ),
        .m_pld        (gpu_aw_pld_rs    )
    );
 
在此示例中，struct 被用作一个特殊的参数。IO 和与 payload 相关的内部信号都通过此 struct 来定义。
当模块实例化时，此 struct 以参数的形式传入。
    
- **访问** 

可以使用 "struct_signal.internal_field" 的格式来访问所需的字段。

struct 类型的信号可以作为整体进行赋值，使用阻塞或非阻塞赋值即可，这在复位阶段和清除操作中非常有用。

以下代码展示了上述目标：

.. code-block:: systemverilog 

    // 用配置值替换原始 qos
    always_comb begin
        aw_pld_new     = aw_pld       ;  // 赋值所有字段
        aw_pld_new.qos = rf_write_qos ;  // 重新赋值所需字段
    end
    
    // 复位 struct 类型的寄存器
    always_ff @(posedge clk or negedge rst_n) begin
        if(~rst_n)
            aw_pld_buffer <= {$bits(axi_aw_pack){1'b0}} ;  // 复位所有
        else if(aw_vld && aw_rdy)
            aw_pld_buffer <= aw_pld ;
    end
