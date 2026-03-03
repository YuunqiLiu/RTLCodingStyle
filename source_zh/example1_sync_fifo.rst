同步 FIFO
============================

以下是一个同步 FIFO 的实现：

- 使用两个指针记录写/读位置

- 实现一个计数器来记录已使用空间

- 遵循握手协议。req_rdy 为低 -> 满，ack_vld 为低 -> 空

- 支持用户自定义 payload

- 存储器不复位

.. code-block:: systemverilog

    //////////////////////////////////////////////////////////////////////////////////
    // Company: URBADASS.LTD
    // Engineer: zick
    // 
    // Create Date: 02/26/2023 09:01:54 PM
    // Design Name: sync fifo handshake
    // Module Name: sync_fifo
    // Project Name: sync fifo
    // Description: 
    //              a handshake based sync fifo for serialized data
    // Dependencies: 
    // 
    // Revision:
    //              Revision 0.01 - File Created
    // Additional Comments:
    // 
    //////////////////////////////////////////////////////////////////////////////////
    module sync_fifo #(
        parameter   int unsigned DEPTH    = 8             ,
        parameter   type         PLD_TYPE = logic[32-1:0] ,  
        localparam  int unsigned AWIDTH   = $clog2(DEPTH)
    )(
        input   logic               clk    ,
        input   logic               rst_n  ,
        // 请求端
        input   logic               req_vld,
        output  logic               req_rdy,
        input   PLD_TYPE            req_pld,
        // 应答端
        output  logic               ack_vld,
        input   logic               ack_rdy,
        output  PLD_TYPE            ack_pld
    );

    //=================================================================
    // 内部信号
    //=================================================================
        PLD_TYPE                    pld_mem  [DEPTH-1:0];
        logic       [AWIDTH-1  :0]  wr_ptr              ;
        logic       [AWIDTH-1  :0]  rd_ptr              ;
        logic                       rd_en               ;
        logic                       wr_en               ;
        logic       [AWIDTH    :0]  fifo_cnt            ;
        logic                       fifo_full           ;
        logic                       fifo_empty          ;

    //=================================================================
    // 读写使能
    //=================================================================
        assign wr_en = req_vld && req_rdy;
        assign rd_en = ack_vld && ack_rdy;

    //=================================================================
    // 深度计数器
    //=================================================================
        always_ff @(posedge clk or negedge rst_n) begin
            if (~rst_n) begin
                fifo_cnt <= {AWIDTH+1{1'b0}};
            end
            else begin
                case ({rd_en, wr_en})
                    {1'b1, 1'b0}: fifo_cnt <= (AWIDTH+1)'(fifo_cnt - 1'b1); // 读，旧版 vcs/xrun 不支持 type(fifo_cnt)'
                    {1'b0, 1'b1}: fifo_cnt <= (AWIDTH+1)'(fifo_cnt + 1'b1); // 写 
                    default     : fifo_cnt <= fifo_cnt;        // 无操作或同时读写
                endcase
            end
        end

    //=================================================================
    // 空 & 满
    //=================================================================
        assign fifo_empty = ~|fifo_cnt;
        assign fifo_full  = fifo_cnt == DEPTH;

    //=================================================================
    // 握手
    //=================================================================
        assign ack_vld = ~fifo_empty;
        assign req_rdy = ~fifo_full;

    //=================================================================
    // 读写控制
    //=================================================================
        always_ff @(posedge clk or negedge rst_n) begin
            if (~rst_n) begin
                wr_ptr <= {AWIDTH{1'b0}};
            end
            else if (wr_en) begin
                if (wr_ptr < DEPTH-1)
                    wr_ptr <= AWIDTH'(wr_ptr + 1'b1);
                else 
                    wr_ptr <= {AWIDTH{1'b0}};
            end
        end

        always_ff @(posedge clk or negedge rst_n) begin
            if (~rst_n) begin
                rd_ptr <= {AWIDTH{1'b0}};
            end
            else if (rd_en) begin
                if (rd_ptr < DEPTH-1)
                    rd_ptr <= AWIDTH'(rd_ptr + 1'b1);
                else 
                    rd_ptr <= {AWIDTH{1'b0}};
            end
        end

    //=================================================================
    // 存储器访问
    //=================================================================
        always_ff @(posedge clk) begin
            if (wr_en) pld_mem[wr_ptr] <= req_pld;
        end
        assign ack_pld = pld_mem[rd_ptr];

    endmodule
