# Domain ile online yayin

Bu proje Streamlit uygulamasidir. Diger cihazlardan erisim icin uygulamayi internette calisan bir sunucuya deploy edip domain DNS kaydini o sunucuya yonlendirmek gerekir.

## Onerilen yol: Render

Projede `render.yaml` hazir. Render bu dosyayla:

- Web servisini baslatir.
- Postgres veritabani olusturur.
- `DATABASE_URL` degiskenini uygulamaya baglar.

Bu sayede Favoriler, NKS kayitlari ve stok takip verileri deploy ortaminda kalici olur.

## GitHub'a yukleme

Bu klasor su anda git repository olarak gorunmuyor. Degisiklikleri GitHub reposuna yuklemek icin:

```powershell
git init
git remote add origin https://github.com/yasskkozax/trendyol-firsat-radari.git
git add .
git commit -m "Prepare Render deployment"
git branch -M main
git push -u origin main
```

Repo daha once GitHub'da varsa ve `git init` yerine yeni kopya almak istersen, once mevcut klasoru yedekle.

## Render'da deploy

1. Render hesabina gir.
2. `New > Blueprint` sec.
3. GitHub reposunu bagla.
4. Render `render.yaml` dosyasini okuyup web servis ve Postgres olusturacak.
5. Ilk kurulumda bu secret degerleri sorarsa gir:
   - `APP_PASSWORD`: Site sifresi.
   - `NEKADARSATTI_API_KEY`: Varsa NKS API key.
   - `NEKADARSATTI_COOKIE`: Varsa NKS cookie.
6. Deploy bitince Render sana `https://...onrender.com` adresi verir.

## Domain baglama

Render servisinde:

1. `Settings > Custom Domains` alanina gir.
2. Domaini ekle:
   - Ornek: `radar.senindomainin.com`
3. Render'in verdigi DNS kaydini domain paneline ekle.

Genelde subdomain icin:

```text
Type: CNAME
Name: radar
Value: trendyol-firsat-radari.onrender.com
```

Kok domain kullanacaksan Render panelinin verdigi kaydi aynen gir. DNS yayilmasi birkac dakika ile birkaç saat surebilir.

## Guvenlik

Uygulama internete acilacagi icin `APP_PASSWORD` mutlaka guclu olsun. NKS cookie veya API key bilgilerini koda yazma; sadece Render Environment alaninda tut.
