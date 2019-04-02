# 桥接模式

[C#设计模式(8)—桥接模式（Bridge Pattern）](https://www.cnblogs.com/zhili/p/BridgePattern.html)

**实现**

```
public interface IImpl
{
    void OperationImpl();
}

public interface IAbstraction
{
    IImpl Implementor{get;set;}
    void Operation();
}

public class ImplA:IImpl
{
    public void OperationImpl()
    {

    }
}

public class ImplB:IImpl
{
    public void OperationImpl()
    {
        
    }
}

public class Abstraction:IAbstraction
{
    private IImpl impl;
    public IImpl Impl
    {
        get{return this.impl;}
        set{this.impl=value;}
    }
    public void Operation()
    {
        impl.OperationImpl();
    }
}
```