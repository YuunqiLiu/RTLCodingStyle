Generate if/for
=================

**generate** 是一种根据用户期望批量生成代码的方法。

通常，**generate** 总是与 **if** 或 **for** 结合使用：

1. **generate-if**：从多功能设计中选择并生成所需的代码。
2. **generate-for**：批量处理具有规律性的代码。

- **generate-if**
为了通用性和可集成性，一些设计实现了所有可能用到的功能，这些功能通过 **generate-if** 来分隔。

在实例化时，用户可以通过给出相应的参数来选择所需的功能。

例如：

.. code-block:: systemverilog 

    // reg slice 定义
    // 支持三种模式：前向模式 (4'b0001)、后向模式 (4'b0010)、全模式 (4'b0100) 和旁路模式 (4'b1000)
    module reg_slice #(
        parameter logic [2:0]   FUNC_MODE = 4'b0001 ,
        parameter type          PLD_TYPE  = logic
    )(
        input  logic          clk  ,
        input  logic          rst_n  ,
        input  logic          s_vld  ,
        output logic          s_rdy  ,
        input  PLD_TYPE       s_pld  ,
        output logic          m_vld  ,
        input  logic          m_rdy  ,
        output PLD_TYPE       m_pld   
    );
        generate 
            if (FUNC_MODE == 4'b0001) begin:Forward_Mode
                ......
            end
            else if (FUNC_MODE == 4'b0010) begin:Backward_Mode
                ......
            end
            else if (FUNC_MODE == 4'b0100) begin:Full_Mode
                ......
            end
            else if (FUNC_MODE == 4'b1000) begin:Bypass_Mode
                ......
            end
        endgenerate

    endmodule
    
    // 实例化 1：前向模式
    reg_slice #(
        .FUNC_MODE    (4'b0001          ),
        .PLD_TYPE     (slv_aw_pack      ))
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
    
    // 实例化 2：全模式
    reg_slice #(
        .FUNC_MODE    (4'b0100          ),
        .PLD_TYPE     (mst_aw_pack      ))
    u_rs_dmc_aw(
        .clk          (clk              ),
        .rst_n        (rst_n            ),
        .s_vld        (dmc_aw_vld       ),
        .s_rdy        (dmc_aw_rdy       ),
        .s_pld        (dmc_aw_pld       ),
        .m_vld        (dmc_aw_vld_rs    ),
        .m_rdy        (dmc_aw_rdy_rs    ),
        .m_pld        (dmc_aw_pld_rs    )
    );



- **generate-for**
对于批量实例化模块和实现有规律的代码，**generate-for** 非常有用。

以下示例通过 **generate-for** 批量实例化模块：

注意生成的模块层次结构为 *xxx.yyy.GPU_AW_RS[i].u_rs_gpu_aw*。

.. code-block:: systemverilog 
    
    // 为所有 GPU 写请求路径实例化四个 regslice
    genvar i;
    generate for(i=0; i<4; i=i+1) begin:GPU_AW_RS
        reg_slice #(
            .FUNC_MODE    (4'b0001          ),
            .PLD_TYPE     (slv_aw_pack      ))
        u_rs_gpu_aw(
            .clk          (clk                  ),
            .rst_n        (rst_n                ),
            .s_vld        (v_gpu_aw_vld     [i] ),
            .s_rdy        (v_gpu_aw_rdy     [i] ),
            .s_pld        (v_gpu_aw_pld     [i] ),
            .m_vld        (v_gpu_aw_vld_rs  [i] ),
            .m_rdy        (v_gpu_aw_rdy_rs  [i] ),
            .m_pld        (v_gpu_aw_pld_rs  [i] )
        );
    end
    endgenerate

以下示例通过 **generate-for** 批量实现有规律的代码：

.. code-block:: systemverilog 
    
    // 16路 cache 的 tag 检查过程
    genvar i;
    generate for(i=0; i<16; i=i+1) begin:Tag_Check
        assign v_tag_hit =  v_tag_vld[i] && (v_tag[i] == req_tag) ;
    end
    endgenerate


- **注意事项**
1. **generate-for 的循环变量必须使用 genvar 声明。**
2. **无论是 generate-if 还是 generate-for，每个过程块（begin-end）都必须有唯一的标签（如 GPU_AW_RS）。**
3. **generate-if 可以用 generate-case 替代以实现相同的功能。**
