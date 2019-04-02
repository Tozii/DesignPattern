# 原型模式

**使用**
```
//原型接口
public interface IPrototype
{
    IPrototype Clone();
    string Name{get;set;}
}

public class Prototype:IPrototype
{
    public IPrototype Clone()
    {
        return (IPrototype)this.MemberwiseClone();
    }
    private string name;
    public string Name
    {
        get{return this.name;}
        set{this.name=value;}
    }
}
```