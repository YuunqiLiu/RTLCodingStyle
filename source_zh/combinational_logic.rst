组合逻辑
====================

**assign** 和 **always_comb** 用于表示组合逻辑。

 
- **assign**

**assign** 推荐用于以下场景：

1. 组合逻辑相对简单。
2. 组合逻辑相对对称。

例如：

.. code-block:: systemverilog 

    // 简单示例
    assign handshake_done = req_vld && req_rdy;
    
    // 对称示例
    assign fix_arb = high_qos_req_en ? high_qos_req_pld  :
                     mid_qos_req_en  ? mid_qos_req_pld   :
                     low_qos_req_en  ? low_qos_req_pld   :
                                       {PLD_WIDTH{1'b0}} ;
                       
- **always_comb**

对于复杂的组合逻辑，推荐使用 **always_comb** 而非 **assign**。

**if-else** 与 **always_comb** 结合使用，用于层次化判断和更好的可读性。

对于基于 SystemVerilog 的设计，使用 **always_comb** 来替代 **always( * )**。

例如：

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


- **总结**

1. 对于相同的组合逻辑，编码风格（assign 或 always_comb）可能不会影响综合结果。
2. 实际上，always_comb 可以表达所有组合逻辑，且具有更好的可读性。
