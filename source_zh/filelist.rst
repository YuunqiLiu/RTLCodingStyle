文件列表
===============

定义文件列表时应遵循以下规则：

1. **保持相同的前缀**

对于一个 IP 的文件列表，所有文件应使用相同的前缀，且该前缀需要确保在项目中是唯一的。

以下是一个示例：

.. code-block:: 

    sc_bus_arbiter.sv
    sc_bus_decoder.sv
    sc_bus_buffer.sv

通常，使用的前缀是该 IP 的缩写（但要注意唯一性）。

2. **集中处理宏定义并及时取消定义**

如果一个 IP 需要使用宏定义，那么所有的 define 必须集中在一个文件中，并放在文件列表的开头。

相应地，所有的 define 都必须有对应的 undefine，同样集中在一个文件中，放在文件列表的末尾。

以下是一个示例：

.. code-block:: 

    sc_bus_define.sv
    sc_bus_arbiter.sv
    sc_bus_decoder.sv
    sc_bus_buffer.sv
    sc_bus_undefine.sv

.. warning::
    除非完全没有其他解决方案，否则不要使用宏定义。

    如果在一个 IP 中定义了宏而在该 IP 文件列表完成后没有取消，它将影响其他 IP 并导致其他 IP 的代码被修改。
