🧠 CodeGen: Küçük Ölçekli LLM'lerde Deep vs. Diverse Düşünme Stratejileri
Bu proje, küçük ölçekli Büyük Dil Modellerinin (LLM) kod üretim yetenekleri üzerinde farklı Düşünce Zinciri (Chain-of-Thought - CoT) stratejilerinin etkisini araştırmaktadır. Qwen2.5-Coder-1.5B modeli, iki farklı veri seti yapısı kullanılarak LoRA yöntemiyle eğitilmiş ve sonuçlar karşılaştırılmıştır: Deep Think (Adım adım derinlemesine mantık) ve Diverse Think (Çoklu çözüm perspektifleri).

📊 Temel Sonuçlar
Deneylerimiz, 1.5B parametreli sınırlı kapasiteye sahip modellerin, net ve yapılandırılmış bir düşünce zinciri (Deep Think) ile eğitildiğinde, çeşitlilik içeren stratejilere (Diverse Think) göre daha hızlı öğrendiğini ve daha yüksek performans gösterdiğini kanıtlamıştır.

Model Varyasyonu	Strateji	Pass@1 Skoru	İyileştirme
Base Model	Eğitimsiz (Zero-shot)	%2.44	-
Deep Think	Adım Adım Mantık	%26.83	11 Kat 🚀
Diverse Think	Çoklu Bakış Açısı	%24.39	10 Kat
Kritik Bulgular: Base modelin %2.44 olan başarısı, Deep Think stratejisi ile %26.83'e yükselmiştir. Bu, veri odaklı ince ayarın (SFT) modelin kodlama yeteneğini devasa oranda artırdığını gösterir.

🛠️ Teknik Mimari ve Altyapı
Proje, kaynak verimliliği ve optimizasyon gözetilerek aşağıdaki teknik altyapı üzerine kurulmuştur:

Taban Model: Qwen/Qwen2.5-Coder-1.5B-Instruct (Decoder-Only Transformer)

Eğitim Yöntemi: LoRA (Low-Rank Adaptation)

Rank (r): 32

Alpha: 64

Learning Rate: 5e-5

Hedef Modüller: q_proj, v_proj, gate_proj, up_proj, down_proj

Hassasiyet (Precision): bfloat16 (Mixed Precision)

Optimizer: AdamW (Cosine Scheduler ile)

Bağlam Uzunluğu: 32k Token

📈 Performans Analizi ve Karşılaştırma
1. Öğrenme Eğrisi (Deep vs. Diverse)

Aşağıdaki grafik, eğitim süreci boyunca modellerin HumanEval benchmark üzerindeki başarısını göstermektedir. Deep Think (Mavi), eğitimin başlarında (Step 100) %17.1 gibi yüksek bir seviyeye ulaşarak kararlı bir öğrenme sergilerken; Diverse Think (Turuncu), farklı çözüm yollarını analiz etmenin getirdiği yük nedeniyle daha yavaş bir başlangıç yapmıştır.

(Not: Kırmızı kesikli çizgi, Base Modelin %2.44'lük başlangıç seviyesini temsil eder.)

2. Başarı ve Başarısızlık Analizi (Root Cause Analysis)

Benchmark sonuçları incelendiğinde modellerin davranışları şu şekilde yorumlanmıştır:

✅ Neden Başarılı Oldular?

Deep Think: Problemi kodlamadan önce <think> etiketleri içinde küçük parçalara ayırıp planladığı için sözdizimi ve mantık hatalarını minimize etmiştir.

Diverse Think: Standart döngüler yerine Python'a özgü kısa yolları (list comprehension vb.) kullanarak daha yaratıcı çözümler üretmiştir.

❌ Neden Başarısız Oldular?

Deep Think Hatası ("Over-reasoning"): Model bazen problemi analiz etmeye o kadar odaklanmıştır ki, çözüm kodunu yazarken bağlam (context) sınırına takılmış veya kod bloğunu yarım bırakmıştır.

Diverse Think Hatası ("Ambiguity"): Eğitim setinde aynı soru için birden fazla çözüm yolu olması, modelde kararsızlık yaratmıştır. Model test sırasında iki farklı yöntemi (örneğin recursive ve iterative) karıştırarak hibrit ve hatalı kod üretmiştir.

3. Sonuç

Bilişsel Yük Hipotezi: Analizler, 1.5B gibi küçük modellerde, modele "tek ve net bir yol" (Deep Think) göstermenin, ona "seçenekler sunmaktan" (Diverse Think) çok daha verimli olduğunu doğrulamaktadır. Diverse stratejisi, daha büyük kapasiteli (7B+) modellerde potansiyel gösterebilir ancak küçük modellerde başlangıç direncine (learning inertia) neden olmaktadır.

📂 Proje Yapısı
Bash
├── result/               # Benchmark logları (JSONL) ve analiz dosyaları
├── src/                   # Eğitim (train.py) ve test (evaluate.py) kodları
├── assets/                # Performans grafikleri
├── requirements.txt       # Gerekli kütüphaneler
└── README.md              # Proje dökümantasyonu
🚀 Kurulum ve Kullanım
1. Repoyu Klonlayın

Bash
git clone https://github.com/caglademir/CodeGen-Deep-vs-Diverse.git
cd CodeGen-Deep-vs-Diverse
2. Kütüphaneleri Yükleyin

Bash
pip install -r requirements.txt
3. Modeli Eğitin (Örnek)

Deep Think veri seti ile eğitimi başlatmak için:

Bash
python src/train.py --model_id "Qwen/Qwen2.5-Coder-1.5B-Instruct" --dataset "deep-think" --epochs 3
4. Benchmark Testi

Eğitilen modeli HumanEval üzerinde test etmek için:

Bash
python src/evaluate.py --checkpoint_path "models/deep_think/checkpoint-400"
🤝 Katkıda Bulunma
Katkılarınızı bekliyoruz! Lütfen bir Pull Request göndermekten çekinmeyin.

📜 Lisans
Bu proje Apache 2.0 Lisansı ile lisanslanmıştır.
