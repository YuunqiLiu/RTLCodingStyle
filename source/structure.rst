Structure
==========

In systemverilog, struct is introduced to pack some signals into an integrated signal.

- **Advantage** 

1. Code length can be significantly reduced.

2. Code readability is improved.

3. Maintenance cost of code is significantly reduced. 

- **Defination** 

The defination of struct is shown below:

.. code-block:: systemverilog 

    // AXI AW channel
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
        axi_aw_userbit_pack              user  ;  // another struct
    } axi_aw_pack;
    
In this example, it can be concluded that struct can be nested. The struct of "axi_aw_pack" nests the struct of "axi_aw_userbit_pack".

In addition, if some field is deleted or added, or its width is changed, you only need to update the struct defination rather than update all code this field involved. It is the advantage for maintenance cost of code.

- **invoking** 

Struct can be invoked to define both IO and internal signal. For example:

.. code-block:: systemverilog 

    // a reg slice defination
    // support three mode: forward mode (4'b0001), backward mode (4'b0010), full mode (4'b0100), and bypass mode (4'b1000)
    module reg_slice#(
        parameter logic [2:0]   FUNC_MODE = 4'b0001
        parameter type          PLD_TYPE  = axi_aw_pack
    )(
        input  logic          clk  ,
        input  logic          rst_n  ,
        input  logic          s_vld  ,
        output logic          s_rdy  ,
        input  PLD_TYPE       s_pld  ,  // invoked for IO
        output logic          m_vld  ,
        input  logic          m_rdy  ,
        output PLD_TYPE       m_pld     // invoked for IO
    );
        ......
        PLD_TYPE          pld_buffer ;  // invoked for internal signal
        ......
        
        // Main Code Start
        ......
        // Main Code End

    endmodule
    
    // instance 1: forward mode
    reg_slice #(
        .FUNC_MODE    (4'b0001          ),
        .PLD_TYPE     (axi_aw_pack      ))  // passed into instance
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
 
For this example, struct is used as a special parameter. And both IO and internal signal releated to payload are defined by this struct.
When the module is instantiated, this struct is passed in fromat of parameter.
    
- **accessing** 

You can use the foramt of "struct_signal.internal_field" to access the desired field.

And the struct type signal can be assigened as a whole just using a blocking or nonblocking assignment, which is very usefull in reset phase and clear operation.

The following code shows the goal mentioned above:

.. code-block:: systemverilog 

    // replace original qos with configured value
    always_comb begin
        aw_pld_new     = aw_pld       ;  // assign all
        aw_pld_new.qos = rf_write_qos ;  // re-assign a desired field
    end
    
    // reset struct type registers
    always_ff @(posedge clk or negedge rst_n) begin
        if(~rst_n)
            aw_pld_buffer <= {$bits(axi_aw_pack){1'b0}} ;  // reset all
        else if(aw_vld && aw_rdy)
            aw_pld_buffer <= aw_pld ;
    end
