# Sayman Taşımacılık — mevcut Nginx bulunan VPS'te Docker kurulumu

Bu proje statik bir web sitesidir. Docker Compose siteyi bir Nginx konteynerinde çalıştırır. Konteyner yalnızca VPS'nin `127.0.0.1:8087` adresinde erişilebilir; dışarıya açık 80/443 portları mevcut VPS Nginx'inde kalır.

## Gereksinimler

- VPS üzerinde Docker Engine ve Docker Compose eklentisi
- `saymantasimacilik.com` DNS kaydının VPS'ye yönlenmesi
- VPS üzerinde çalışan Nginx ve HTTPS sertifikası yönetimi
- Sunucuda `127.0.0.1:8087` portunun başka bir servis tarafından kullanılmaması

## Kurulum

Depoyu VPS'ye kopyalayın veya klonlayın. Proje dizininde:

```sh
docker compose up -d --build
docker compose ps
curl -I http://127.0.0.1:8087/
```

[`deploy/vps-nginx.conf`](deploy/vps-nginx.conf) dosyasını VPS'de `/etc/nginx/sites-available/saymantasimacilik.com` konumuna örnek olarak kopyalayın. Sunucunuzun mevcut Nginx dosya düzeni farklıysa aynı `server_name` ve `proxy_pass` ayarlarını o düzene ekleyin. Bu alan adı için zaten bir Nginx `server` bloğu varsa ikinci bir blok açmayın; mevcut bloğun `location /` bölümünü konteynere yönlendirin.

```sh
sudo ln -s /etc/nginx/sites-available/saymantasimacilik.com /etc/nginx/sites-enabled/saymantasimacilik.com
sudo nginx -t
sudo systemctl reload nginx
```

Sembolik bağlantı zaten varsa tekrar oluşturmayın. HTTPS için mevcut sertifika yönetiminizi kullanın. Certbot'un Nginx eklentisini kullanıyorsanız ve bu alan adı için sertifika henüz yoksa `sudo certbot --nginx -d saymantasimacilik.com` çalıştırabilirsiniz. Sertifika kurulduktan sonra `https://saymantasimacilik.com` adresini kontrol edin.

## Güncelleme ve bakım

Yeni kodu aldıktan sonra `docker compose up -d --build` çalıştırın. Durum için `docker compose ps`, loglar için `docker compose logs --tail=100 web` kullanın. Konteyneri durdurmak için `docker compose down` çalıştırın.

Site dosyaları imajın içine kopyalanır. Google Fonts ve Google Maps içerikleri ziyaretçinin tarayıcısında harici servislerden yüklenir.
