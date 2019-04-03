# 责任链模式

[C#设计模式(21)—责任链模式](https://www.cnblogs.com/zhili/p/ChainOfResponsibity.html)

**定义**

&emsp;&emsp;责任链模式指的是——某个请求需要多个对象进行处理，从而避免请求的发送者和接收之间的耦合关系。将这些对象连成一条链子，并沿着这条链子传递该请求，直到有对象处理它为止。

> 实现

```
namespace ChainofResponsibility
{
    // 采购请求
    public class PurchaseRequest
    {
        // 金额
        public double Amount { get; set; }
        // 产品名字
        public string ProductName { get; set; }
        public PurchaseRequest(double amount, string productName)
        {
            Amount = amount;
            ProductName = productName;
        }
    }

    //抽象的操作对象 Handler
    public interface IHandler
    {
        //处理客户程序请求
        void HandlerRequest(PurchaseRequest request);
        //后继节点
        IHandler Successor {get;set;}
    }

    // 操作的抽象类型
    public abstract class HandlerBase:IHandler
    {
        public IHandler Successor { get; set; }
        public string Name { get; set; }
        public double Amount { get; set; }
        public HandlerBase(string name,double Amount)
        {
            this.Name = name;
            this.Amount = Amount;
        }
        //需要具体IHandler类型处理的内容
        public abstract void Process(PurchaseRequest request);
        //按照链式方式一次把调用继续下去
        public virtual void HandlerRequest(PurchaseRequest request)
        {
            if(request == null)
                return;
            if(request.Amount < Amount)
                Process(request);
            else
                if(Successor != null)
                    Successor.HandlerRequest(request);
        }
    }

    // ConcreteHandler
    public class Manager : HandlerBase
    {
        public Manager(string name)
            : base(name,10000.0)
        { }

        public override void Process(PurchaseRequest request)
        {
            Console.WriteLine("{0}-{1} Handler the request of purshing {2}", this, Name, request.ProductName);
        }
    }

    // ConcreteHandler,副总
    public class VicePresident : HandlerBase
    {
        public VicePresident(string name)
            : base(name,25000.0)
        { }
        public override void Process(PurchaseRequest request)
        {
            Console.WriteLine("{0}-{1} Handler the request of purshing {2}", this, Name, request.ProductName);
        }
    }

    // ConcreteHandler，总经理
    public class President :HandlerBase
    {
        public President(string name)
            : base(name,100000.0)
        { }
        public override void Process(PurchaseRequest request)
        {
            Console.WriteLine("{0}-{1} Handler the request of purshing {2}", this, Name, request.ProductName);
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            PurchaseRequest requestTelphone = new PurchaseRequest(4000.0, "Telphone");
            PurchaseRequest requestSoftware = new PurchaseRequest(10000.0, "Visual Studio");
            PurchaseRequest requestComputers = new PurchaseRequest(40000.0, "Computers");

            IHandler manager = new Manager("LearningHard");
            IHandler Vp = new VicePresident("Tony");
            IHandler Pre = new President("BossTom");

            // 设置责任链
            manager.Successor = Vp;
            Vp.Successor = Pre;

            // 处理请求
            manager.HandlerRequest(requestTelphone);
            manager.HandlerRequest(requestSoftware);
            manager.HandlerRequest(requestComputers);
            Console.ReadLine();
        }
    }
}
```