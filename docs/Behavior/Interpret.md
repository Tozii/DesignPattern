# 解释器模式

[C#设计模式—解释器模式(Interpreter Pattern) — saville](https://www.cnblogs.com/saville/p/3379533.html)

**定义**

&emsp;&emsp;在软件开发特别是DSL开发中常常需要使用一些相对较复杂的业务语言，如果业务语言使用频率足够高，且使用普通的编程模式来实现会导致非常复杂的变化，那么就可以考虑使用解释器模式构建一个解释器对复杂的业务语言进行翻译。这种做法虽然效率相对较低，但可以允许用户使用自定义的业务语言来处理逻辑，因此在效率不是关键问题的场合还是较为有用的。

> 实现

```
public static class ChineseEnglishDict
    {
        private static Dictionary<string, string> _dictory = new Dictionary<string, string>();

        static ChineseEnglishDict()
        {
            _dictory.Add("this", "这");
            _dictory.Add("is", "是");
            _dictory.Add("an", "一个");
            _dictory.Add("apple", "苹果");
        }

        public static string GetEnglish(string value)
        {
            return _dictory[value];
        }
    }

    public interface IExpression
    {
        void Interpret(StringBuilder sb);
    }

    public class WordExpression : IExpression
    {
        private string _value;

        public WordExpression(string value)
        {
            _value = value;
        }

        public void Interpret(StringBuilder sb)
        {
            sb.Append(ChineseEnglishDict.GetEnglish(_value.ToLower()));
        }
    }

    public class SymbolExpression : IExpression
    {
        private string _value;

        public SymbolExpression(string value)
        {
            _value = value;
        }

        public void Interpret(StringBuilder sb)
        {
            switch (_value)
            {
                case "." :
                    sb.Append("。");
                    break;
            }
        }
    }

    public static class Translator
    {
        public static string Translate(string sentense)
        {
            StringBuilder sb = new StringBuilder();
            List<IExpression> expressions = new List<IExpression>();
            string [] elements = sentense.Split(new char [] {'.'}, StringSplitOptions.RemoveEmptyEntries);
            foreach (string element in elements)
            {
                string[] words = element.Split(new char [] {' '}, StringSplitOptions.RemoveEmptyEntries);
                foreach (string word in words)
                {
                    expressions.Add(new WordExpression(word));
                }
                expressions.Add(new SymbolExpression("."));
            }
            foreach (IExpression expression in expressions)
            {
                expression.Interpret(sb);
            }
            return sb.ToString();
        }
    }

    static void Main(string[] args)
    {
        string englist = "This is an apple.";
        string chinese = Translator.Translate(englist);
        Console.WriteLine(chinese);
    }
```