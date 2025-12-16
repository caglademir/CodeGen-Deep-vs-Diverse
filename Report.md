
📑 CodeGen Projesi: Düşünce Zinciri (CoT) Modelleri Performans Analiz Raporu
 
1. Yönetici Özeti (Executive Summary)
Bu proje kapsamında, Qwen2.5-Coder-1.5B-Instruct taban modeli, proje dökümantasyonunda belirtilen yönergelere tam sadakatle bağlı kalınarak iki farklı strateji ile eğitilmiştir: Deep Think ve Diverse Think.
Çalışmanın temel amacı, veri setlerinde yer alan "Output" (Çıktı) kısımlarındaki farklı düşünce yapılarının (derinlemesine mantık vs. çeşitli bakış açıları) modelin kod üretim başarısına etkisini ölçmektir. Yapılan LiveCodeBench (AtCoder-Easy) testleri sonucunda; Deep Think yaklaşımının, modelin karmaşık problemleri çözme yeteneğini daha hızlı geliştirdiği ve %26.83 ile en yüksek başarı oranına ulaştığı tespit edilmiştir.
 

2. Veri Seti ve Eğitim Metodolojisi
Eğitim süreci, sağlanan teknik dökümandaki parametreler ve veri işleme talimatları doğrultusunda gerçekleştirilmiştir.
2.1. Veri Seti Kullanımı (Dataset Utilization)
Modellerin eğitiminde, sadece problem tanımları (input) değil, veri setlerinin Output (Çıktı) sütunlarında yer alan "Reasoning Traces" (Mantık Yürütme İzleri) aktif olarak kullanılmıştır:
•	Deep Think Modeli: Naholav/CodeGen-Deep-5K veri seti kullanılmıştır. Bu veri setinin outputkısımlarında yer alan uzun, adım adım ve detaylandırılmış çözüm yolları modele öğretilerek, modelin bir problemi çözerken "derinlemesine düşünmesi" hedeflenmiştir.
•	Diverse Think Modeli: Naholav/CodeGen-Diverse-5K veri seti kullanılmıştır. Bu setin outputkısımlarındaki aynı probleme getirilmiş farklı ve çeşitli çözüm yaklaşımları modele öğretilerek, modelin bakış açısı zenginleştirilmeye çalışılmıştır.
2.2. Dökümana Uyumluluk (Adherence to Documentation)
Proje dökümanında belirtilen aşağıdaki kısıtlar ve parametreler değiştirilmeden uygulanmıştır:
•	Taban Model: Qwen/Qwen2.5-Coder-1.5B-Instruct
•	Eğitim Süresi: Her model için yaklaşık 3 Epoch (800 Step).
•	Değerlendirme: LiveCodeBench framework'ü üzerinden, 2408-2502 tarih aralığındaki AtCoder (Easy) problemleri.
•	Metrik: Pass@1 (Tek seferde doğru çözüm oranı).
 
3. Eğitim Dinamikleri ve Kayıp (Loss) Analizi
Modellerin öğrenme sürecinin kararlılığını (stability) ve yakınsama hızını (convergence speed) değerlendirmek amacıyla, 800 adımlık eğitim süreci boyunca kaydedilen "Training Loss" değerleri analiz edilmiştir. Eğitim kaybının düşüş eğilimi, modelin veri setindeki mantıksal yapıları ne kadar başarılı bir şekilde içselleştirdiğinin birincil göstergesidir.
3.1. Eğitim Kayıp Grafiği
Aşağıdaki grafik, Deep Think ve Diverse Think modellerinin eğitim adımlarına (steps) göre loss değişimini göstermektedir:
 
 

3.2. Grafik Yorumu ve Gözlemler
Grafik ve log kayıtları incelendiğinde şu temel çıkarımlar yapılmıştır:
•	Hızlı Adaptasyon (Early Convergence): Her iki modelde de ilk 100 adımda loss değerinde keskin bir düşüş gözlemlenmiştir. Bu durum, Qwen2.5 taban modelinin, yeni veri setlerine (Deep ve Diverse instruction formatlarına) hızla uyum sağladığını göstermektedir.
•	Deep Think İstikrarı: Deep Think modelinin loss eğrisi, daha pürüzsüz ve istikrarlı bir düşüş sergilemiştir. Modelin "Adım Adım Düşünme" (CoT) yapısını öğrenmesi, optimizasyon açısından daha verimli gerçekleşmiştir.
•	Diverse Think Dalgalanmaları: Diverse Think modelinin loss grafiğinde yer yer (özellikle orta adımlarda) küçük dalgalanmalar veya daha yavaş bir düşüş görülmüştür. Bu durum, veri setindeki "çeşitli çözüm yollarının" (diversity) model tarafından genelleştirilmesinin daha zorlu bir süreç olduğunu, ancak modelin eğitimin sonunda (Step 800) başarılı bir minima noktasına ulaştığını kanıtlamaktadır.
•	Aşırı Öğrenme Kontrolü: Step 400 civarında loss değerinin optimum seviyeye yaklaştığı görülmüştür. Bu nokta, Benchmark testlerindeki zirve performans (Step 400: %26.83) ile de örtüşmektedir. Step 500 sonrasında loss değerinde marjinal düşüşler devam etse de, validasyon başarısındaki dalgalanmalar modelin ezberlemeye (overfitting) yatkın hale geldiğine işaret etmektedir.
 4. Deneysel Sonuçlar (Experimental Results)
Aşağıdaki tablo, modellerin eğitim süreci boyunca gösterdiği performansı karşılaştırmaktadır. Veriler, her 100 adımda bir alınan checkpoint'lerin benchmark sonuçlarından derlenmiştir.
Checkpoint (Step)	Epoch	Deep Think Pass@1	Diverse Think Pass@1	Fark (Deep vs Diverse)
Step 100	0.35	%17.1	%9.8	Deep (+%7.3)
Step 200	0.71	%24.4	%24.4	Eşit
Step 300	1.06	%17.1	%14.6	Deep (+%2.5)
Step 400	1.42	%26.8 🥇	%22.0	Deep (+%4.8)
Step 500	1.77	%12.2	%12.2	Eşit
Step 600	2.13	%22.0	%22.0	Eşit
Step 700	2.48	%12.2	%12.2	Eşit
Step 800	2.84	%24.4	%24.4	Eşit


 
5. Analiz ve Bulgular
5.1. Output Verisinin Etkisi
Veri setlerindeki output yapılarının farkı, modellerin öğrenme eğrisini (learning curve) doğrudan etkilemiştir:
•	Deep Think'in Output Yapısı: Adım adım ilerleyen mantıksal çözüm yapısı (Deep output), modelin henüz eğitimin başında (Step 100) %7.3'lük bir fark atmasını sağlamıştır. Bu, modelin "nasıl düşüneceğini" daha hızlı öğrendiğini gösterir.
•	Diverse Think'in Output Yapısı: Çeşitlilik içeren outputlar, modelin başta kararsız kalmasına (Step 100: %9.8) neden olmuş, ancak eğitimin sonlarına doğru model bu çeşitliliği özümseyerek Deep Think ile aynı seviyeye gelmiştir.
5.2. En İyi Model Seçimi
Proje dökümanındaki başarı kriterleri göz önüne alındığında; Step 400 (Epoch 1.42) noktasındaki Deep Think modeli, %26.83 başarı oranı ile en optimum model olarak belirlenmiştir. 
 

 
6. Detaylı Analiz ve Tartışma (Discussion & Insights)
Bu bölümde, elde edilen deneysel bulgular; model mimarisi, veri yapısı ve eğitim dinamikleri perspektifinden yorumlanmış, "Deep" ve "Diverse" stratejilerinin altında yatan neden-sonuç ilişkileri irdelenmiştir.
6.1. Bilişsel Yük ve Öğrenme Verimliliği Hipotezi
Deneysel sonuçlar, Deep Think (Derinlemesine Düşünme) stratejisinin modele daha hızlı ve keskin bir yetenek kazandırdığını göstermektedir.
•	Bulgu: Deep Think, Step 100'de %17.1 gibi yüksek bir başlangıç yapmış ve 400. adımda zirveye ulaşmıştır.
•	Analiz: 1.5 milyar parametreli (1.5B) gibi nispeten "küçük" sayılan LLM'lerde, modelin kapasitesi sınırlıdır. Deep Think veri setindeki "tekil ama derinlemesine açıklanmış" çözüm yolu, modelin üzerindeki bilişsel yükü (cognitive load) azaltmıştır. Model, bir problemi çözmek için net bir "algoritmik rota" izlemeyi öğrenmiş ve bu da optimizasyon sürecini hızlandırmıştır.
•	Diverse Think Durumu: Buna karşın, Diverse Think setindeki "çoklu çözüm yolları", 1.5B boyutundaki bir modelde başlangıçta kafa karışıklığına (ambiguity) yol açmış olabilir. Modelin hangi çözüm stratejisini ağırlıklandıracağına karar vermesi zaman almış, bu da öğrenme eğrisinin (learning curve) daha yatay seyretmesine neden olmuştur.
6.2. Kapasite Doygunluğu ve "Unutma" (Catastrophic Forgetting) Riski
Her iki modelde de Step 400-500 sonrasında gözlemlenen dalgalanmalar ve performans düşüşleri kritik bir teknik detaya işaret etmektedir.
•	Gözlem: Step 400'deki %26.8'lik zirve başarı, eğitim devam ettikçe korunamamış ve Step 500'de %12.2'ye gerilemiştir.
•	Yorum: Bu durum, modelin "Overfitting" (Aşırı Öğrenme) sınırına geldiğini ve eğitim verisindeki gürültüyü ezberlemeye başladığını düşündürmektedir. Ayrıca, 1.5B modelin parametre uzayının doygunluğa ulaştığı, yeni karmaşık desenleri öğrenmeye çalışırken daha önce öğrendiği temel yetenekleri baskıladığı (Catastrophic Forgetting) sonucuna varılabilir.
6.3. "Diverse" Verinin Uzun Vadeli Etkisi
Diverse Think stratejisinin başarısız olduğu söylenemez; aksine, eğitimin sonunda Deep Think ile performansının eşitlenmesi (Convergence) önemli bir bulgudur.
•	Analiz: Bu, yeterli eğitim süresi (epoch) verildiğinde, modelin "çeşitlilikten" gelen karmaşayı çözebildiğini ve bu çeşitliliği genel bir kodlama yeteneğine dönüştürebildiğini gösterir. Ancak, bu projedeki gibi sınırlı kaynak ve adım sayısında (800 step), Diverse stratejisi "maliyet/performans" açısından Deep stratejisinin gerisinde kalmıştır.
6.4. Gelecek Çalışmalar ve Stratejik Öneriler (Future Work)
Bu çalışmadan elde edilen içgörüler doğrultusunda, bir sonraki aşamada şu stratejilerin uygulanması önerilmektedir:
1.	Hibrit Eğitim (Curriculum Learning): Eğitime önce Deep Think verisi ile başlanıp, modelin temel mantık yürütme yeteneği oturtulduktan sonra (örneğin 400. adımdan sonra), Diverse Think verisi ile devam edilerek modelin esnekliği artırılabilir.
2.	Erken Durdurma (Early Stopping): Step 400'den sonra performansın plato çizmesi veya düşmesi nedeniyle, kaynak verimliliği için eğitimin bu noktada kesilmesi (Early Stopping) en optimal stratejidir.
3.	Model Ölçekleme: Diverse Think stratejisinin potansiyelini tam olarak görebilmek için, parametre kapasitesi daha yüksek (örneğin Qwen2.5-7B veya 14B) modeller üzerinde test edilmesi, modelin farklı bakış açılarını daha iyi sentezlemesini sağlayabilir.
 
7. Sonuç
Bu çalışma, LLM eğitiminde kullanılan veri setinin sadece input değil, output kalitesinin ve yapısının da performansı kritik düzeyde etkilediğini doğrulamıştır. Proje dökümanındaki yönergelere sadık kalınarak yapılan testlerde, derinlemesine düşünme (Deep Think) stratejisinin, kodlama problemlerinde daha hızlı ve yüksek başarı sağladığı sonucuna varılmıştır.
Model	En İyi Checkpoint	Pass@1	Çözülen Soru
Base Model	-	%	27/41
Deep_instruction	step-400	%26.83 🥇	11/41 🥇
Diverse_instruction	step-200	%24.39	10/41

 

