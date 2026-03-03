缩进与对齐
============================


缩进
------------


- **所有缩进必须使用4个空格。**


下面是一个示例。

.. code-block:: systemverilog

    always_comb begin
        if(enable_1) 
            if(enable_2)
                data = 1'b1;
            else
                data = 1'b0;
        else
            if(enable_3)
                data = 1'b0;
            else
                data = 1'b1;
    end


.. warning:: 

    "tab" 和 "4个空格" 看起来很相似，但请不要使用 tab 来代替4个空格。

    一些编辑器可以自动将 tab 键替换为4个空格。


- **每个子代码块必须缩进一级。**


以下是一些示例。

.. code-block:: systemverilog

    module pipe (
        // IO 是 module 的子代码块
        input           clk     ,
        input           rst_n   ,
        input           din0    ,
        input           din1    ,
        output logic    dout0   ,
        output logic    dout1
    );
        // RTL 内容是 module 的子代码块
        always_ff @(posedge clk or negedge rst_n) begin

            // if-else 是 always 的子代码块
            if(rst_n) begin
        
                // 这是 if-else 的子代码块
                dout0 <= 1'b0;
                dout1 <= 1'b0;
            end
            else begin
                dout0 <= din0;
                dout1 <= din1;
            end
        end

        // 实际上，begin ... end 形成了一个子块，但按照惯例，
        // "begin" 可以留在上一行，"end" 另起一行但不缩进，
        // 子块内的其余代码必须另起一行并缩进。

    endmodule

对齐
----------------

通常，功能相似的几行代码中的变量名、运算符、关键字等应该对齐，如以下示例所示。

.. code-block:: systemverilog

    assign res0  = din0  + din1    ;
    assign res32 = din45 + din235  ;


.. code-block:: systemverilog


    always_comb begin
        out_en  = in_en  ;
        out_pld = in_pld ;
    end

.. code-block:: systemverilog

    input               out_ready   ,
    output logic        out_valid   ,
    output logic [31:0] out_payload ,

.. code-block:: systemverilog

    reg_slice u_rs (
        .clk         (clk         ),  
        .rst_n       (rst_n       ),  
        .in_valid    (in_valid    ),      
        .in_ready    (in_ready    ),      
        .in_payload  (in_payload  ),      
        .out_valid   (out_valid   ),      
        .out_ready   (out_ready   ),      
        .out_payload (out_payload )          
    );
