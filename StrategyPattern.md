# Strategy Pattern

**Strategy** is a behavioral design pattern that enables selecting an algorithm at runtime. Strategy lets the algorithm vary independently from clients that use it.

![](https://upload.wikimedia.org/wikipedia/commons/3/39/Strategy_Pattern_in_UML.png)

The **Context** refers to the **Strategy** interface for performing an algorithm (Strategy.execute()). This will make **Context** independent of how an algorithm is implemented.

The **ConcreteStrategyA** and **ConcreteStrategyB** classes implement the **Strategy** interface

```csharp
public class StrategyPatternWiki
{
    public static void Main(String[] args)
    {
        // Prepare strategies
        var normalStrategy    = new NormalStrategy();
        var happyHourStrategy = new HappyHourStrategy();

        var firstCustomer = new CustomerBill(normalStrategy);

        // Normal billing
        firstCustomer.Add(1.0, 1);

        // Start Happy Hour
        firstCustomer.Strategy = happyHourStrategy;
        firstCustomer.Add(1.0, 2);

        // New Customer
        var secondCustomer = new CustomerBill(happyHourStrategy);
        secondCustomer.Add(0.8, 1);
        // The Customer pays
        firstCustomer.Print();

        // End Happy Hour
        secondCustomer.Strategy = normalStrategy;
        secondCustomer.Add(1.3, 2);
        secondCustomer.Add(2.5, 1);
        secondCustomer.Print();
    }
}

// CustomerBill as class name since it narrowly
// pertains to a customer's bill
// This is the client of the IBillingStrategy, by passing different strategy
// to the constructor, we can change the behaviour in runtime.
// As it promgrams to interface, so we can add as many new strategy as we want 
// without chagning the client code.
class CustomerBill
{
    private IList<double> drinks;

    // Get/Set Strategy
    public IBillingStrategy Strategy { get; set; }

    public CustomerBill(IBillingStrategy strategy)
    {
        this.drinks = new List<double>();
        this.Strategy = strategy;
    }

    public void Add(double price, int quantity)
    {
        this.drinks.Add(this.Strategy.GetActPrice(price * quantity));
    }

    // Payment of bill
    public void Print()
    {
        double sum = 0;
        foreach (var drinkCost in this.drinks)
        {
            sum += drinkCost;
        }
        Console.WriteLine($"Total due: {sum}.");
        this.drinks.Clear();
    }
}

interface IBillingStrategy
{
    double GetActPrice(double rawPrice);
}

// Normal billing strategy (unchanged price)
class NormalStrategy : IBillingStrategy
{
    public double GetActPrice(double rawPrice) => rawPrice;
}

// Strategy for Happy hour (50% discount)
class HappyHourStrategy : IBillingStrategy
{
    public double GetActPrice(double rawPrice) => rawPrice * 0.5;
}
```


