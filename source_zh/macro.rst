宏定义
=======

除非完全没有其他解决方案，否则不要使用宏定义！！！
------------------------------------------------------

对于经常使用的常量，建议通过 **parameter** 或 **localparam** 来定义。

建议使用一个专门的 package 文件来包含所使用的常量，并在 module IO 定义之前导入它。

例如：

.. code-block:: systemverilog 

    module sc_cmn_vr_dec
        
        import scc_param_pkg::*;
        // package 生效 ===============================
    #(
        parameter integer unsigned A = 1
    )(
        input         clk    ,
        output logic  data
    );

        // RTL 代码
        // ...

        // package 失效 ===============================
    endmodule
    
    
这种方法的优势在于常量的生效范围被明确固定在模块内部。
 
并且不会因为生效范围而对其他模块或设计产生不可预期的影响。


如果必须使用宏，请严格遵守以下规则
------------------------------------------------------

如果一个 IP 需要使用宏定义，那么所有的 define 必须集中在一个文件中，并放在文件列表的开头。

相应地，所有的 define 都必须有对应的 undefine，同样集中在一个文件中，放在文件列表的末尾。

以下是一个示例：

.. code-block:: 

    sc_bus_define.sv       // 宏定义生效
    sc_bus_arbiter.sv
    sc_bus_decoder.sv
    sc_bus_buffer.sv
    sc_bus_undefine.sv     // 宏定义失效

**如果在一个 IP 中定义了宏而在该 IP 文件列表完成后没有取消，它将影响其他 IP 并导致其他 IP 的代码被修改。**
