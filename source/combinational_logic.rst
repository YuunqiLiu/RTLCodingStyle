Combinational logic
====================

**assign** and **always_comb** are used to represent combinational logic.

 
- **assign**

**assign** is recommended to be used for the following occasions：

1. combinational logic is relatively simple.
2. combinational logic is relatively symmetric.

For example:

.. code-block:: systemverilog 

    // simple example
    assign handshake_done = req_vld && req_rdy;
    
    // symmetric example
    assign fix_arb = high_qos_req_en ? high_qos_req_pld  :
                     mid_qos_req_en  ? mid_qos_req_pld   :
                     low_qos_req_en  ? low_qos_req_pld   :
                                       {PLD_WIDTH{1'b0}} ;
                       
- **always_comb**

For complex combinational logic, it is recommended to use **always_comb** rather than **assign**.

**if-else** is used combined with **always_comb** for hierarchical judgement and more user-friendly readability.

For design based on systemverilog, **always_comb** is implemented to replace **always( * )**.

For example:

.. code-block:: systemverilog 

    always_comb begin
        if dmc_rdata_vld
            if dmc_rdata_last==1
                if context_table[dmc_rdata_id].dmc_raddr_bit5==1         data_ram_wdata = {256'b0, dmc_rdata} ;
                else                                                     data_ram_wdata = {dmc_rdata, 256'b0} ;
            else
                if context_table[dmc_rdata_id].dmc_raddr_bit5==1         data_ram_wdata = {dmc_rdata, 256'b0} ;
                else                                                     data_ram_wdata = {256'b0, dmc_rdata} ;
        else                                                             data_ram_wdata = 512'b0              ;
    end


- **Summary**
1. For same combinational logic, synthesis result is probably not affected by coding style (assign or always_comb).
2. In fact, always_comb can express all combinational logic with user-friendly readability.
