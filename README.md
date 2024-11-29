#1 .Single Responsibility Principle (SRP) - Tek Sorumluluk Prensibi
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

#2. Open/Closed Principle (OCP) - Açık/Kapalı Prensibi
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


