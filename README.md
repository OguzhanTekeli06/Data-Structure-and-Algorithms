# 1.Single Responsibility Principle (SRP) - Tek Sorumluluk Prensibi
Açıklama: Bu prensibe göre, bir sınıf yalnızca bir işten sorumlu olmalıdır. Başka bir deyişle, sınıf bir işin yanı sıra başka sorumluluklar üstlenmemelidir. Bu, kodun daha modüler ve yönetilebilir olmasını sağlar.

Örnek: Bir kullanıcı yönetimi sistemi düşünelim. Kullanıcıları yönetmek için bir UserManager sınıfımız olsun. SRP'ye göre bu sınıf sadece kullanıcı işlemleriyle ilgilenmeli, veritabanı işlemleri veya raporlama gibi başka sorumlulukları olmamalıdır.

```
// SRP'yi ihlal eden bir örnek
public class UserManager
{
    public void AddUser(string username)
    {
        // Kullanıcı ekleme işlemi
    }

    public void DeleteUser(string username)
    {
        // Kullanıcı silme işlemi
    }

    public void GenerateReport()
    {
        // Rapor üretme işlemi - Burada SRP ihlali var!
    }
}

```
Yukarıdaki sınıf SRP'yi ihlal ediyor çünkü hem kullanıcı işlemleri yapıyor hem de raporlama işini üstleniyor. Bunu düzeltmek için şu şekilde ayırabiliriz:

```
// SRP'ye uygun bir çözüm
public class UserManager
{
    public void AddUser(string username)
    {
        // Kullanıcı ekleme işlemi
    }

    public void DeleteUser(string username)
    {
        // Kullanıcı silme işlemi
    }
}

public class ReportManager
{
    public void GenerateReport()
    {
        // Rapor üretme işlemi
    }
}

```
Bu şekilde her sınıf tek bir sorumluluğa sahip olur.

# 2. Open/Closed Principle (OCP) - Açık/Kapalı Prensibi
Açıklama: Bir sınıf, genişletilebilir olmalı ancak değiştirilmemelidir. Yani mevcut sınıflara yeni işlevsellik eklemek için sınıfın üzerine yeni kod yazılabilir, ancak mevcut kodda değişiklik yapılmamalıdır. Bu, kodun esnekliğini artırır ve mevcut kodu bozmadan yeni özellikler eklememize olanak tanır.

Örnek: Bir ödeme sistemini düşünelim. Farklı ödeme yöntemlerini destekleyen bir sınıfımız olsun.

```
public class PaymentProcessor
{
    public void ProcessPayment(PaymentMethod method)
    {
        if (method == PaymentMethod.CreditCard)
        {
            // Kredi kartı ile ödeme işleme
        }
        else if (method == PaymentMethod.PayPal)
        {
            // PayPal ile ödeme işleme
        }
        // Yeni bir ödeme yöntemi eklemek için bu sınıfı değiştirmemiz gerekir
    }
}

```
Bu kod, Open/Closed prensibine uymuyor çünkü yeni bir ödeme yöntemi eklemek için mevcut sınıfı değiştirmek zorundayız. OCP'ye uygun çözüm şu şekilde olabilir:

```
public abstract class PaymentMethod
{
    public abstract void ProcessPayment();
}

public class CreditCardPayment : PaymentMethod
{
    public override void ProcessPayment()
    {
        // Kredi kartı ile ödeme işleme
    }
}

public class PayPalPayment : PaymentMethod
{
    public override void ProcessPayment()
    {
        // PayPal ile ödeme işleme
    }
}

public class PaymentProcessor
{
    public void ProcessPayment(PaymentMethod method)
    {
        method.ProcessPayment();  // Polimorfizm kullanarak yeni ödeme yöntemlerini ekleyebiliriz.
    }
}

```
Burada PaymentMethod sınıfını soyutlayarak ve CreditCardPayment, PayPalPayment gibi alt sınıflar oluşturup, yeni ödeme yöntemlerini sınıfa dahil etmeden ekleyebiliriz.

# 3. Liskov Substitution Principle (LSP) - Liskov Yerine Geçme Prensibi

Açıklama: Türetilmiş sınıflar, temel sınıflarının yerine geçebilecek şekilde tasarlanmalıdır. Yani bir sınıfın alt sınıfları, üst sınıfının yerini alabilmeli ve aynı işlevselliği bozmadan kullanılabilmelidir.

Örnek: Bir şekil sınıfı ve onun alt sınıflarıyla ilgili bir örnek:

```
public class Rectangle
{
    public int Width { get; set; }
    public int Height { get; set; }

    public int Area()
    {
        return Width * Height;
    }
}

public class Square : Rectangle
{
    public new int Width
    {
        set { base.Width = base.Height = value; }
    }
    public new int Height
    {
        set { base.Height = base.Width = value; }
    }
}

```
Yukarıdaki örnekte Square sınıfı, Rectangle sınıfından türemekte, ancak Width ve Height özelliklerini farklı şekilde ele alıyor. Bu, LSP'yi ihlal eder çünkü bir Square nesnesi, Rectangle sınıfının yerine geçemez. LSP'yi ihlal etmeden çözüm şu şekilde olabilir:
```
public abstract class Shape
{
    public abstract int Area();
}

public class Rectangle : Shape
{
    public int Width { get; set; }
    public int Height { get; set; }

    public override int Area()
    {
        return Width * Height;
    }
}

public class Square : Shape
{
    public int Side { get; set; }

    public override int Area()
    {
        return Side * Side;
    }
}

```
Bu şekilde, Rectangle ve Square sınıfları, Shape sınıfını türeterek aynı metodları kullanabilir ve birbirlerinin yerine geçebilirler.

# 4. Interface Segregation Principle (ISP) - Arayüz Ayrımı Prensibi

Açıklama: Bir sınıf, kullanmadığı bir arayüzü uygulamamalıdır. Yani, büyük ve kapsamlı arayüzler yerine, daha küçük ve daha özel arayüzler tercih edilmelidir.

Örnek: Bir aracın özellikleriyle ilgili bir arayüz düşünelim:
```
public interface ICar
{
    void Drive();
    void Fly();
}

```
Buradaki ICar arayüzü, uçma yeteneğine sahip olmayan araba sınıflarını zorlayabilir. ISP'ye uygun çözüm şu şekilde olabilir:
```
public interface IDriveable
{
    void Drive();
}

public interface IFlyable
{
    void Fly();
}

public class Car : IDriveable
{
    public void Drive()
    {
        // Araba sürme işlemi
    }
}

public class Airplane : IDriveable, IFlyable
{
    public void Drive()
    {
        // Uçak sürme işlemi
    }

    public void Fly()
    {
        // Uçma işlemi
    }
}

```
Burada her sınıf yalnızca ihtiyacı olan arayüzü uygular, böylece daha temiz ve esnek bir yapı elde edilir.

# 5. Dependency Inversion Principle (DIP) - Bağımlılıkların Tersine Çevrilmesi Prensibi

Açıklama: Yüksek seviyeli modüller, düşük seviyeli modüllere bağlı olmamalıdır. Her iki tür modül de soyutlamalara bağlı olmalıdır. Yani, detaylara bağlılık yerine soyutlamalar kullanılmalıdır.

Örnek: Bir veri tabanı sınıfı düşünelim:

```
public class DatabaseService
{
    public void SaveData(string data)
    {
        // Veriyi kaydetme işlemi
    }
}

public class DataProcessor
{
    private DatabaseService _databaseService;

    public DataProcessor()
    {
        _databaseService = new DatabaseService();  // Bağımlılık doğrudan verilmiş
    }

    public void ProcessData(string data)
    {
        // Veriyi işler
        _databaseService.SaveData(data);
    }
}

```
Yukarıdaki örnekte DataProcessor sınıfı, doğrudan DatabaseService sınıfına bağlıdır. Bu, DIP'yi ihlal eder. DIP'ye uygun çözüm şu şekilde olabilir:
```
public interface IDataService
{
    void SaveData(string data);
}

public class DatabaseService : IDataService
{
    public void SaveData(string data)
    {
        // Veriyi kaydetme işlemi
    }
}

public class DataProcessor
{
    private readonly IDataService _dataService;

    public DataProcessor(IDataService dataService)
    {
        _dataService = dataService;
    }

    public void ProcessData(string data)
    {
        // Veriyi işler
        _dataService.SaveData(data);
    }
}

```
Bu çözümde DataProcessor sınıfı, IDataService soyutlamasına bağımlıdır, dolayısıyla hangi veri kaydetme sınıfının kullanılacağına dışarıdan karar verilebilir.

