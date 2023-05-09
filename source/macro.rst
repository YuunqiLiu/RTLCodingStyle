Macro
=======

Do not use macro definitions unless there is no other solution to the problem at all!!!
-----------------------

For the constants frequently used, it is recommended to define them by **parameter** or **localparam**.

A special package file is expected to include used constants. And import it before moudle IO defination.

For example:

.. code-block:: systemverilog 

    module sc_cmn_vr_dec
        
        import scc_param_pkg::*;
        // package takes effect ===============================
    #(
        parameter integer unsigned A = 1
    )(
        input         clk    ,
        output logic  data
    );

        // RTL code
        // ...

        // package becomes invalid ===============================
    endmodule
    
    
The advantage of this method is that the effectiveness scope of the constants is clearly fixed, which is inside the module.
 
And there is no unpredictable impact for other modules or designs dut to the effctiveness scope.


If you must use macro, please strictly observe the following rule
-----------------------

If an IP needs to use macro definitions, then all the defines must be grouped together in one file and placed at the beginning of the filelist.

Correspondingly, all defines must have their undefine counterparts, which are also grouped together in a single file and placed at the end of the filelist

An example is as follows:

.. code-block:: 

    sc_bus_define.sv       // macro defination takes effect
    sc_bus_arbiter.sv
    sc_bus_decoder.sv
    sc_bus_buffer.sv
    sc_bus_undefine.sv     // macro defination becomes invalid

**If a macro definition is defined in an IP and not cancelled after the IP filelist is completely finished, it will affect other IPs and cause the code of other IPs to be modified.**
