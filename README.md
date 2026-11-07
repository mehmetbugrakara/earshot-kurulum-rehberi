# 🎧 Earshot Kurulum ve Claude Code Entegrasyon Rehberi

> **Yapay zeka ajanlarınıza (Claude Code, Cursor, Antigravity) kulak ve ses üretim yeteneği kazandırın.**  
> Kodladığınız videodaki ses senkronunu kontrol edin, ses seviyelerini (LUFS/dBTP) profesyonelce ölçün ve geçiş efektleri için kodla anında yeni sesler üretin.

[![GitHub stars](https://img.shields.io/badge/Original%20Repo-Leonxlnx%2Fearshot-blue?logo=github)](https://github.com/Leonxlnx/earshot)
[![Web Rehber](https://img.shields.io/badge/Canlı%20Rehber-GitHub%20Pages-cyan?logo=safari)](https://mehmetbugrakara.github.io/earshot-kurulum-rehberi/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-brightgreen.svg?logo=python)](https://www.python.org/)
[![Claude Code MCP](https://img.shields.io/badge/Claude%20Code-MCP%20Ready-purple.svg)](https://modelcontextprotocol.io/)

---

## 📌 İçindekiler
1. [Earshot Nedir ve Neden Önemli?](#-earshot-nedir-ve-neden-önemli)
2. [Sistem Gereksinimleri](#-sistem-gereksinimleri)
3. [Adım 1: FFmpeg Kurulumu](#-adım-1-ffmpeg-kurulumu)
4. [Adım 2: Earshot Kurulumu](#-adım-2-earshot-kurulumu)
5. [Adım 3: Claude Code ve AI Ajanlarına Bağlantı (MCP)](#-adım-3-claude-code-ve-ai-ajanlarına-bağlantı-mcp)
6. [Adım 4: Temel Yetenekler ve Komutlar](#-adım-4-temel-yetenekler-ve-komutlar)
   - [1. Ses Seviyesi ve Kalite Ölçümü (`measure`)](#1-ses-seviyesi-ve-kalite-ölçümü-measure)
   - [2. Akıllı Ses Arama ve Nokta Atışı Kırpma (`rank` & `find`)](#2-akıllı-ses-arama-ve-nokta-atışı-kırpma-rank--find)
   - [3. Sentetik vs. Gerçek Ses Tespiti (`realness`)](#3-sentetik-vs-gerçek-ses-tespiti-realness)
   - [4. Video & Ses Senkronizasyon Analizi (`timeline` & `sheet`)](#4-video--ses-senkronizasyon-analizi-timeline--sheet)
   - [5. Kodla Anında Ses Efekti Üretme (`forge` & `kit`)](#5-kodla-anında-ses-efekti-üretme-forge--kit)
7. [Claude Code ile Örnek Promptlar (İstemler)](#-claude-code-ile-örnek-promptlar-istemler)
8. [Sık Karşılaşılan Sorunlar ve Çözümleri](#-sık-karşılaşılan-sorunlar-ve-çözümleri)

---

## 💡 Earshot Nedir ve Neden Önemli?

Normal şartlarda LLM'ler ve yapay zeka kodlama ajanları (Claude Code vb.) ses dosyalarını doğrudan "duyamaz". Bir videoya kodla ses eklerken sesin görüntünün neresine denk geldiğini, patlayıp patlamadığını (clipping), ses seviyesinin (LUFS) standartlara uyup uymadığını veya ses efektinin sahneye yakışıp yakışmadığını tahmin etmek zorunda kalırlar.

**Earshot**, yerel makinenizde (CPU üzerinde, internete ses yüklemeden) çalışan açık kaynaklı bir ses analizi ve sentezleme motorudur:
- **Duyma Yetisi Kazandırır:** CLAP ve AST modelleriyle sesi anlayıp metinsel etiketlere döker.
- **Senkron Kontrolü Yapar:** Videodaki sahne geçişleri ile ses patlamaları (transient/onset) arasındaki milisaniyelik farkı hesaplar.
- **Ses Seviyesini Ölçer:** YouTube, TV ve podcast yayıncılık standartlarına (EBU R128, LUFS, True Peak) göre sesi değerlendirir.
- **Kodla Sıfırdan Ses Üretir (Forge/Kit):** Hazır kütüphanelerde saatlerce doğru efekti aramak yerine, geçiş whoosh'u, pop, tık, zil gibi sesleri parametrik DSP senteziyle kodla anında oluşturur.

---

## 🛠️ Sistem Gereksinimleri

- **İşletim Sistemi:** Windows 10/11, macOS veya Linux
- **Python:** Python 3.10 veya üzeri
- **FFmpeg:** Ses ve video dosyalarını işlemek için sistemde kurulu ve PATH'e ekli olmalıdır
- **Donanım:** Harici GPU şart değildir; yerel CPU üzerinde sorunsuz çalışır

---

## 🚀 Adım 1: FFmpeg Kurulumu

Earshot'ın MP4, WAV, MP3 ve OGG dosyalarını okuyabilmesi için FFmpeg gereklidir.

### Windows (PowerShell ile):
```powershell
winget install Gyan.FFmpeg
```
*Kurulum sonrası terminali kapatıp açın ve doğrulamak için `ffmpeg -version` yazın.*

### macOS (Terminal ile):
```bash
brew install ffmpeg
```

### Linux (Ubuntu/Debian):
```bash
sudo apt update && sudo apt install -y ffmpeg
```

---

## 📦 Adım 2: Earshot Kurulumu

Earshot'ı bağımlılıkları çakışmayacak şekilde ayrı bir sanal ortamda (venv) kurmak en sağlıklı yoldur.

### 1. Depoyu Klonlayın
```bash
git clone https://github.com/Leonxlnx/earshot.git
cd earshot
```

### 2. Sanal Ortam (venv) Oluşturun ve Aktif Edin

**Windows (PowerShell):**
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**macOS / Linux:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Paketleri ve Bağımlılıkları Yükleyin
```bash
pip install --upgrade pip
pip install -e ".[mcp,llm]"
```

### 4. Kurulumu Doğrulayın (`doctor`)
```bash
earshot doctor
```
Tüm gereksinimlerin yeşil tik (`✓`) aldığını ve FFmpeg ile model önbelleğinin hazır olduğunu görün.

---

## 🔌 Adım 3: Claude Code ve AI Ajanlarına Bağlantı (MCP)

Earshot'ın en güçlü tarafı, **Model Context Protocol (MCP)** sunucusu olarak çalışabilmesidir. Bu sayede Claude Code her analiz için modeli baştan yüklemez; modeller hafızada kalır ve milisaniyeler içinde yanıt verir.

### Yöntem A: Claude Code CLI İle Tek Komutta Ekleme

Claude Code kullanıyorsanız terminalden doğrudan şu komutu verin:

**Windows (PowerShell):**
```powershell
claude mcp add earshot --scope user -e EARSHOT_THREADS=3 -- "C:/path/to/earshot/.venv/Scripts/python.exe" -m earshot mcp
```
*(Not: `C:/path/to/earshot` kısmını kendi earshot klasör yolunuzla değiştirin)*

**macOS / Linux:**
```bash
claude mcp add earshot --scope user -e EARSHOT_THREADS=3 -- "/path/to/earshot/.venv/bin/python" -m earshot mcp
```

---

### Yöntem B: `.mcp.json` veya Claude Ayar Dosyasına Manuel Ekleme

Ajanınızın MCP ayar dosyasına (`.mcp.json` veya `claude_desktop_config.json`) şu bloğu ekleyin:

```json
{
  "mcpServers": {
    "earshot": {
      "command": "C:/path/to/earshot/.venv/Scripts/python.exe",
      "args": ["-m", "earshot", "mcp"],
      "env": {
        "EARSHOT_THREADS": "3"
      }
    }
  }
}
```
*(macOS/Linux için `python` yolunu `.venv/bin/python` olarak girin).*

> 💡 **İpucu:** İlk model yüklemesi 20-30 saniye sürebilir. Claude Code'un zaman aşımına (timeout) uğramaması için ortam değişkenini artırabilirsiniz:
> - PowerShell: `$env:MCP_TIMEOUT=60000; claude`
> - Bash: `export MCP_TIMEOUT=60000 && claude`

---

## 🎯 Adım 4: Temel Yetenekler ve Komutlar

### 1. Ses Seviyesi ve Kalite Ölçümü (`measure`)
Videonuzdaki veya ses dosyanızdaki desibel ve dinamik aralığı ölçer:
```bash
earshot measure audio.wav
```
**Çıktı Bilgileri:**
- `LUFS` (Integrated Loudness - YouTube için ideal seviye -14 LUFS civarıdır)
- `dBTP` (True Peak - dijital patlamaları önlemek için en fazla -1.0 dBTP olmalı)
- `LRA` (Loudness Range - sesin dinamik genişliği)

---

### 2. Akıllı Ses Arama ve Nokta Atışı Kırpma (`rank` & `find`)
Elinizdeki SFX klasöründeki yüzlerce dosya arasından promptunuza en uygun olanı sıralar:
```bash
# En uygun 5 efekti listele
earshot rank "a crisp whoosh air transition" ./downloads/sfx/ --top 5

# Seçilen uzun ses kaydının içindeki en temiz vurucu anı saniye bazında bul
earshot find sfx_kaydi.ogg "a single clean mallet hit" --top 3
```

---

### 3. Sentetik vs. Gerçek Ses Tespiti (`realness`)
Kullanılan ses efektinin gerçek bir akustik kayıt mı yoksa yapay zeka/kod sentezi mi olduğunu test eder:
```bash
earshot realness effect.wav
```
- `p_real > 0.77`: Yüksek ihtimalle gerçek stüdyo kaydı.
- `p_real < 0.17`: Yapay veya kod ile sentezlenmiş ses.

---

### 4. Video & Ses Senkronizasyon Analizi (`timeline` & `sheet`)
Videonuz ile ses kanalının uyumunu görsel ve metinsel olarak raporlar:
```bash
# Kesim noktaları ve ses vuruşlarının zaman çizelgesi
earshot timeline video.mp4

# Görsel spektrogram ve senkron analiz görseli üretir (PNG)
earshot sheet video.mp4 -o sync_raporu.png
```
*`sync_raporu.png` açıldığında geçişlerin tam ses vuruşuna oturup oturmadığı yeşil/sarı/kırmızı işaretlerle gösterilir.*

---

### 5. Kodla Anında Ses Efekti Üretme (`forge` & `kit`)
Dışarıdan dosya aramadan, projenizin ihtiyacı olan ses kitini anında üretin:
```bash
# Sakin/minimal bir video için ses kiti şablonu başlat
earshot kit init --preset calm -o audio/kit.json

# Kit içerisindeki ses efektlerini WAV olarak render et
earshot kit render audio/kit.json --out audio/kit/
```
Hazır kategoriler: `ui_pop`, `knock`, `cut_hit`, `mallet`, `drip`, `whoosh_fast`, `reverse_swell` vb.

---

## 🤖 Claude Code ile Örnek Promptlar (İstemler)

Earshot MCP aracını Claude Code'a bağladıktan sonra doğrudan şu tarz promptlar verebilirsiniz:

```text
"Kurguladığımız 'final_video.mp4' dosyasını earshot timeline aracıyla dinle.
Görsel kesimlerle ses efektlerinin senkronunu kontrol et.
Geciken veya ses olmayan geçiş noktalarını bana listele."
```

```text
"'intro_music.wav' dosyasının ses seviyesini ölç.
YouTube standartlarına göre (-14 LUFS) fazla kısık veya patlama var mı incele."
```

```text
"Bu Reels videosunun 3. saniyesindeki başlık animasyonu için
earshot kit aracıyla tok ve profesyonel bir 'ui_pop' efekti render et
ve kurgu kodumuza entegre et."
```

---

## ❓ Sık Karşılaşılan Sorunlar ve Çözümleri

| Hata / Durum | Sebep | Çözüm |
| :--- | :--- | :--- |
| `ffmpeg: command not found` | FFmpeg sistem PATH'inde tanımlı değil | FFmpeg kurun ve terminali yeniden başlatın (`winget install Gyan.FFmpeg`). |
| `MCP connection timed out` | CLAP/AST modelleri ilk yüklenirken zaman aşımı oluştu | `$env:MCP_TIMEOUT=60000; claude` şeklinde zaman aşımı süresini 60 saniyeye çekin. |
| `CPU kullanımı çok yüksek` | Varsayılan thread sayısı işlemciyi zorluyor | `EARSHOT_THREADS=2` veya `EARSHOT_THREADS=3` ortam değişkeniyle sınırlayın. |
| `Model indirme hatası` | Hugging Face modelleri indirilirken bağlantı koptu | `earshot doctor --load` komutunu çalıştırarak modelleri önceden önbelleğe alın. |

---

## 📚 Kaynaklar ve Teşekkürler

- **Orijinal Earshot Projesi:** [Leonxlnx/earshot](https://github.com/Leonxlnx/earshot)
- **Model Context Protocol (MCP):** [modelcontextprotocol.io](https://modelcontextprotocol.io/)

---

⭐ **Faydalı bulduysanız depoyu yıldızlamayı unutmayın!**  
Herhangi bir sorunuz veya takıldığınız bir adım olursa Issues sekmesinden iletebilirsiniz.
