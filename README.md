
🧠 Reasoning-Enhanced LLM: Comparative Analysis of Deep vs. Diverse Instruction Tuning
📑 Proje Özeti (Executive Summary)
Bu proje, Large Language Models (LLMs) dünyasında "Reasoning" (Akıl Yürütme) yeteneğinin Supervised Fine-Tuning (SFT) yöntemiyle nasıl kazandırılacağını araştırmaktadır.
Proje kapsamında, Qwen2.5-Coder-1.5B-Instruct temel modeli, iki farklı veri seti stratejisi ("Deep" ve "Diverse") kullanılarak LoRA (Low-Rank Adaptation) yöntemiyle eğitilmiştir. Amaç, modelin sadece kod sentaksını öğrenmesi değil, Chain-of-Thought (CoT) yaklaşımıyla problemleri adım adım analiz ederek çözmesidir.
🔬 Metodoloji ve Veri Setleri
Projede hipotez testi için iki farklı eğitim stratejisi kurgulanmıştır:
Strateji	Veri Seti Yapısı	Hedeflenen Yetkinlik
🔵 Deep Instruction	Karmaşık, çok adımlı mantık zincirleri içeren derinlemesine problemler.	Zor problemlerde derinlemesine mantık yürütme (Deep Reasoning).
🟣 Diverse Instruction	Farklı alanlara yayılmış, geniş çeşitlilikte problem tipleri.	Hızlı adaptasyon, genelleme (Generalization) ve veri verimliliği.
🚀 Kritik Eğitim Stratejisi: "Output" (Reasoning + Code) Kullanımı
Bu çalışmada standart "Instruction -> Solution" eğitimi yerine, veri setindeki opsiyonel Output (Reasoning Steps + Code) alanı aktif olarak kullanılmıştır.
•	Amaç: Modele sadece nihai kodu değil, o koda giden düşünce sürecini (Thinking Process)öğretmek.
•	Yöntem: Model, problemleri çözerken <think> etiketleri arasında adım adım mantık yürütmeye teşvik edilmiştir.
⚙️ Teknik Konfigürasyon ve Hiperparametreler
Eğitim sürecinde "Long Context" yeteneklerini korumak ve hassas öğrenme sağlamak adına aşağıdaki konfigürasyon kullanılmıştır:
🛠️ Model & LoRA Ayarları
•	Base Model: Qwen/Qwen2.5-Coder-1.5B-Instruct
•	LoRA Rank (r): 16
•	LoRA Alpha: 32
•	Target Modules: q_proj, k_proj, v_proj, o_proj, gate_proj, up_proj, down_proj
•	LoRA Dropout: 0.05
📉 Eğitim Parametreleri
•	Epoch Sayısı: 3 (Tam Eğitim Döngüsü)
•	Learning Rate: 5e-5 (Hassas ayar için düşük tutuldu)
•	Context Length: 8192 Token (Uzun mantık zincirlerini desteklemek için genişletildi)
•	Effective Batch Size: 16 (Gradient Accumulation ile)
•	Precision: float16 (T4 Optimization) / bfloat16 (A100)
•	System Prompt: "You are an expert Python programmer. Please read the problem carefully before writing any Python code."
📊 Deneysel Sonuçlar ve Analiz
Modellerin başarısı, eğitim setinde görülmeyen 41 adet AtCoder (Easy/Medium) problemi üzerinde test edilmiştir. Başarı metriği olarak Pass@1 kullanılmıştır.
1. Karşılaştırmalı Performans Tablosu
Model Mimarisi	Checkpoint (Adım)	Pass@1 Skoru	Çözülen Soru	Analiz Notu
Base Model (Ref)	-	~17.0%	7/41	Referans Başlangıç Noktası
Deep Think	Step-400	%26.8	11/41	Maksimum Performans (Geç Zirve)
Deep Think	Step-700	%12.2	5/41	Aşırı Overfitting (Çöküş)
Diverse Think	Step-200	%24.4	10/41	Hızlı Öğrenme & Optimal Nokta
Diverse Think	Step-300	%14.6	6/41	Over-Reasoning Kaynaklı Düşüş
Diverse Think	Step-400	%21.95	9/41	Doygunluk (Saturation)
(Tablo verileri proje raporundaki analizlere dayanmaktadır)
2. Kritik Analiz ve Bulgular (Analist Yorumu)
Eğitim süreçleri incelendiğinde iki temel fenomen gözlemlenmiştir:
📉 Deep Model: "Geç Öğrenme ve Yüksek Volatilite"
Deep stratejisi, en yüksek skoruna (%26.8) eğitimin ortalarında (Step 400) ulaşabilmiştir. Ancak Step 500 sonrasında model ezberlemeye (overfitting) başlamış ve performansı %12.2'ye kadar gerilemiştir.
🚀 Diverse Model: "Veri Verimliliği ve Erken Zirve"
Diverse modeli, eğitimin henüz başında (Step 200) %24.4 başarı oranına ulaşarak Deep modelinin başarısını yakalamıştır.
•	Context Length Etkisi: 8192 tokenlık geniş bağlam penceresi ve "Output" alanının kullanımı sayesinde model, çok az veriyle (Step 200) karmaşık mantık yürütme yeteneği kazanmıştır.
•	Sonuç: Diverse veri seti, 2 kat daha hızlı öğrenme sağlamış ve kaynak verimliliği açısından Step 200final model olarak seçilmiştir.
📈 Görselleştirme
Performans Değişimi ve Optimal Noktalar
Aşağıdaki grafik, Deep modelin geç zirvesini ve Diverse modelin erken doygunluğunu göstermektedir:
🚀 Kurulum ve Yeniden Üretilebilirlik
Projenin yerel ortamda tekrar edilmesi için adımlar:
Bash
# 1. Repoyu Klonlayın
git clone https://github.com/KULLANICI_ADINIZ/LLM-Reasoning-LoRA.git
cd LLM-Reasoning-LoRA

# 2. Gereksinimleri Yükleyin
pip install -r requirements.txt

# 3. Klasör Yapısını Doğrulayın
# CodeGen/models/ altında "deep_instruction" ve "diverse_instruction" olmalıdır.

# 4. Modeli Test Edin (Örnek: Diverse Model)
python livecodebench_eval.py --model_type diverse_think --platform atcoder --difficulty easy
📚 Kaynakça
Bu proje aşağıdaki rehber ve veri setleri referans alınarak geliştirilmiştir:
1.	Hugging Face LoRA Training Guide
2.	CodeGen Dataset (Deep & Diverse)
3.	LiveCodeBench Evaluation Framework


