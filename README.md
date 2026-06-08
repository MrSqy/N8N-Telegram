# 🤖 Telegram AI Asistan — n8n Workflows

Telegram üzerinden çalışan, komut tabanlı bir AI asistan sistemi. n8n workflow'ları ile otomasyon altyapısı kurulmuştur.

## 📋 Genel Bakış

Bu proje 3 workflow'dan oluşur:

| Workflow | Dosya | Açıklama |
|----------|-------|----------|
| **Master Router** | `master-telegram-router.json` | Ana yönlendirici. Telegram'dan gelen mesajları komutlara göre ilgili alt workflow'a yönlendirir. |
| **Web Research** | `telegram-web-research.json` | `/arastir` komutuyla Google üzerinden arama yapar, sonuçları AI ile analiz edip özetler. |
| **Document Summary** | `telegram-document-summary.json` | `/ozet` komutuyla gönderilen PDF dosyalarını okur ve AI ile Türkçe özetler. |

## 🏗️ Mimari

```
Telegram Kullanıcı
       │
       ▼
[Master Router] ──── /arastir ────▶ [Web Research] ──▶ SerpAPI + GPT-4.1-mini
       │
       ├──────────── /ozet ────────▶ [Document Summary] ──▶ PDF Parse + GPT-4.1-mini
       │
       └──────────── (serbest mesaj) ──▶ Google Gemini (genel sohbet)
```

### Desteklenen Komutlar

| Komut | İşlev |
|-------|-------|
| `/arastir <konu>` | Web'de arama yapar, sonuçları AI ile analiz eder |
| `/ozet` | Mesajla birlikte gönderilen PDF'i özetler |
| *(serbest mesaj)* | Genel AI sohbet (Gemini) |

## ⚙️ Kurulum

### 1. Workflow'ları Import Et

1. n8n arayüzünü aç
2. Her workflow için: **Add Workflow → ... → Import from File**
3. `workflows/` klasöründeki JSON dosyalarını sırayla import et

### 2. Gerekli Credential'lar

Import sonrası aşağıdaki credential'ları bağlaman gerekiyor:

| Credential | Kullanıldığı Yer | Nasıl Alınır |
|------------|-------------------|--------------|
| **Telegram Bot API** | Master Router | [@BotFather](https://t.me/BotFather) üzerinden bot oluştur |
| **OpenAI API** | Web Research, Document Summary | [platform.openai.com](https://platform.openai.com) |
| **Google Gemini API** | Master Router (genel sohbet) | [Google AI Studio](https://aistudio.google.com) |
| **SerpAPI** | Web Research | [serpapi.com](https://serpapi.com) |

### 3. Ortam Değişkenleri

Workflow'lardaki placeholder'ları kendi key'lerinle değiştir:

- `${TELEGRAM_BOT_TOKEN}` → Telegram bot token'ın
- `${SERPAPI_KEY}` → SerpAPI anahtarın
- `YOUR_TELEGRAM_USER_ID_1` → Yetkili kullanıcı Telegram ID'si

### 4. Aktivasyon

- Önce alt workflow'ları (**Web Research**, **Document Summary**) aktif et
- Son olarak **Master Router**'ı aktif et

## 🔒 Güvenlik Notları

- Workflow'lar **yetkili kullanıcı kontrolü** içerir — sadece tanımlı ID'ler bot'u kullanabilir
- API key'ler bu repo'da **temizlenmiştir**, kendi key'lerinizi eklemeyi unutmayın
- Credential'ları asla Git'e pushlamayın

## 🛠️ Teknoloji

- **n8n** — Workflow otomasyon platformu
- **OpenAI GPT-4.1-mini** — AI analiz ve özetleme
- **Google Gemini** — Genel sohbet
- **SerpAPI** — Google arama entegrasyonu
- **Telegram Bot API** — Mesajlaşma arayüzü
