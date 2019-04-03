# 模板方法模式

[C#设计模式(14)—模板方法模式（Template Method）](https://www.cnblogs.com/zhili/p/TemplateMethodPattern.html)

**定义**

&emsp;&emsp;模板方法模式——在一个抽象类中定义一个操作中的算法骨架（对应于生活中的大家下载的模板），而将一些步骤延迟到子类中去实现（对应于我们根据自己的情况向模板填充内容）。模板方法使得子类可以不改变一个算法的结构前提下，重新定义算法的某些特定步骤，模板方法模式把不变行为搬到超类中，从而去除了子类中的重复代码。

> 实现

```
//抽象的模板接口
public interface IAbstract
{
    int Quantity {get;}
    double Total {get;}
    double Average {get;}
}

//定义了算法梗概的抽象类型
public abstract class AbstractBase:IAbstract
{
    public abstract int Quantity {get;}
    public abstract double Total {get;}
    //算法梗概
    public virtual double Average
    {
        get
        {
            return Total/Quantity;
        }
    }
}

//具体类型
public class ArrayData:AbstractBase
{
    protected double[] data=new double[3]{1.1,2.2,3.3};
    public override int Quantity
    {
        get
        {
            return data.Length;
        }
    }
    public override double Total
    {
        get
        {
            double count=0;
            for(int i=0; i<data.Length; i++)
                count+=data[i];
            return count;
        }
    }
}

//具体类型
public class ListData:AbstractBase
{
    protected IList<double> data=new List<double>();
    public ListData()
    {
        data.Add(1.1);
        data.Add(2.2);
        data.Add(3.3);
    }
    public override int Quantity
    {
        get
        {
            return data.Count;
        }
    }
    public override double Total
    {
        get
        {
            double count=0;
            foreach(double item in data)
                count+=item;
            return count;
        }
    }
}

//客户端调用
IAbstract i1=new ArrayData();
IAbstract i2=new ListData();
```