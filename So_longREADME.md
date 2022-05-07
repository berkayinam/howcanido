--->-> Minilibx <-<---
Öncelikle kütüphanede bulunan OpenGL ve Appkit kavramlarının ne olduğunu araştıralım;
--->->OpenGL & Appkit<-<---


->Cocoa of MacOSX (Appkit)<- : 1980-90 larda Masaüstü işletim sistemi macOS için Apple'ın yerel nesne yönetimli programlama ve uygulama programlama arayüzüdür;

IOS, tvOS ve watchOS için Cocoa Touch adlı jest tanıma,animasyon ve farklı grafik kontrol öğeleri içeren bir API dir;
iPAD iPod Tocuh Apple TV ve apple Watch gibi cihazlarının uygulamalarında kullanılır;

Objective-C Cocoa programlarını basit bir metin editöründe yazmak ve komut satırından veya make'den GNU Compiler Collection (GCC) veya Clang ile manuel olarak oluşturmak da mümkündür;

**Application Kit (AppKit) doğrudan orijinal NeXTSTEP Uygulama Kitinden çıkarılmıştır. Grafiksel kullanıcı arayüzleri oluşturmak ve onlarla etkileşime geçmek için kullanabileceğiniz kod programları içerir.**

-> OpenGL<-
OpenGL (Open Graphics Library) gelişmiş donanım desteğini kullanarak hem iki hem de üç boyutlu grafikleri ekrana çizmek için kullanılan ücretsiz bir grafik uygulama geliştirme arabirimidir;

Windows, Linux, MacOS ve Solaris gibi birçok işletim sisteminde yaygın olarak ve Playstation 3 başta olmak üzere bazı oyun konsollarınca desteklenir. Donanım tarafında ise SGI, ATI, Nvidia veya Intel gibi büyük üreticiler her ekran kartında OpenGL desteği sunar;

1992 yılında ilk taslağı yaratılmış olan bu standart, günümüzde 4.6 sürümüne ulaşmıştır ve 700 üzerinde fonksiyona sahiptir;

*Çoklu platform desteği ve deneysel&bilimsel araçlarda açık arayla önde ve standart olarak kullanılmakta olan platform OpenGL'dir;*

*Birçok programlama dilinde kullanılabilir*
Ada, C, C++, C# (SharpGL adı verilen sınıflar sayesinde), Fortran, Python, Perl ve Java programlama dilleri kullanılarak OpenGL kitaplığından faydalanılabilir;

*Pencere yöneticisinden bağımsızdır*
OpenGL kullanılarak yazılmış programlar, Win32, MacOS ve X-Window pencere yöneticilerinde sorunsuz çalışırlar;

*OpenGL kitaplığıda ekrana grafik çizmeyi standartlaştırmıştır,*
OpenGL sayesinde grafik kartının modeli veya işlemcinin mimarisi gibi donanımsal etkenlerden bağımsız programlama yapılır;*

*Kullanmak istediğiniz her platformda kullanabilirsiniz*,
Platform bağımsızdır;*

Şimdi Makefile için Derleme cümlemize geçelim

mlxNesne dosyaları için, kaynağın mlxprojenizin kökünde adlandırılmış bir dizinde olduğunu varsayarak,
"$(NAME): $(OBJ)
	$(CC) $(OBJ) -Lmlx -lmlx -framework OpenGL -framework AppKit -o $(NAME)"
Unutmadan, Libmlx Dinamik bir kitaplıktır(dylib dynamic library);
Bu yüzden derleme hedefinizle aynı dizinde ihtiyacınız olduğunu unutmayın !;

Peki nedir bu dynamic library :
dynamic-link library / .dll . bana biraz tanıdık geldi :)
Çok kısaca Bir program çalıştırılmak istendiğinde işletim sisteminin yükleyicisi (loader)  devreye girer çalıştırılacak dosya ile birlikte  dosyanın kullanıldığı dinamik kütüphaneleri belleğe yükler  ve böylece çalışma fonksiyon çağrıldığında akışın bellekteki dinamik kütüphane içerisine geçmesiyle yapılır.

Evet Şimdi Minilibx Kütüphnesine geçelim
Basitçe ekrana bir resim göstermek, klavyeden 'D' tuşuna basıldığında resimin bir adım sağa taşınmasını sağlayan kod bloğunu yazalım ve kullanılan metotları açıklayalım;

```
#include "./mlx/mlx.h"
#include <stdlib.h>
#define PL_FT "./player_front.xpm"
#define PL_LT "./player_left.xpm"
#define PL_RT "./player_right.xpm"

typedef struct s_data
{
    void    *mlx;
    void    *mlx_window;
    void    **img;
    int        imgx;
    int        imgy;
    int        position_x;
    int        position_y;
}    t_data;

int    key_event(int keycode, t_data *data)
{
    mlx_clear_window(data->mlx, data->mlx_window);
    if (keycode == 2)//d harfi
    {
        data->position_x+=64;
        mlx_put_image_to_window(data->mlx, data->mlx_window, data->img[2],
                data->position_x, data->position_y);
    }
    if (keycode == 0)//a harfi
    {
        data->position_x-=64;
        mlx_put_image_to_window(data->mlx, data->mlx_window, data->img[1],
                data->position_x, data->position_y);
    }
    return (0);
}

int    main(void)
{
    t_data    *data;
    data = calloc(sizeof(t_data), 1);
    data->img = (void **)malloc(sizeof(void *) * 3);

    data->mlx = mlx_init();
    data->img[0] = mlx_xpm_file_to_image(data->mlx, PL_FT,
            &data->imgx, &data->imgy);
    data->img[1] = mlx_xpm_file_to_image(data->mlx, PL_LT,
            &data->imgx, &data->imgy);
    data->img[2] = mlx_xpm_file_to_image(data->mlx, PL_RT,
            &data->imgx, &data->imgy);


    data->mlx_window = mlx_new_window(data->mlx, 300, 300, "Pencere :D");

    mlx_put_image_to_window(data->mlx, data->mlx_window, data->img[0],
                data->position_x, data->position_y);

    mlx_hook(data->mlx_window, 2, 1L << 0, key_event, data);
    mlx_loop(data->mlx);
}
```
Adım Adım gidelim;
ilk amacımız grafik arayüzü ile konuşup resimi belleğe taşımak ardından ekranda görebilmek ve daha sonrasında hareket ettirmek olsun;

İlk olarak minilibx kütüphanemizi so_long proje sayfamızdan indirip main ile aynı dizine atıyoruz;
Herhangi bir yerde daha rahat kullanmak üzere adını mv komutu ile mlx olarak kısalttım --bu size kalmış;
sonrasında projemize include larımızı dahil ediyoruz, mlx/malloc/calloc için ekstra kütüphanelerimizi tanımladık;
ve kodun daha düzenli durması açısından resimlerimizin yolunu define ile en başta tanımladık;

struct yapısını neden kullandım ve içindeki değişkenler nerede kullanılacak ? 
Fonksiyonlar 4 paramtetreden fazla alamıyor ve so_long gibi bir projede çok basit bir işlemde bile bir fonksyiondan diğerine veri taşırken,
çok fazla veriyi taşımamız gerekebiliyor. bu gibi durumlarda tek tek göndermek yerine bunları bir veri yığını(struct) olarak taşımak işimizi kolaylaştırıyor;

t_data *data diyip bi

int main(void) kısmına gelelim;






















