# Trendyol Firsat Radari

Streamlit arayuzu ile Trendyol urunlerini ve stoklarini yerel bilgisayarda takip eder.

Bu proje artik yalnizca lokal kullanim icindir. Dis yayin ve uzak veritabani ayarlari projeden kaldirilmistir.

## Calistirma

Windows PowerShell:

```powershell
.\.venv\Scripts\streamlit.exe run app.py --server.port 8501 --server.address 127.0.0.1 --server.headless true
```

Tarayicida:

```text
http://localhost:8501/
```

## Veri

Favoriler, NKS kayitlari ve stok takip verileri yerel `.runtime` klasorunde tutulur. Bu klasor git'e eklenmez.

## Notlar

- Uygulama lokal calisir; dis cihazlardan erisim icin ayar tutulmaz.
- Nekadarsatti cookie/API bilgilerini koda yazma.
- `.runtime` klasorunu silersen yerel kayitlar da silinir.
