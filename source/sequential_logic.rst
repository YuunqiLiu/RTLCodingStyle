Sequential logic
==================

In systemverilog, key word of **always_ff** is introduced to indicate sequential logic.

It is recommended that express sequential logic using **always_ff**.

**In addition, only nonblocking assignment shall be used in sequential process block.**

For example:

.. code-block:: systemverilog 

    // Flip data path using asynchronous reset
    always_ff @(posedge clk or negedge rst_n) begin
        if(~rst_n)
            data_d <= {DATA_WIDTH{1'b0}} ;
        else if(data_en)
            data_d <= data ;
    end
    
    
For **always_ff** process block, it is recommended to omit the last **else**. 

In this way, when there are more than three or four register drived by a same enable signal, EDA tool will insert/infer clock gating cell automatically.

However, if you write the last **else** branch (data_d <= data_d), some tool can also insert/infer it. But we still recommend omit it.

For reset in sequential logic, pay attention to:

1. **if expect synchronous reset, please omit "negedge rst_n" in "always_ff" line.** For example:

.. code-block:: systemverilog 

    // Flip data path using synchronous reset
    always_ff @(posedge clk) begin
        if(~rst_n)
            data_d <= {DATA_WIDTH{1'b0}} ;
        else if(data_en)
            data_d <= data ;
    end
 
2. **In sequential logic, reset is not mandatory and can be omitted in data path for high speed and low area.** For example:

.. code-block:: systemverilog 

    // Flip data path without reset
    always_ff @(posedge clk) begin
        if(data_en)
            data_d <= data ;
    end
