变量定义与命名
==============================

- **显式声明类型**

verilog/system verilog 在类型不明确时会使用默认类型。

以下是一些示例。

.. code-block:: systemverilog

    // 这段代码没有显式声明类型，
    // 语言会自动推断为 1bit logic 类型。
    // 等效于 
    // output logic valid
    output  valid,          


.. code-block:: systemverilog

    // 语言根据值 "32" 推断其为 "integer unsigned" 类型。
    // 等效于
    // parameter integer unsigned WIDTH = 32
    parameter WIDTH = 32

我们应该直接指定变量类型，而不是让语言进行类型推断。

因为有时候语言的推断结果可能与我们的意图不一致。



以下是一些好的示例。


.. code-block:: systemverilog

    output logic            valid,
    output logic [31:0]     payload,


.. code-block:: systemverilog

    parameter integer unsigned WIDTH = 32
    parameter integer unsigned DEPTH 


- **同一组变量使用相同的前缀**


如果若干 IO 或若干信号以总线的方式传输和处理，即这些信号共同构成一个功能或协议。

例如 AXI 的一个通道。

.. code-block:: systemverilog

    input   logic           awvalid   ,
    output  logic           awready   ,
    output  logic  [31:0]   awaddr    ,


对于这些信号，应该使用相同的前缀名，以下是一些示例。


.. code-block:: systemverilog

    logic           in_vld  ;
    logic           in_rdy  ;
    logic [31:0]    in_data ;

.. code-block:: systemverilog

    input   logic           out_rdy ,
    output  logic           out_vld ,
    output  logic [31:0]    out_pld ,


- **向量变量添加前缀 "v\_"**

向量是一组相同类型但功能独立的信号。

对于向量的定义，这里给出两个示例。

.. code-block:: systemverilog

    // 该信号由32个独立的 en 组成，因此我们认为它是一个需要添加前缀的向量。
    logic [31:0] in_v_en    ;

    // 该信号的32位共同构成一个数据，因此逻辑上我们不认为它是一个需要添加前缀的向量。
    logic [31:0] in_data    ;



- **有意义的命名**

- **适当使用缩写**
