## 1. Kurulum
LM Studio'yu işletim sistemine uygun olarak indirin ve kurun:
[Download LM Studio - Mac, Linux, Windows](https://lmstudio.ai/download)
<img width="1066" height="527" alt="image" src="https://github.com/user-attachments/assets/bd81b866-7595-4b81-9d08-fc5b9c79b57b" />

Kurulum sonrası donanım hızlandırma (GPU Offload) ayarları için sağ alt köşedeki ayarlar ikonuna gidin.
<img width="949" height="733" alt="image" src="https://github.com/user-attachments/assets/e256a760-a0f0-4c1b-86b1-9bd3d3ba0c9f" />

### Mac (Apple Silicon / M Serisi)
Ekstra bir ayara gerek yok. Metal otomatik olarak devreye girer. (M5 Serisinde destek eklenmemiş olabilir.)

### Windows
"Runtime" sekmesi altından uygun backend'i seçmelisiniz:
* **Sadece CPU:** "CPU llama.cpp" veya "Vulkan llama.cpp" seçin.
* **NVIDIA GPU:** Ekran kartını tam performans kullanmak için mutlaka **CUDA** versiyonlarını seçin.
* **NVIDIA Ekran Kartı Varsa:** Mutlaka **CUDA** sürümü seçilmeli. Bu sayede işlemlerin işlemci yerine ekran kartında yapılır.

<img width="947" height="737" alt="image" src="https://github.com/user-attachments/assets/0eee32a5-7c51-4448-be32-8f1922e75755" />

GPU kullanımı için "GGUF" ayarlarından indirdiğiniz CUDA sürümünün seçili olduğundan emin olun.
<img width="938" height="733" alt="image" src="https://github.com/user-attachments/assets/6301b886-1626-4566-9686-c70f1879ed3f" />

---

## 2. Model İndirme
Sol menüdeki "Search" ikonundan model arayın.
<img width="947" height="724" alt="image" src="https://github.com/user-attachments/assets/120615a4-e158-42ca-93a6-d624afc971ed" />

**Quantization Seçimi:**
* Genellikle önerilen modeller **Q4_K_M** veya **Q5_K_M** quantizeli versiyonlar olur. Bu modeller hız/kalite dengesi için ideal olmasına rağmen modelin tam kalitesini veremez.
* Maksimum doğruluk için **fp16** veya **bf16** tercih edilir (VRAM kullanımı artar, hız düşer).
<img width="952" height="727" alt="image" src="https://github.com/user-attachments/assets/0eff3801-ad24-4cd7-b47e-656d94407f12" />
<img width="846" height="589" alt="image" src="https://github.com/user-attachments/assets/fa9ab9ac-8bad-4c8e-897e-cdebebb8f1ab" />

### Model ID Yönetimi
Yüklü modelleri görüntülemek ve yönetmek için sol menüdeki **My Models** (Klasör ikonu) sekmesine gidin.
<img width="297" height="275" alt="image" src="https://github.com/user-attachments/assets/2c689238-c4e7-4736-ac32-249791bb4904" />

API entegrasyonunda kullanılacak kimliği (ID) almak için:
İlgili modelin **Action** menüsü altından **Copy Default Identifier** seçeneğine tıklayın.
<img width="1329" height="388" alt="image" src="https://github.com/user-attachments/assets/95ddc286-22ac-4bb0-8844-97e93365642d" />

---

## 3. Local Server Başlatma
Sunucuyu başlatmak ve API olarak kullanmak için sol menüden **Developer** sekmesine gidin.

<img width="1919" height="1028" alt="image" src="https://github.com/user-attachments/assets/c1130102-7fc6-4f2b-939d-610b8c62070d" />

**Kritik Ayarlar:**
"Start Server" yaptıktan önce veya sonra **Server Settings** altında şunları aktif edin:
1.  **CORS:** Cross-Origin hatalarını önlemek için.
2.  **Local Network:** Ağdaki veya lokaldeki diğer servislerin erişebilmesi için.

<img width="524" height="447" alt="image" src="https://github.com/user-attachments/assets/9928227a-bfa3-4f85-9969-72ae2d602d74" />

API uyumluluğu için "Supported Endpoints" kısmında **OpenAI-Compatible** seçili olmalı.
<img width="457" height="280" alt="image" src="https://github.com/user-attachments/assets/3ba26a23-ffc7-4261-8d62-89a2b29ae8b5" />

---

## 4. API Kullanımı (Python)

> **Dokümantasyon:**
> - OpenAI Standartları: [OpenAI API Docs](https://platform.openai.com/docs/api-reference/chat/create)
> - LM Studio Spesifik: [LM Studio Docs](https://lmstudio.ai/docs/developer/openai-compat/chat-completions)

LM Studio, OpenAI kütüphanesi ile uyumludur. `base_url` parametresini localhost'a yönlendirmeniz yeterlidir.

```python
from openai import OpenAI

API_KEY = "lmstudio" # Auth kapalıysa opsiyonel
BASE_URL = "http://localhost:1234/api/v1"
MODEL_ID = "ministral-3b-2512" # Yüklü modelin ID'si
MAX_TOKENS = 1024
TEMPERATURE = 0.7

client = OpenAI(
    api_key=API_KEY,
    base_url=BASE_URL
)

completion = client.chat.completions.create(
    model=MODEL_ID,
    messages=[
        {"role": "system", "content": "Sen yardımcı bir yapay zekasın."},
        {"role": "user", "content": "Python ile binary search algoritmasını yaz."}
    ],
    temperature=TEMPERATURE,
    max_tokens=MAX_TOKENS,
    stream=False
)

print(completion.choices[0].message.content)
```
