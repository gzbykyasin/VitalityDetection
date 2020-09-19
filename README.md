# VitalityDetection
Canlılık Tespiti
Şuanda kullanılan yüz tanıma sistemlerinde kullanıcının canlı olup olmadığını kontrol etme işlemi için birçok çalışma yapılmış ve kullanılıyor. 
Fakat benim geçenlerde incelediğim bir makeledeki canlılık tespiti algoritması ve mantığı gayet hoşuma gitti "Uzaktan Fotopletismografi (RPPG)". 
Fotopletismografi genel olarak, kalbin kan pompalamasıyla birlikte deride oluşan küçük renk değişimlerinin incelenmesiyle dolaşım sistemine dair bilgiler elde edilmesine dayanan tıbbi ve biyometri sistemlerinin bir çalışma alanı.
Makalenin linki burada dursun ben biraz konuyu anlatayım size. (https://lnkd.in/davDe7v )
Alın veya yanak bölgesinin kan pompalaması sırasında meydana gelen renk değişimlerini ( RGB renk uzayı için G referans alınıyor, HSV renk uzayı için H referans alınıyor) en hassas şekilde gösterdiği bulunuş. 
DLIB ile yüzün 68 bölgesini bulan derin öğrenme tabanlı yüz tespiti işlemimiz sonucunda alın bölgesini dinamik birşekilde bulunur ve ard arda çektiğimiz diğer görüntüler ile karşılaştırılır. 
Görüntüden histogramı çıkartıp OTSU ile filtreleyerek fazlalıklardan kurtulunur. Gaussian filtrelemesi ile alın bölgesinde istenmeyen gürültü pixellerden temizlenir.
Daha sonra Destek Vektör Makinaları (SVM) kullanılır FFT dönüşümü uygulanır ve G kanalı baz alarak zamansal olarak değişikliği izleyerek kalp atışını çıkartılır. 
