# SpeakDeck AI Worker

Konuşma cevaplarını yapay zekâ ile değerlendiren Cloudflare Worker.

- `POST /evaluate` → cevabı değerlendirir, günlük ⚡ enerjiden 1 düşer
- `GET /energy?d=<cihazId>` → kalan enerji

**Sağlayıcılar:** önce Gemini API (Gemma 4 26B), hata olursa Cloudflare Workers AI (Gemma 4 26B).
**Koruma:** cihaz başına günlük enerji (KV), IP başına günlük üst sınır, CORS izin listesi, girdi doğrulama, JSON çıktı doğrulama.
Yapay zekâ ulaşılamazsa enerji düşmez.

## Panelden kurulum

1. **Worker oluştur:** Cloudflare paneli → *Workers & Pages* → *Create* → *Create Worker* → adı `speakdeck-ai` → *Deploy*.
2. **Kodu yapıştır:** *Edit code* → `worker.js` dosyasının tamamını yapıştır → *Deploy*.
3. **KV oluştur:** *Storage & Databases* → *KV* → *Create* → adı `speakdeck-energy`.
4. **Bağlamalar:** Worker → *Settings* → *Bindings* → *Add*:
   - *Workers AI* → değişken adı `AI`
   - *KV namespace* → değişken adı `KV` → `speakdeck-energy`
5. **Gizli anahtar:** Worker → *Settings* → *Variables and Secrets* → *Add* → tür **Secret**, ad `GEMINI_KEY`, değer = Google AI Studio anahtarın.
6. **Test:** `https://speakdeck-ai.<hesabın>.workers.dev/` adresini aç → `{"ok":true,...}` görmelisin.

## İsteğe bağlı değişkenler

| Ad | Varsayılan |
|---|---|
| `GEMINI_MODEL` | `gemma-4-26b-a4b-it` |
| `CF_MODEL` | `@cf/google/gemma-4-26b-a4b-it` |
| `DAILY_ENERGY` | `5` |
| `IP_DAILY_MAX` | `40` |
| `ALLOWED_ORIGINS` | `https://i-asimov.github.io` |

## Test

```bash
node test/test.mjs   # sahte KV/AI/fetch ile 15 test
```
