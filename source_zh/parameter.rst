参数
==========

参数定义应采用以下形式：

.. code-block:: systemverilog 

    parameter integer unsigned    EXAMPLE1 = 1,
    parameter logic [31:0]        EXAMPLE2 = 32'b0

- **定义时应显式声明具体的数据类型。**


- **参数应全部使用大写字母，以示与其他变量的区别。**
- **每行只定义一个参数。**
- **使用参数进行电路赋值时，需要注意是否可综合。**
