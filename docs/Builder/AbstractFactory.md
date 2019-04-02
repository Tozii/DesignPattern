# 抽象工厂

**使用**

```
    /// <summary>
    /// 下面以绝味鸭脖连锁店为例子演示下抽象工厂模式
    /// 因为每个地方的喜欢的口味不一样，有些地方喜欢辣点的，有些地方喜欢吃不辣点
    /// 客户端调用
    /// </summary>
    class Client
    {
        static void Main(string[] args)
        {
            // 南昌工厂制作南昌的鸭脖和鸭架
            AbstractFactory nanChangFactory = new NanChangFactory();
            YaBo nanChangYabo = nanChangFactory.CreateYaBo();
            nanChangYabo.Print();
            YaJia nanChangYajia= nanChangFactory.CreateYaJia();
            nanChangYajia.Print();

            // 上海工厂制作上海的鸭脖和鸭架
            AbstractFactory shangHaiFactory = new ShangHaiFactory();
            shangHaiFactory.CreateYaBo().Print();
            shangHaiFactory.CreateYaJia().Print();

            Console.Read();
        }
    }

    /// <summary>
    /// 抽象工厂类，提供创建两个不同地方的鸭架和鸭脖的接口
    /// </summary>
    public abstract class AbstractFactory
    {
        // 抽象工厂提供创建一系列产品的接口，这里作为例子，只给出了绝味中鸭脖和鸭架的创建接口
        public abstract YaBo CreateYaBo();
        public abstract YaJia CreateYaJia();
    }

    /// <summary>
    /// 南昌绝味工厂负责制作南昌的鸭脖和鸭架
    /// </summary>
    public class NanChangFactory : AbstractFactory
    {
        // 制作南昌鸭脖
        public override YaBo CreateYaBo()
        {
            return new NanChangYaBo();
        }
        // 制作南昌鸭架
        public override YaJia CreateYaJia()
        {
            return new NanChangYaJia();
        }
    }

    /// <summary>
    /// 上海绝味工厂负责制作上海的鸭脖和鸭架
    /// </summary>
    public class ShangHaiFactory : AbstractFactory
    {
        // 制作上海鸭脖
        public override YaBo CreateYaBo()
        {
            return new ShangHaiYaBo();
        }
        // 制作上海鸭架
        public override YaJia CreateYaJia()
        {
            return new ShangHaiYaJia();
        }
    }

    /// <summary>
    /// 鸭脖抽象类，供每个地方的鸭脖类继承
    /// </summary>
    public abstract class YaBo
    {
        /// <summary>
        /// 打印方法，用于输出信息
        /// </summary>
        public abstract void Print();
    }

    /// <summary>
    /// 鸭架抽象类，供每个地方的鸭架类继承
    /// </summary>
    public abstract class YaJia
    {
        /// <summary>
        /// 打印方法，用于输出信息
        /// </summary>
        public abstract void Print();
    }

    /// <summary>
    /// 南昌的鸭脖类，因为江西人喜欢吃辣的，所以南昌的鸭脖稍微会比上海做的辣
    /// </summary>
    public class NanChangYaBo : YaBo
    {
        public override void Print()
        {
            Console.WriteLine("南昌的鸭脖");
        }
    }

    /// <summary>
    /// 上海的鸭脖没有南昌的鸭脖做的辣
    /// </summary>
    public class ShangHaiYaBo : YaBo
    {
        public override void Print()
        {
            Console.WriteLine("上海的鸭脖");
        }
    }

    /// <summary>
    /// 南昌的鸭架
    /// </summary>
    public class NanChangYaJia : YaJia
    {
        public override void Print()
        {
            Console.WriteLine("南昌的鸭架子");
        }
    }

    /// <summary>
    /// 上海的鸭架
    /// </summary>
    public class ShangHaiYaJia : YaJia
    {
        public override void Print()
        {
            Console.WriteLine("上海的鸭架子");
        }
    }
```

**使用配置文件改进，程序运行期间读取配置文件决定使用工厂**

```
    /// <summary>
    /// 抽象工厂类
    /// </summary>
    public interface IAbstractFactory
    {
        T Create<T>() where T:class;
    }

    public abstract class AbstractFactoryBase:IAbstractFactory
    {
        //外部程序通过配置获取的“抽象类型/实体类型”映射字典
        protected IDictionary<Type,Type> mapper;
        //构造函数注入
        public AbstractFactoryBase(IDictionary<Type,Type> mapper)
        {
            this.mapper=mapper;
        }
        public virtual T Create<T>() where T:class
        {
            if((mapper==null) || (mapper.Count==0) || (!mapper.ContainsKey(typeof(T))))
                throw new ArgumentException("T");
            Type targetType=mapper[typeof(T)];
            return (T)Activator.CreateInstance(targetType);
        }
    }

    public class Factory:AbstractFactoryBase
    {
        public Factory(IDictionary<Type,Type> mapper):base(mapper)
        {

        }
    }

    //配置文件
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="producta" value="Product.IProductA,Product.ProductA" />
            <add key="productb" value="Product.IProductB,Product.ProductB" />
        </appSettings>
    </configuration>

    //外部配置方法
    public IAbstractFactory AssemblyFactory()
    {
        IDictionary<Type,Type> dic=new IDictionary<Type,Type>();
        string iproduct=ConfigurationManager.AppSettings["producta"].Split(',')[0];
        string product=ConfigurationManager.AppSettings["producta"].Split(',')[1];
        dic.Add(Type.GetType(iproduct),Type.GetType(product));
        ...
        return new Factory(dic);
    }

    //客户端调用
    IAbstractFactory factory=AssemblyFactory();
    IProductA product = factory.Create<IProductA>();//IProductA要创建的产品
    IProductB product = factory.Create<IProductB>();//IProductB要创建的产品
```