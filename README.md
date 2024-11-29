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




# Monolithic Mimari,
Monolithic Mimari, yazılım geliştirme dünyasında, uygulamanın tüm bileşenlerinin tek bir bütün olarak çalıştığı ve dağıtıldığı bir mimari yaklaşımdır. Yani, monolitik bir uygulama, bir "blok" olarak tasarlanır ve tüm işlevsellikler bu tek bir uygulama içerisinde birleştirilir.

Monolitik mimaride, tüm modüller ve bileşenler aynı uygulama içinde yer alır ve genellikle aynı veritabanını veya veri kaynaklarını kullanır. Bu tür bir uygulama, tüm işlevlerin merkezi bir yapı altında toplandığı bir yazılım çözümüdür.

## Monolitik Mimari Nasıl Çalışır?
Monolitik bir uygulama, genellikle aşağıdaki özelliklere sahip olur:

1. Tek Bütün Olarak Çalışır: Uygulamanın tüm işlevleri, tek bir büyük uygulama içinde birleştirilir. Bu, kullanıcı arayüzü (frontend), iş mantığı (business logic), veri erişimi (data access) ve diğer bileşenlerin tek bir yazılım içinde yer alması anlamına gelir.

2. Tek Veritabanı: Genellikle monolitik uygulamalar, tüm bileşenlerin aynı veritabanına eriştiği merkezi bir veritabanına sahip olur. Tüm veriler ve veritabanı işlemleri tek bir yerde yönetilir.

3. Tek Dağıtım Birimi: Uygulama, tek bir dağıtım birimi olarak ele alınır. Uygulamanın tamamı tek bir dosya veya kod tabanı olarak dağıtılır ve güncellenir.

4. Tek Teknoloji Yığını: Monolitik uygulamalarda, tüm bileşenler genellikle aynı teknoloji yığını (programlama dili, framework, vb.) ile geliştirilir. Örneğin, bir monolitik uygulama tüm işlevlerini Java ile yazabilir veya C# ile geliştirilmiş olabilir.

## Monolitik Mimari Örneği
Bir e-ticaret uygulaması düşünelim. Monolitik bir yapıda bu uygulama şu bileşenleri içerebilir:
* Kullanıcı Yönetimi: Kullanıcılar, kayıt olma, giriş yapma, profil yönetimi.
* Ürün Yönetimi: Ürünleri listeleme, ekleme, güncelleme.
* Sipariş Yönetimi: Kullanıcıların siparişlerini yönetme.
* Ödeme Sistemi: Ödeme işlemleri ve takibi.
* Stok Yönetimi: Ürün stoğu kontrolü ve güncelleme.
  Tüm bu bileşenler tek bir uygulama içinde yer alır ve uygulamanın her bir modülü birbirine sıkı bir şekilde bağlıdır.

  ## Monolitik Mimari Avantajları
1. Basitlik: Monolitik uygulamalar, ilk başta geliştirilmesi ve yönetilmesi açısından daha basit olabilir. Tek bir kod tabanı ve veri kaynağı olduğu için yapı daha az karmaşıktır.

2. Kolay Test Edilebilirlik: Tüm bileşenler tek bir uygulama içinde olduğu için, uygulamanın her bir parçası arasında entegrasyon kolaylıkla test edilebilir.

3. Tek Dağıtım Süreci: Uygulamanın tamamı tek bir dosya veya kod tabanı olarak dağıtıldığından, dağıtım süreci daha basit olabilir. Sadece bir uygulama güncellenir.

4. Geliştirme Hızı (Başlangıçta): Monolitik yapılar başlangıçta hızlı bir şekilde geliştirilebilir, çünkü tüm bileşenler aynı uygulama içerisinde yer alır ve iletişim için ayrı bir mekanizma gerekmez.

## Monolitik Mimari Zorlukları
1. Karmaşıklık ve Büyüme: Uygulama büyüdükçe, monolitik yapılar yönetilmesi zor hale gelebilir. Çünkü tüm bileşenler birbirine sıkı bir şekilde bağlıdır ve bir parçada yapılacak değişiklik, tüm sistemi etkileyebilir.

2. Dağıtım Zorlukları: Monolitik bir uygulama büyüdükçe, tüm uygulamanın yeniden dağıtılması gerekebilir. Bu, çok büyük ve karmaşık sistemlerde zaman alıcı ve verimsiz olabilir.

3. Veri Tabanı Bağımlılığı: Tüm bileşenler aynı veritabanını kullandığı için, veritabanı şemasında yapılacak herhangi bir değişiklik, tüm uygulamayı etkileyebilir.

4. Performans Sorunları: Tüm bileşenlerin aynı uygulama içinde olması, performans sorunlarına yol açabilir. Bir modülde meydana gelen performans problemi, tüm uygulamayı etkileyebilir.

5. Zor Ölçeklenebilirlik: Monolitik uygulamalar, genellikle sadece tüm uygulamanın ölçeklendirilmesine olanak tanır. Örneğin, sadece ödeme işlemleri için daha fazla kaynağa ihtiyaç duyulsa bile, tüm uygulamanın ölçeklendirilmesi gerekir. Bu, kaynakların verimli kullanılmasını zorlaştırabilir.

6. Teknolojik Bağımlılıkları: Monolitik mimaride tüm bileşenler aynı teknoloji ile geliştirilir. Eğer teknoloji yığını değiştirilmek istenirse, tüm uygulamanın yeniden yazılması gerekebilir.

## Monolitik Mimarinin Büyüyen Uygulamalarda Karşılaştığı Sorunlar
* Yavaş Geliştirme: Monolitik uygulamalar büyüdükçe geliştirme ve bakım süreçleri yavaşlayabilir. Küçük bir değişiklik yapmak bile büyük bir test ve dağıtım sürecini gerektirebilir.

* Hata Yayılma: Bir modüldeki hata, tüm uygulamayı etkileyebilir. Bu da uygulamanın güvenilirliğini düşürebilir.

* Zor Yönetim: Tek bir büyük kod tabanı, ekipler arasında işbirliğini zorlaştırabilir. Özellikle birden fazla ekip çalışıyorsa, her ekip monolitik yapıyı doğru şekilde yönetmekte zorlanabilir.


# Microservice Mimari
Microservices (Mikroservis) Mimarisi, yazılım uygulamalarını bir dizi bağımsız, küçük, ve modüler servise ayırmayı amaçlayan bir yazılım mimarisi modelidir. Bu servislere "mikroservisler" denir ve her biri genellikle bir işlevi yerine getirir. Mikroservisler, uygulamanın her bir bölümünü küçük, bağımsız ve yönetilebilir parçalara böler, böylece her bir servis kendi başına geliştirilebilir, dağıtılabilir ve yönetilebilir.

Mikroservis mimarisi, modern yazılım geliştirme dünyasında büyük ve karmaşık uygulamaları daha verimli, esnek, ölçeklenebilir ve sürdürülebilir hale getirebilmek için sıklıkla kullanılır.

## Mikroservis Mimarisi Nedir?
Mikroservis Mimarisi, büyük ve monolitik uygulamaları daha küçük, birbirinden bağımsız olarak çalışan servisler haline getiren bir yaklaşımdır. Bu servisler, birbirleriyle haberleşmek için genellikle API'ler (özellikle RESTful API'ler veya gRPC) kullanır. Her mikroservis, kendi veritabanına sahip olabilir, ancak bu zorunlu değildir; bazı durumlarda, mikroservisler ortak bir veritabanı da kullanabilir.

## Mikroservislerin Temel Özellikleri
1. Bağımsızlık: Her mikroservis, kendi başına bağımsız olarak çalışır ve geliştirilebilir. Diğer servislere veya bileşenlere olan bağımlılığı minimum düzeyde tutulur. Bu sayede, bir servisin değiştirilmesi diğer servisleri etkilemez.

2. Kendi Veritabanı: Mikroservislerin çoğu, kendi veri yönetim sistemine (veritabanı) sahip olabilir. Bu, her servisin kendi veri modelini kullanmasını sağlar ve veri yönetimini daha esnek hale getirir.

3. Otonomi: Mikroservisler, dışarıdan gelen istekleri işleyebilen kendi işlevlerini yerine getirir. Her mikroservis genellikle belirli bir işlevi yerine getirir (örneğin kullanıcı yönetimi, ödeme işlemleri, ürün yönetimi vb.).

4. Küçük ve Odaklı: Mikroservisler küçük ve belirli bir işlevi yerine getiren uygulama birimleridir. Her mikroservisin tek bir sorumluluğu vardır (Single Responsibility Principle – SRP).

5. Teknoloji Bağımsızlığı: Mikroservisler, farklı teknolojilerle yazılabilir. Örneğin, bir mikroservis Java ile yazılırken, bir diğer mikroservis Node.js ile yazılabilir. Bu, geliştiricilerin farklı teknolojileri kullanarak en uygun çözümü geliştirmelerine olanak tanır.

6. Ölçeklenebilirlik: Mikroservis mimarisi, belirli bir servisin ihtiyaç duyduğu kaynakları daha kolay ölçeklendirebilmenizi sağlar. Örneğin, yüksek trafikli bir ödeme servisini, yalnızca bu servisi ölçeklendirerek rahatça yönetebilirsiniz.

7. Bağımsız Dağıtım: Mikroservisler bağımsız olarak dağıtılabilir ve güncellenebilir. Bu, uygulamanın kesintisiz olarak çalışmasına olanak tanır ve geliştirme sürecini hızlandırır.

8. Hizmet Keşfi: Mikroservisler arasında iletişimi sağlamak için genellikle bir hizmet keşif (Service Discovery) mekanizması kullanılır. Bu, servislerin birbirlerini bulup iletişim kurabilmelerine olanak tanır.

9. API Gateway: Mikroservisler arasında yönlendirme yapmak ve çeşitli servislerin birleştirilmesi için bir API Gateway kullanılabilir. API Gateway, istemciden gelen istekleri uygun mikroservislere yönlendirir.

## Mikroservislerin Avantajları

1. Modülerlik: Her mikroservis, belirli bir işlevi yerine getirir ve bu işlev bağımsız bir birim olarak geliştirilir. Bu, geliştirme sürecini daha yönetilebilir hale getirir.

2. Hızlı Geliştirme ve Dağıtım: Mikroservislerin bağımsız olarak geliştirilmesi, yazılımın daha hızlı bir şekilde teslim edilmesini sağlar. Her bir servis, ekipler tarafından bağımsız olarak geliştirilebilir, bu da geliştirme sürecini hızlandırır.

3. Esneklik ve Teknoloji Seçimi: Farklı mikroservisler, farklı teknolojilerle yazılabilir. Her servis, kendi ihtiyacına göre uygun teknolojilerle oluşturulabilir. Bu, teknoloji bağımsızlığı sağlar.

4. Ölçeklenebilirlik: Mikroservis mimarisi, uygulamanın yalnızca ihtiyaç duyulan bölümlerini ölçeklendirmeye imkan tanır. Yüksek trafikli bir servisi ayrı olarak ölçeklendirebilir, diğer servisleri etkilemeden performansı artırabilirsiniz.

5. Hata Yalıtımı: Mikroservisler, hata izolasyonu sağlar. Eğer bir mikroservis başarısız olursa, bu genellikle diğer mikroservisleri etkilemez, çünkü her mikroservis bağımsızdır. Bu, sistemin genel güvenilirliğini artırır.

6. Kolay Bakım: Mikroservisler küçük ve bağımsız olduğu için bakım ve geliştirme süreçleri daha basittir. Ayrıca, her mikroservisin güncellenmesi, tüm sistemin güncellenmesine gerek kalmadan yapılabilir.

## Mikroservislerin Zorlukları

1. Dağıtık Sistem Karmaşıklığı: Mikroservisler, birden fazla bileşenle çalıştıkları için dağıtık bir sistem oluştururlar. Bu, servisler arası iletişim, hata yönetimi ve veri tutarlılığı gibi problemleri beraberinde getirebilir.

2. Veri Yönetimi: Mikroservislerde her servisin kendi veritabanı olması, veri tutarlılığı ve entegrasyonu sorunlarına yol açabilir. Bu durumda, veri yönetiminde dikkatli olunması gerekir.

3. İletişim ve Ağ: Mikroservislerin birbirleriyle iletişim kurması gerektiği için ağ trafiği artar. Servisler arasında veri alışverişinin verimli bir şekilde yapılması önemlidir. Ayrıca, API yönetimi ve güvenliği de önemli bir sorundur.

4. Yönetim ve İzleme: Birden fazla mikroservisin yönetilmesi ve izlenmesi, geleneksel monolitik yapılara göre daha karmaşık olabilir. Bu yüzden, mikroservislerin yönetimi için güçlü bir izleme (monitoring) ve loglama altyapısı gereklidir.

## Mikroservis Mimarisi Nasıl Çalışır?
Mikroservisler genellikle şu şekilde çalışır:

1. API Gateway: İstemciler, genellikle bir API Gateway üzerinden mikroservislere erişir. API Gateway, gelen istekleri doğru mikroservise yönlendirir ve bazen ek işlemler (kimlik doğrulama, veri dönüştürme gibi) yapar.

2. Bağımsız Mikroservisler: Her mikroservis, belirli bir işlevi yerine getirir ve kendi veri tabanına sahip olabilir. Servisler, genellikle RESTful API'ler veya gRPC gibi protokoller üzerinden birbirleriyle iletişim kurar.

3. Servis Keşfi: Mikroservisler, dinamik bir ortamda çalışıyorsa, birbirlerini bulabilmek için bir servis keşif mekanizması kullanılır. Bu, servislerin adreslerini otomatik olarak keşfetmesini sağlar.

4. Veri Yönetimi: Her mikroservisin kendi veritabanı olabilir, ancak bazı durumlarda ortak bir veri kaynağı da kullanılabilir. Mikroservisler, kendi verilerini kontrol eder ve işleyebilir.

5. Dağıtım ve İzleme: Mikroservisler bağımsız olarak dağıtılır ve sürekli izlenir. Her mikroservis kendi loglarına ve izleme metriklerine sahip olabilir.

## Mikroservis Mimarisi Örneği
Diyelim ki bir e-ticaret uygulaması geliştiriyorsunuz. Mikroservis mimarisi ile bu uygulama şu şekilde modüllere ayrılabilir:

1. Kullanıcı Yönetimi Servisi: Kullanıcı kaydı, giriş ve profil yönetimi.
2. Ürün Yönetimi Servisi: Ürünleri listeleme, ekleme, güncelleme, silme işlemleri.
3. Ödeme Servisi: Ödeme işlemlerini yönetir.
4. Sipariş Yönetimi Servisi: Siparişlerin alınması, işlenmesi ve takibi.
5. Stok Yönetimi Servisi: Ürün stoğu ve stok takibi.
6. Raporlama Servisi: Satış verileri ve raporların üretimi.
Her bir mikroservis bağımsız olarak geliştirilebilir ve API'ler aracılığıyla birbirleriyle iletişim kurabilir.

Sonuç  
Mikroservis mimarisi, büyük ve karmaşık uygulamaların daha verimli ve sürdürülebilir bir şekilde yönetilmesine olanak tanır. Ancak mikroservislerin avantajları kadar zorlukları da vardır. Uygulamanın gereksinimlerine ve hedeflerine göre mikroservis mimarisi dikkatlice planlanmalı ve uygun altyapı ile desteklenmelidir.
