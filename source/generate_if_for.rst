Generate if/for
=================

**generate** is a method to produce batch code based on user expectations.

In general, **generate** is always combined with **if** or **for**:

1. **generate-if**: select and produce desired code from a multifunctional design.
2. **generate-for**: batch processing code with regularity.

- **generate-if**
For universality and integrability, some design implements all function may used which are seperated by **generate-if**.

During instantiating, user can select desired function through giving the corresponding parameter.

For example:

.. code-block:: systemverilog 

    // a reg slice defination
    // support three mode: forward mode (4'b0001), backward mode (4'b0010), full mode (4'b0100), and bypass mode (4'b1000)
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
    
    // instance 1: forward mode
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
    
    // instance 2: full mode
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
For batching instantiation of modules and implementation of regular code, **generate-for** is very usefull.

The following exapmle is bacthing instantiation of modules by **generate-for**:

Note that hierarchy of the generated modules is *xxx.yyy.GPU_AW_RS[i].u_rs_gpu_aw*.

.. code-block:: systemverilog 
    
    // instantiate four regslice for all gpu write request path
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

The following exapmle is bacthing implementation of regular code by **generate-for**:

.. code-block:: systemverilog 
    
    // tag check process of a 16 way cache
    genvar i;
    generate for(i=0; i<16; i=i+1) begin:Tag_Check
        assign v_tag_hit =  v_tag_vld[i] && (v_tag[i] == req_tag) ;
    end
    endgenerate


- **Pay attention to**
1. **Loop variable must be declared using genvar for generate-for.**
2. **Whether it's generate-if or generate-for, each process block (begin-end) must have unique label (such as GPU_AW_RS).**
3. **generate-if can be replaced by generate-case to realize the same function.**
