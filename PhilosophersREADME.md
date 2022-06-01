--->-> pthread <-<---
POSIX Kütüphanesi olarak geçmektedir, ismini PosixTHREAD pthread olarak almıştır;
Bilgisayar Bilimlerinde İşletim Sisteminde konusunda kullanılan bir terimdir;
*Bir process'in birden fazla işi aynı anda yapmasını sağlayan yapılara thread denir;*
CPU anlık olarak tek iş çalıştırabilir. bu problemin aşılması için 2 ayrı teknolojiden bahsedilebilir,
kendiliğinden çatallanan Fork / İşlemci Zamanlama Algoritması (CPU Scheduling algorithm);

-->İşlemci zamanlama algoritması da kendi içinde kesintili(preemptive) ve kesmeyen (nonpreemptive) algoritmalar olarak 2 ye ayrılır.<--
Thread ın var olması için bir işlemin var olması gerekir,eğer thread bir işlemin altında çalışıyorsa bu işlemin içerisindeki bütün haklara sahiptir.
Dolayısıyla bir işlem(process) tarafından üretilen bütün işlemler, bu işlemin tüm kaynaklarına erişebilirler(RAM&CPU&I/O) buna Paylaşılmış hafıza (Shared memory) denir;
paylaşılan değişkenlere de Shared variable denir;
*Threadlar bir kaynağı kullanırken çeşitli problemlerden dolayı birbiriyle karışabilir,uyumsuz davranışlar gösterebilir ve senkronize olarak hareket etmede sorun yaşayabilir. bunu engellemek için mutex veya semaphore yapılarına başvurulur;*

pthread_create(&variables) : Thread oluşturmak için kullanılır;
pthread_exit(&variable): Thread kendi akışını sonlandırmak için kullanılır;
pthread_join(&variables): Threadi bekletir uyutur;
pthread_detach(&variable): Threadi oluşturduktan sonra verilir. thread işlemi bittiğinde yok edilmek üzere işaretlenir;
pthread_cancel(&variable): Threadi iptal eder. 



--->-> Mutex (MUTual EXclusion) <-<---
*Ben bir iş yaparken başka hiç kimse başka bir iş yapmasın mantığı ile çalışır;*
Farklı Threadler tarafından paylaşılan her bir kaynak için *kaynağa olan erişimi düzenlemek* üzere bir mutex yaratılır;

Critical Section : Paylaşılan kaynağa erişim yapılan kod bölgesi Critical section olarak adlandırılır;
kaynakla işi olan thread, mutex sahipliği almaya(Acquire) çalışır, Mutex o anda başka bir thread tarafından tutulmuyorsa Thread Mutexi alır ve Critical Section a girerek kaynağı kullanır;

*Mutex Critical Section yönetimini sağlamak üzere tasarlandığı için kendilerini Release eden Thread'ın Thread olması gerekliliğini tutar. Ama semaphore için bu kısıtlama yoktur;*

pthread_mutex_t Veri yığını (struct) olarak isimlendirilir;
pthread_mutex_init(&variables) : yeni bir mutex oluşturmak için kullanılır;
pthread_mutex_destroy(&variable) : verilen adresteki mutex kilidini siler ve yok eder;
pthread_mutex_lock(&variable) : verilen parametredeki değişkeninin kilitlenmesini sağlar.
pthread_mutex_unlock(&variable) : verilen parametredeki değişkeninin kilitlenen mutex kilidini açar;
pthread_mutex_trylock(&variable) : Kilit koymadan konulup konulamayacağını sorgular. Örneğin kilitlenme ihtimali olmayan bir mutex için kilit konulur ve kilit açılana kadar (unlock) thread işlemeye devam eder;



--->-> sem_t <-<---
Semaphorelar sem_t türündedir,Eş zamanlı programlamada(concurrent programming) kullanılır;

*Mutexler threadlar arası kullanılırken semaphore yapısı sadece thread değil aralarında sinyalleşme gereken Üretici-Tüketici (Producer-Consumer) senaryolarında sinyalleşme açısından da kullanılır* ve leziz bir çözümdür;
Semaforler üzerinde kilitleme ve kilidi kaldırma yerine semafor değerini arttırma ve azaltma şeklinde işlemler yapılabilir, mutexten temel farkı budur;
*Semaforların çalışması sırasında bölünmezlik (atomicity) ön plandadır*. Yani bir semafor’un içerisinde yapılan birden fazla iş, program tarafından sanki tek bir iş gibi algılanmalı ve araya başka işin girmesine izin verilmemelidir;
fakat semafor üzerinde sem_unlink() uygulanmamış ise program 1 kere çalışır. 2.seferde semaforlar kilitli kaldıgı için sonsuz döngüye girer;

P Ve V işlemi olarak 2 işleme sahiptir : 
P işlemi bekleme, uyku veya aşağı çalışma olarak adlandırılır, -1
V işlemi ise sinyal, uyanma ve yukarı işlem olarak adlandırılır: +1

sem_unlink() : ile semafor tamamen silinmek üzere işaretlenir bu semaforu kullanan tüm processler sem_close() komutunu kullandıktan sonra semafor tamamen silinir;
sem_open(): semafor oluşturmak veya semafora erişebilmek için kullanılır;
sem_close():çekirdek ve semaforun ilişkisini keser, semaforu kapatır;
sem_post(): ile semaforun değeri arttırılırken,
sem_wait(): fonksiyonu ile semaforun değeri azaltılır;



--->-> pid_t & fork<-<---
Process, programın belleğe yüklenerek çalıştırılmasıyla ortaya çıkan yapıdır;
terminale "ps" komutunu yazarak çalışan processleri görebilirsiniz;

her process'in id değeri vardır;
*id değeri pid_t türündedir;*
ve her id *tekildir;*

Fork ? Çalışmakta olan process in kopyasını oluşturur;
Yeni oluşan Process e *child process*,
kopyası alınan process e *parent process* denir;

Fork yapıldığında parent process in Handle , Bellek alanın *kopyası* alınır;
getpid(): process in id değerini döndürür;
getppid(): Üst process in id değerini döndürür;
Handle : Bir dosyayı hangi process'in açık tuttuğunu söyleyen Sysinternals Tooludur;
fork() : Çağrıldığı andan itibaren ana processin bir kopyası oluşturulur.
-->Üst processte fork, yeni yaratılan alt processin handle değeri ile geri döner. Alt processte ise 0 ile geri döner<--



--->->Deadlock & Starvation & Data Race<-<---
Kilitlenme(Deadlock): İki veya daha fazla işlem, sadece bekleyen bir işlemin neden olabileceği bir olayı sonsuza kadar bekler.
Yüksek öncelikli process gelince düşük öncelikli process’e bakmaz. Ve hep yüksek öncelikli gelirse; düşük öncelikli process bir türlü sıra gelmez.
Ve deadlock olur;

Starvation :(Açlık) Sınırsız bloklanma; semafor beklenme listesinde bekleyen bir işlemin hiçbir zaman listeden silinmemesi durumudur. Last in first of sırasıyla çalışır.Philisophers dünyaca ünlü bir starvation algoritma problemidir;

Data Race : Race Condition birden çok thread'in paylaşılan bir hafıza alanına aynı anda ulaşıp o alanı değiştirmesiyle meydana gelir;
Örnek olarak iki Thread eş zamanlı olarak bir while döngüsünde sabit bir değişkeni arttırsın,
Thread 1 veriyi okuyor / thread 2 veriyi okuyor
thread 1 veriyi arttırıyor / thread 2 veriyi arttırıyor
ikisi de aynı alana yazdıkları için veri 2 kez değil 1 kez yazmış oluyor;
çare thread loc :D ;



--->-> sys/time.h & time.h<-<---
struct timeval yapısı süreyi göstermekte kullanılır . bu yapı sys/time.h olarak bildirilmiştir;
long int tv_sec sürenin tam saniyelerden oluşan kısmını içerir (ondalık ayracın solundaki kısım);
long int tv_usec sürenin saniyeden küçük kısmını gösterir (ondalık ayracın sağındaki kısım); 
tv_sec Microsecond tv_usec Nanosecond cinsinden çalışır;

struct timespec ise time.h adı altında kullanılır;
aynı şekilde tv_sec ve tv_nsec olarak kullanılır;
double difftime(time_t zaman1, time_t zaman2) : zaman1 ve zaman2 mutlak zamanları arasındaki süreyi double türünce saniye cinsinden bir değer olarak hesaplar;

struct timeval kullanan işlevler gettimeofday() ve settimeofday() işlevredir. biz de projemizde gettimeofday() kullanacağız;
fonksiyonlar başarılı çalışırsa 0 döndürür aksi taktirde -1; 
gettimeofday(),settimeofday(): şu andaki zamanı ekrana basmak için ve saat & zaman dilimini ayarlamak için kullanılır;


-->Örnek<--
Mutex yapısına göre 2 araç kapasiteli 1 boş oroparkımız olsun (ready_to_run);
bu otoparkta da 1 drift pisti olsun(running);
araçların görevi toplamda 10 tur drift atıp otoparktan çıkmak (finish);
araçların drift atma tur sayısını sayan 1 adam olsun; (data/variable);
1 araç 1 kere piste çıktığında sadece 1 tur drift atsın(eat_time / drift_time);
araçların o an drift atıp atmadığını kontrol eden süreci mutex ve semaphore ile ayrı ayrı handle edeceğiz;

arabalar eğer uzun süre drift atmazsa aküleri bitip bozulacak (starvation);
arabalar aynı anda drift atmaya çalışırsa drift tur sayısını sayan adam 2 arabaya da aynı anda bakamayacağından dolayı 10 yerine yanlış bir değer sayabilir (Data Race);

1.aracı oluşturalım pthread_create() ve otoparktan çıktıktan sonra yok olmak üzere pthread_detach() fonksiyonu ile işaretleyelim;
2.aracı oluşturalım pthread_create() ve otoparktan çıktıktan sonra yok olmak üzere pthread_detach() fonksiyonu ile işaretleyelim;

1.Mutex i oluşturalım. pthread_mutex_init() bu mutex 1.arabanın otoparkta olup olmadığını anlamak için oluşturduk;
2.Mutex i oluşturalım. pthread_mutex_init() bu mutex 2.arabanın otoparkta olup olmadığını anlamak için oluşturduk;
3.Mutexi i oluşturalım pthread_mutex_init() pistin kullanıp kullanılmayacağı durumu için; 

1. ve 2.araç otoparkta olduğu için mutex_lock() kullanıp arabaları kilitliyoruz çünkü daha pistte değiller (ready_to_run);
3.mutex unlock() durumuna getirelim çünkü pistimiz boş(işlemcide çalışan process yok);
bir while döngüsü düşünelim (piste göz atma) bu döngü içinde 1. ve 2.araçlar 3.mutexin durumunu kontrol edicek ve açık(unlock) gören ilk mutex 3.mutexi kitleyecek (ilk gören araç piste girdi);
//bir sıralama yok mutexin kilidi unlock olduğunda ilk gören araç defalarca art arda piste çıkabilir. bu durumda diger aracın aküsü bitebilir bu durumu da starvation ile açıklarız//
//eğer sıra sıra drift atmalarını isteseydik. mutexlere rank(sıra numarası) vermemiz gerekirdi. ve o sıra numarasına göre piste çıkmaları sağlanırdı//

piste çıkan araba 1 tur driftini tamamladıktan sonra 3.mutexin kilidini açıp(unlock) kendi kilidini kapatıcak(lock);
bir while da ölüm kontrolü için , diğer arabanın piste çıkmadığı süre zarfı saklanacak ve eğer akü bitme süresine eşit veya büyük olursa araba bozulucak (starve to death);

10 tur bittikten sonra data == 0 olunca mutexleri patlatıp(pthread_mutex_destroy()) processleri öldürüyoruz(exit(1));

aynı örneği semaphore ile yapmış olsaydık mutex lock yerine sem_wait(); mutex unlock yerine sem_post kullanmış olucaktık;
mutex kullanılan her yerde semaphore kullanılabilir ama semaphore kullanılan her yerde mutex kullanılmaz;
ortak bir veri kaynağına erişmekte mutex-thread yapısı daha performanslıyken. processler ile çalışırken semaphorelar zorundur;

Semaphoreların zorunluluğunu da uyuyan berber örneğine bakarak anlayabilirsiniz; berber örneğinde mutex kullanılamaz ama semaphore kullanılabilir;
https://bilgisayarkavramlari.com/2012/04/17/sleeping-barber-uyuyan-berber-problemi/;
