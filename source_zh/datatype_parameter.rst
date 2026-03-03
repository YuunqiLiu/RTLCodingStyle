数据类型参数
====================


数据类型参数（parameter type）是一个非常有用的特性，允许你将类型传递给参数，并使用该参数来定义新变量，以下是一个示例。


.. code-block:: systemverilog 


    module param_test #(
        parameter type DAT_TYPE = logic
    )(
        input   DAT_TYPE din  ,
        output  DAT_TYPE dout
    );

        assign dout = din;

    endmodule


上面示例中的模块可以如下实例化。


.. code-block:: systemverilog 

    //实例化 1
    param_test #(
        .DAT_TYPE(logic [2:0]))
    uinst_1 (
        .din    (din_width3     ),
        .dout   (dout_width3    ));

    //实例化 2
    param_test #(
        .DAT_TYPE(logic [5:0]))
    u_inst2 (
        .din    (din_width6     ),
        .dout   (dout_width6    ));

这种用法与定义 "parameter DATA_WIDTH = 3" 没有区别，但 parameter type 还可以支持包（package），以及更具体的类型。以下是两个更有意义的示例。

示例 1。

.. code-block:: systemverilog 

    module adder #(
        parameter DAT_TYPE = logic
    )(
        input  DAT_TYPE         din1,
        input  DAT_TYPE         din2,
        output type(din1+din2)  dout
    );

        assign dout = din1 + din2;

    endmodule

    //--------------------------------------------
    // 实例化

    //无符号加法器
    adder #(logic unsigned [7:0])
    u_uadder (
        .din1(udin1 ),
        .din2(udin2 ),
        .dout(udout ));

    //有符号加法器
    adder #(logic signed [7:0])
    u_sadder (
        .din1(sdin1 ),
        .din2(sdin2 ),
        .dout(sdout ));

示例 2。

在此示例中，如果对 package 进行任意增删，不关心 package 内容的 regslice 都不需要修改。

.. code-block:: systemverilog 

    module reg_slice #(
        parameter PLD_TYPE = logic
    )(
        output logic    out_vld ,
        input  logic    out_rdy ,
        output PLD_TYPE out_pld ,

        input  logic    in_vld  ,
        output logic    in_rdy  ,
        input  PLD_TYPE in_pld 
    );

        assign out_vld  = in_vld    ;
        assign in_rdy   = out_rdy   ;
        assign out_pld  = in_pld    ;

    endmodule

    package PLD1;
        logic [31:0]    addr    ;
        logic [3:0]     qos     ;
    endpackage

    package PLD2;
        logic [31:0]    addr    ;
        logic [3:0]     qos     ;
        logic [4:0]     user    ;
    endpackage

    //--------------------------------------------
    // 实例化

    reg_slice #(
        .PLD_TYPE(PLD1))
    u_rs1 (
        .in_vld     (in_vld1    ),
        .in_rdy     (in_rdy1    ),
        .in_pld     (in_pld1    ),
        .out_vld    (out_vld1   ),
        .out_rdy    (out_rdy1   ),
        .out_pld    (out_pld1   ));

    reg_slice #(
        .PLD_TYPE(PLD2))
    u_rs2 (
        .in_vld     (in_vld2    ),
        .in_rdy     (in_rdy2    ),
        .in_pld     (in_pld2    ),
        .out_vld    (out_vld2   ),
        .out_rdy    (out_rdy2   ),
        .out_pld    (out_pld2   ));


通过使用数据类型参数，一个模块可以处理不同的数据类型。


.. attention:: 

    **此语法是可综合的，已在主流商业综合和质量检查工具上验证通过。**
