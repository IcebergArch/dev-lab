# exception

## 异常结构
![异常分类](../images/exception_struct.png)

Throwable
 |_Error 尽全力使程序安全退出
 |_Exception 
    |_RuntimeException JVM运行期间可能遇到的异常，NPE、ClassCastException、IOException、SQLException等
    |_CheckedException 一般是外部错误，这种异常都发生在编译阶段，需要体检做好try_catch

