Clock and reset
==================

Clock
-------------------------------------------------
Clock is a very common signal used in flip-flop, latch, and memory.

Clock signal is recommanded to be named using recognized abbreviations as **clk**.

When multiple clock are implemented, suffix is required to be added to distingush each other.

To be mentioned, the suffix shall be clear and meaningful.

For example:

.. code-block:: systemverilog 

    input  logic    clk_slc    , // function suffix, System Level Cache clock
    input  logic    clk_dmc    , // function suffix, DDR Memory Controller clock
    input  logic    clk_cfg    , // config suffix
    input  logic    clk_dbg    , // debug suffix
    input  logic    ......

In addition, there are some notes for clock.

1. **ocks that are require dynamic frequency switching are processed by a dedicated glitch free circuit. It must be a common IP and don't do it by yourself.**
2. **r clock, "&" and "|" directily written by user will be synthesized as a logic with glitch.**



Reset
-------------------------------------------------
Like clock, reset is a another common signal used in flip-flop, latch, and memory.

Reset signal is recommanded to be named using recognized abbreviations as **rst**.

Given that reset is mostly active low, the above abbreviation is optimized to **rst_n**. "_n" represents low level active.

As same as clock, suffix is required to be added when multiple reset are implemented.

.. code-block:: systemverilog 

    input  logic    rst_n_slc    , // function suffix, System Level Cache reset
    input  logic    rst_n_dmc    , // function suffix, DDR Memory Controller reset
    input  logic    rst_n_hwe    , // debug suffix, debug IP reset
    input  logic    rst_n_sw     , // software suffix, software reset
    input  logic    ......

For triggering mode of reset, it can be asynchronous or synchronous:

.. code-block:: systemverilog 

    // asynchronous trigger
    always_ff @(posedge clk or negedge rst_n) begin
        if(~rst_n) begin
            ......
        end
        else begin
            ......
        end
    end
    
    // synchronous trigger
    always_ff @(posedge clk) begin
        if(~rst_n) begin
            ......
        end
        else begin
            ......
        end
    end

**Note that reset shall follow the principle of synchronous releasing.**
