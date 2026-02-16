## 1. Kurulum
LM Studio'yu işletim sistemine uygun olarak indirin ve kurun:
[Download LM Studio - Mac, Linux, Windows](https://lmstudio.ai/download)

![[Pasted image 20260216121907.png]]

Kurulum sonrası donanım hızlandırma (GPU Offload) ayarları için sağ alt köşedeki ayarlar ikonuna gidin.
![[Pasted image 20260216122034.png]]

### Mac (Apple Silicon / M Serisi)
Ekstra bir ayara gerek yok. Metal otomatik olarak devreye girer. (M5 Serisinde destek eklenmemiş olabilir.)

### Windows
"Runtime" sekmesi altından uygun backend'i seçmelisiniz:
* **Sadece CPU:** "CPU llama.cpp" veya "Vulkan llama.cpp" seçin.
* **NVIDIA GPU:** Ekran kartını tam performans kullanmak için mutlaka **CUDA** versiyonlarını seçin.

![[Pasted image 20260216122110.png]]

GPU kullanımı için "GGUF" ayarlarından indirdiğiniz CUDA sürümünün seçili olduğundan emin olun.
![[Pasted image 20260216123035.png]]

## 2. Model İndirme
Sol menüdeki "Search" ikonundan model arayın.
![[Pasted image 20260216123217.png]]

**Quantization Seçimi:**
* Genellikle önerilen modeller **Q4_K_M** veya **Q5_K_M** quantizeli versiyonlar olur. Bu modeller hız/kalite dengesi için ideal olmasına rağmen modelin tam kalitesini veremez.
* Maksimum doğruluk için **fp16** veya **bf16** tercih edilir (VRAM kullanımı artar, hız düşer).
![[Pasted image 20260216125146.png]]
![[Pasted image 20260216125215.png]]

### Model ID Yönetimi

Yüklü modelleri görüntülemek ve yönetmek için sol menüdeki **My Models** (Klasör ikonu) sekmesine gidin.
![[Pasted image 20260216131436.png]]

API entegrasyonunda kullanılacak kimliği (ID) almak için:
İlgili modelin **Action** menüsü altından **Copy Default Identifier** seçeneğine tıklayın.
![[Pasted image 20260216131536.png]]

## 3. Local Server Başlatma
Sunucuyu başlatmak ve API olarak kullanmak için sol menüden **Developer** sekmesine gidin.

![[Pasted image 20260216125436.png]]

**Kritik Ayarlar:**
"Start Server" yaptıktan önce veya sonra **Server Settings** altında şunları aktif edin:
1.  **CORS:** Cross-Origin hatalarını önlemek için.
2.  **Local Network:** Ağdaki veya lokaldeki diğer servislerin erişebilmesi için.

![[Pasted image 20260216125658.png]]

API uyumluluğu için "Supported Endpoints" kısmında **OpenAI-Compatible** seçili olmalı.
![[Pasted image 20260216125751.png]]

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
