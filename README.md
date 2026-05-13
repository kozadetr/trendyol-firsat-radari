# Trendyol Fırsat Radarı

Streamlit arayüzü ile Trendyol kategori sayfalarındaki çok satan ürünleri tarar, ürün sayfasındaki gömülü `window["__envoy_product-info__PROPS"]` verisinden satıcı/varyant bazlı `quantity` stoklarını okur ve 3 günlük satış adediyle karşılaştırır.

Fırsat kuralı:

```text
satıcı/varyant stoku <= son 3 günlük satış adedi
```

## Çalıştırma

```bash
streamlit run app.py
```

Şifre koruması açmak için ortam değişkeni ver:

```bash
APP_PASSWORD=guclu-bir-sifre streamlit run app.py
```

Windows PowerShell:

```powershell
$env:APP_PASSWORD="guclu-bir-sifre"
.venv\Scripts\streamlit run app.py
```

## Online Yayınlama

Render için hazır ayarlar `render.yaml` dosyasındadır.

1. Projeyi GitHub'a yükle.
2. Render'da `New Web Service` seç.
3. GitHub reposunu bağla.
4. Build command:

```bash
pip install -r requirements.txt
```

5. Start command:

```bash
streamlit run app.py --server.port $PORT --server.address 0.0.0.0 --server.headless true
```

6. Environment bölümüne `APP_PASSWORD` ekle.
7. Nekadarsatti ücretli üyelik entegrasyonu için gerekirse şu değişkenleri ekle:

```text
NEKADARSATTI_API_KEY=varsa-uyelik-api-key
NEKADARSATTI_COOKIE=nekadarsatti-oturum-cookie-degeri
NEKADARSATTI_QUERY_LIMIT=10000
```

`NEKADARSATTI_COOKIE` eklenirse uygulama Nekadarsatti isteklerinde bu oturumu kullanmaya hazırdır. API/cookie bilgisini yalnız Render Environment alanına koy; koda yazma.

Domain bağlamak için Render servisindeki `Settings > Custom Domains` alanına domaini ekleyip verdiği DNS kaydını domain paneline gir.

## Nekadarsatti hazırlığı

Uygulama Nekadarsatti bağlantısını tek yerden yönetir. Ücretli üyelik sonrası:

- Ürün linki bazlı satış sorgusunda `NEKADARSATTI_API_KEY` veya `NEKADARSATTI_COOKIE` kullanılabilir.
- Kategori çok satanlar araştırmasında aynı oturum bilgisi istek başlıklarına eklenir.
- `NEKADARSATTI_QUERY_LIMIT` ile seçilebilir sorgu üst sınırı artırılabilir.

Bu hazırlık güvenli taraftadır: üyelik bilgisi kodda tutulmaz, sadece environment değişkeninden okunur.

## Satış verisi kaynakları

İki mod var:

- `Trendyol ibareleri`: Ürün sayfasındaki “3 günde 100+ satış”, “1 günde 2K kişi görüntüledi”, “1000 kişinin sepetinde” gibi ibareleri ve JSON içindeki favori/yorum/çok satan sıra sinyallerini stokla karşılaştırır.
- `CSV / metin`: Nekadarsatti sonuçlarını sen yüklersin veya yapıştırırsın.
- `Nekadarsatti otomatik`: Uygulama kategori listesindeki seçtiğin sayıda ürün linkini aynı IP'den nekadarsatti.com'a gönderir ve dönen `order` değerini son 3 günlük satış adedi olarak kullanır.

`Trendyol ibareleri` modu için varsayılan fırsat kuralı:

```text
stok <= 150
ve
(3 günlük satış ibaresi >= 100
 veya 1 günlük görüntülenme >= 2000
 veya sepette >= 1000
 veya favori >= 10000
 veya çok satan sıra <= 20)
```

Otomatik modun sorgu üst sınırı `NEKADARSATTI_QUERY_LIMIT` ile yönetilir. Üyelik/cookie yoksa varsayılan deneme limiti 30, üyelik oturumu tanımlanırsa varsayılan 10000 olur.

Kategori seçiminden sonra ilgili kategori için önerilen markalar marka filtresinde görünür. Ek markaları virgülle yazabilirsin.

## Marka ve stok sekmesi

`Marka ve stok` sekmesinde kategori, liste türü, marka ve maksimum ürün stoku seçilir. Uygulama seçilen listedeki ürünleri tarar, sadece seçilen markaya ait olanları bırakır ve ürünün tüm satıcıları içindeki en yüksek stok değeri maksimum stok eşiğini aşmıyorsa ürünü listeler.

Sonuçta ürün başına tek satır görünür; ürün adına bağlı Trendyol linki, en yüksek stok satıcısı, ürün max stok değeri ve diğer satıcı stokları birlikte gösterilir.

## CSV / metin formatı

`nekadarsatti.com` verisini CSV olarak yükleyebilir veya metin alanına yapıştırabilirsin. Uygulama şu kolon adlarını otomatik tanır:

- Ürün ID, product_id, id
- Link, url, ürün linki
- 3 günlük satış, sales_3d, satış, adet

Örnek CSV:

```csv
Ürün ID,3 günlük satış
123456789,18
987654321,7
```

Örnek yapıştırma:

```text
https://www.trendyol.com/marka/urun-p-123456789 18
987654321, 7
```

## Notlar

- Trendyol veya nekadarsatti tarafında oturum/anti-bot kısıtı varsa kategori ya da ürün sayfası okunamayabilir.
- `Satıcı filtresi` alanına örneğin `Saat & Saat` yazarsan sadece o satıcının stok satırları fırsat hesabına girer.
- Eski tek ürün stok okuyucu ikinci sekmede korunur.
