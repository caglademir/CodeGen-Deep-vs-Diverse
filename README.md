

```markdown
# 🧠 CodeGen: Optimizing Small LLMs with Chain-of-Thought Strategies

![Project Status](https://img.shields.io/badge/Status-Completed-success)
![Model](https://img.shields.io/badge/Base_Model-Qwen2.5_Coder_1.5B-blue)
![Method](https://img.shields.io/badge/Fine_Tuning-LoRA-orange)
![Performance](https://img.shields.io/badge/Performance_Boost-11x-brightgreen)

## 📋 Proje Özeti (Executive Summary)

Bu proje, **1.5 Milyar** parametreli (küçük ölçekli) bir Dil Modelinin (LLM) kodlama yeteneklerini artırmak için veri odaklı eğitim stratejilerini araştırmaktadır. 

Proje kapsamında **Qwen2.5-Coder-1.5B** taban modeli, iki farklı düşünce yapısı (reasoning style) ile fine-tune edilmiştir:
1.  **Deep Think (Derinlemesine Mantık):** Problemi adım adım (step-by-step) analiz eden yapı.
2.  **Diverse Think (Çeşitli Bakış Açıları):** Aynı probleme farklı çözüm yolları sunan yapı.

**Ana Sonuç:** "Deep Think" stratejisi ile eğitilen model, **Step 400**'de ham modele kıyasla **11 kat performans artışı** (%2.44 -> %26.83) sağlayarak en iyi sonucu (SOTA) elde etmiştir.

---

## 📊 Benchmark ve Sonuçlar

Modeller **OpenAI HumanEval** ve **AtCoder** veri setleri üzerinde test edilmiştir. Aşağıdaki tablo, eğitim adımlarına (steps) göre başarı oranını (Pass@1) göstermektedir.

| Model Varyasyonu | Step | Başarı Oranı (Accuracy) | Değişim (vs Base) | Durum |
| :--- | :--- | :--- | :--- | :--- |
| **Base Model** | - | **%2.44** | - | 🔴 Yetersiz |
| Diverse Think | 200 | %24.39 | +10x | 🟡 İyi |
| Deep Think | 200 | %24.39 | +10x | 🟡 İyi |
| **Deep Think (Final)** | **400** | **%26.83** | **+11x** | 🟢 **Şampiyon** |
| Diverse Think (Final)| 400 | %21.95 | +9x | 🔴 Gerileme (Overfitting) |

### 🔍 Kritik Bulgular
* **Küçük Model Paradoksu:** 1.5B parametreli modellerde, modele "seçenek sunmak" (Diverse) yerine "net bir yol haritası çizmek" (Deep) daha başarılı olmuştur.
* **Verimlilik Artışı:** Deep Think modeli Step 200'den Step 400'e geçerken hem başarısını artırmış hem de ortalama düşünme uzunluğunu (token sayısı) **%20 azaltarak** optimize olmuştur.

---

## 🛠️ Teknik Altyapı ve Metodoloji

### Model Mimarisi
* **Base Model:** `Qwen/Qwen2.5-Coder-1.5B-Instruct`
* **Architecture:** Decoder-Only Transformer with Grouped-Query Attention (GQA).
* **Context Window:** 32k Tokens.

### Eğitim Yöntemi (Fine-Tuning)
Eğitim sürecinde bellek verimliliği için **LoRA (Low-Rank Adaptation)** kullanılmıştır.
* **Rank (r):** 32
* **Alpha:** 64
* **Target Modules:** `q_proj`, `v_proj`, `gate_proj`, `up_proj`
* **Batch Size:** 16
* **Optimizer:** AdamW

### Veri Setleri
* **Deep Dataset:** `Naholav/CodeGen-Deep-5K` (Adım adım mantıksal analiz içeren kod çözümleri).
* **Diverse Dataset:** `Naholav/CodeGen-Diverse-5K` (Farklı algoritmik yaklaşımlar içeren çözümler).



## 🚀 Kurulum ve Kullanım

Modeli yerel ortamınızda çalıştırmak için aşağıdaki adımları izleyebilirsiniz.

### 1. Gereksinimler

```bash
pip install torch transformers peft datasets bitsandbytes accelerate

```

### 2. Modelin Yüklenmesi (Inference)

Aşağıdaki Python kodu, eğitilmiş LoRA adaptörünü taban model ile birleştirerek çalıştırır:

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer
from peft import PeftModel

# 1. Taban Modeli Yükle
base_model_id = "Qwen/Qwen2.5-Coder-1.5B-Instruct"
model = AutoModelForCausalLM.from_pretrained(
    base_model_id,
    torch_dtype=torch.float16,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained(base_model_id)

# 2. Deep Think Adaptörünü Yükle (Step 400)
# 'path/to/deep-lora-adapter' kısmını indirdiğiniz klasör ile değiştirin
model = PeftModel.from_pretrained(model, "path/to/deep-lora-adapter")

# 3. Test Et
prompt = "Write a Python function to find the nth Fibonacci number."
inputs = tokenizer(prompt, return_tensors="pt").to("cuda")
outputs = model.generate(**inputs, max_new_tokens=500)
print(tokenizer.decode(outputs[0], skip_special_tokens=True))

```

---

## 👥 İletişim ve Lisans

Bu proje **[Çağla Demir 2020556018]** tarafından, LLM'lerin kodlama yeteneklerini araştırma projesi kapsamında geliştirilmiştir.

* **Lisans:** MIT License
* **Base Model Lisansı:** Apache 2.0 (Qwen)

---

*Generated for the CodeGen Final Report Submission.*

```

```
