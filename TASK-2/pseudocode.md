BAŞLA

    // --- DEĞİŞKENLER ---
    tanımla kullanici, oturumAcik, sepet, urunID, miktar, stok, fiyat, araToplam
    tanımla kuponKodu, kuponGecerliMi, kuponIndirim, kargoAdres, kargoSecimi, kargoUcreti
    tanımla vergi, toplamTutar, odemeYontemi, odemeBasarili, odemeId
    tanımla maksimumDeneme ← 3
    sepet ← boşListe
    oturumAcik ← false

    // --- 1. KULLANICI GİRİŞİ ---
    BAŞLA Kullanıcı_Girisi
        YAZ "Kullanıcı girişi yapılacak mı? (E/H)"
        OKU cevap
        EĞER cevap = "E" İSE
            DÖNGÜ maksimumDeneme > 0 İKEN
                YAZ "OKU e-posta veya kullanıcı adı"
                OKU girisEmail
                YAZ "OKU şifre"
                OKU girisSifre

                // --- Kimlik doğrulama kontrolü ---
                EĞER DogrulaKullanici(girisEmail, girisSifre) = true İSE
                    oturumAcik ← true
                    kullanici ← GetirKullanici(girisEmail)
                    YAZ "Giriş başarılı. Sepet yükleniyor..."
                    sepet ← YukleKullaniciSepeti(kullanici.id)
                    ÇIK DÖNGÜDEN
                DEĞİLSE
                    maksimumDeneme ← maksimumDeneme - 1
                    EĞER maksimumDeneme > 0 İSE
                        YAZ "Giriş hatası. Kalan deneme: ", maksimumDeneme
                    DEĞİLSE
                        YAZ "Giriş başarısız. Lütfen daha sonra tekrar deneyin."
                        DUR
                    BİTİR
                BİTİR
            SON
        DEĞİLSE
            YAZ "Misafir kullanıcı devam ediyor. Geçici sepet oluşturuluyor."
            sepet ← BosSepetOluştur()
        BİTİR
    BİTİR Kullanıcı_Girisi

    // --- 2. ÜRÜN EKLEME (SEPETE) ---
    BAŞLA Urun_Ekleme
        DÖNGÜ true İKEN
            YAZ "Ürün eklemek ister misiniz? (E/H)"
            OKU ekleCevap
            EĞER ekleCevap = "H" İSE
                ÇIK DÖNGÜDEN
            BİTİR

            YAZ "OKU urunID"
            OKU urunID
            YAZ "OKU miktar"
            OKU miktar

            // --- Stok kontrol ---
            stok ← GetirUrunStok(urunID)
            EĞER stok ≥ miktar İSE
                // -- Geçici rezervasyon veya hemen sepete ekle --
                EĞER oturumAcik = true İSE
                    // Oturumlu kullanıcı: kalıcı veya geçici rezervasyon politikasına göre
                    rezervasyonBasarili ← RezerveEt(urunID, miktar, kullanici.id)
                    EĞER rezervasyonBasarili = true İSE
                        sepet.Ekle(urunID, miktar)
                        YAZ "Ürün sepete eklendi."
                    DEĞİLSE
                        YAZ "Ürün rezervasyonu yapılamadı. Tekrar deneyin."
                    BİTİR
                DEĞİLSE
                    // Misafir: session tabanlı sepet
                    sepet.Ekle(urunID, miktar)
                    YAZ "Ürün (misafir) sepete eklendi."
                BİTİR
            DEĞİLSE
                YAZ "Stok yetersiz. Mevcut stok: ", stok
            BİTİR

        SON
    BİTİR Urun_Ekleme

    // --- 3. SEPET GÖRÜNTÜLE / GÜNCELLE ---
    BAŞLA Sepet_Islemleri
        YAZ "Sepetiniz:"
        sepetGoruntusu ← GetirSepetDetay(sepet)
        YAZ sepetGoruntusu

        DÖNGÜ true İKEN
            YAZ "Sepeti güncellemek ister misiniz? (adet değiştir/sil/devam) (D/S/V)"
            OKU secim
            EĞER secim = "D" İSE
                YAZ "OKU urunID"
                OKU urunID
                YAZ "OKU yeniAdet"
                OKU yeniAdet
                stok ← GetirUrunStok(urunID)
                EĞER stok ≥ yeniAdet İSE
                    sepet.GuncelleAdet(urunID, yeniAdet)
                    YAZ "Adet güncellendi."
                DEĞİLSE
                    YAZ "Stok yetersiz. Mevcut stok: ", stok
                BİTİR
            DEĞİLSE EĞER secim = "S" İSE
                YAZ "OKU urunID silinecek"
                OKU urunID
                sepet.Sil(urunID)
                YAZ "Ürün sepetten silindi."
            DEĞİLSE
                ÇIK DÖNGÜDEN
            BİTİR
        SON
    BİTİR Sepet_Islemleri

    // --- 4. İNDİRİM KODU / PROMOSYON ---
    BAŞLA Kupon_Uygula
        YAZ "Kupon kodu var mı? (E/H)"
        OKU kuponVar
        EĞER kuponVar = "E" İSE
            YAZ "OKU kuponKodu"
            OKU kuponKodu
            kuponGecerliMi, kuponIndirim ← KontrolEtKupon(kuponKodu, kullanici, sepet)
            EĞER kuponGecerliMi = true İSE
                UygulaKupon(sepet, kuponIndirim)
                YAZ "Kupon uygulandı: indirim = ", kuponIndirim
            DEĞİLSE
                YAZ "Kupon geçersiz veya koşullar sağlanmıyor."
            BİTİR
        DEĞİLSE
            YAZ "Kupon uygulanmadı."
        BİTİR
    BİTİR Kupon_Uygula

    // --- 5. KARGO HESAPLAMA ---
    BAŞLA Kargo_Hesapla
        YAZ "Teslimat adresi girilsin mi? (E/H)"
        OKU adresVar
        EĞER adresVar = "E" İSE
            YAZ "OKU adres bilgileri"
            OKU kargoAdres
        DEĞİLSE
            YAZ "Lütfen önce adres ekleyin."
            DUR
        BİTİR

        // Kargo seçeneklerini getir
        kargoSecenekleri ← HesaplaKargoSecenekleri(sepet, kargoAdres)
        YAZ "Mevcut kargo seçenekleri: ", kargoSecenekleri

        YAZ "Hangi kargo seçilsin? (ID gir)"
        OKU kargoSecimi
        kargoUcreti ← GetirKargoUcreti(kargoSecimi, sepet, kargoAdres)
        YAZ "Seçilen kargo ücreti: ", kargoUcreti
    BİTİR Kargo_Hesapla

    // --- 6. FİYAT, VERGİ VE TOPLAM HESAPLAMA ---
    BAŞLA Fiyat_Hesapla
        araToplam ← HesaplaAraToplam(sepet)   // ürün fiyatları * adet
        vergi ← HesaplaVergi(araToplam, kargoAdres)
        toplamTutar ← araToplam - sepet.indirim + vergi + kargoUcreti
        YAZ "Ara toplam: ", araToplam
        YAZ "İndirim: ", sepet.indirim
        YAZ "Vergi: ", vergi
        YAZ "Kargo: ", kargoUcreti
        YAZ "Toplam: ", toplamTutar
    BİTİR Fiyat_Hesapla

    // --- 7. ÖDEME AŞAMASI ---
    BAŞLA Odeme
        YAZ "Ödeme yöntemi seçiniz: (KrediKartı/ Havale/ Kapıda/ DijitalCuzdan)"
        OKU odemeYontemi

        // --- Ödeme doğrulama ve idempotency ---
        odemeDenemeSayisi ← 0
        DÖNGÜ odemeDenemeSayisi < maksimumDeneme İKEN
            odemeDenemeSayisi ← odemeDenemeSayisi + 1

            EĞER odemeYontemi = "KrediKartı" İSE
                YAZ "OKU kart bilgileri (kartNo, sonKullanma, cvv)"
                OKU kartBilgi
                // Tokenize ve gönder
                token ← TokenizeKart(kartBilgi)
                odemeBasarili, odemeId ← OdemeSaglayici.Ode(token, toplamTutar, idempotencyKey=OlusturKey())
            DEĞİLSE EĞER odemeYontemi = "Havale" İSE
                YAZ "Havale bilgileri verildi. Ödeme yapılana kadar bekle."
                odemeBasarili, odemeId ← HavaleBekleVeOnayla(kullanici, toplamTutar)
            DEĞİLSE EĞER odemeYontemi = "Kapıda" İSE
                // Kapıda ödeme politikasını kontrol et
                EĞER KapidaIzinVar(kargoAdres) = true İSE
                    odemeBasarili ← true
                    odemeId ← "KAPIDA-" + ZamanStamp()
                DEĞİLSE
                    YAZ "Kapıda ödeme bu adrese yapılamaz."
                    odemeBasarili ← false
                BİTİR
            DEĞİLSE
                // Dijital cüzdan vb.
                odemeBasarili, odemeId ← DijitalCuzdan.Ode(kullanici, toplamTutar)
            BİTİR

            EĞER odemeBasarili = true İSE
                YAZ "Ödeme başarılı. İD: ", odemeId
                ÇIK DÖNGÜDEN
            DEĞİLSE
                YAZ "Ödeme başarısız. Kalan deneme: ", maksimumDeneme - odemeDenemeSayisi
                EĞER odemeDenemeSayisi >= maksimumDeneme İSE
                    YAZ "Ödeme işlemi başarısız. Sipariş tamamlanamadı."
                    DUR
                DEĞİLSE
                    YAZ "Farklı bir ödeme yöntemi denemek ister misiniz? (E/H)"
                    OKU farkli
                    EĞER farkli = "E" İSE
                        YAZ "Yeni ödeme yöntemi seçiniz."
                        OKU odemeYontemi
                    DEĞİLSE
                        DUR
                    BİTİR
                BİTİR
            BİTİR
        SON
    BİTİR Odeme

    // --- 8. SIPARIŞ OLUŞTURMA VE STOK GÜNCELLEME (ATOMIC) ---
    BAŞLA Siparis_Olustur
        // Ödeme başarılıysa devam et
        EĞER odemeBasarili = true İSE
            // Başlat transaction
            BaşlatTransaction()
            başarılı ← true

            // Stokları kalıcı olarak düş
            DÖNGÜ her bir ürün in sepet İKEN
                stok ← GetirUrunStok(ürün.id)
                EĞER stok ≥ ürün.miktar İSE
                    azaltildi ← AzaltStok(ürün.id, ürün.miktar)
                    EĞER azaltildi = false İSE
                        başarılı ← false
                        YAZ "Stok güncelleme hatası ürün: ", ürün.id
                        ÇIK DÖNGÜDEN
                    BİTİR
                DEĞİLSE
                    başarılı ← false
                    YAZ "Sipariş iptal: stok yetersiz ürün: ", ürün.id
                    ÇIK DÖNGÜDEN
                BİTİR
            SON

            EĞER başarılı = true İSE
                siparisId ← OlusturSiparis(kullanici, sepet, kargoAdres, odemeId, toplamTutar)
                CommitTransaction()
                YAZ "Sipariş başarıyla oluşturuldu. Sipariş ID: ", siparisId
            DEĞİLSE
                RollbackTransaction()
                // Ödeme alındıysa iade başlat
                EĞER odemeBasarili = true İSE
                    YAZ "Ödeme alındı fakat stok hatası. İade başlatılıyor."
                    BaslatIade(odemeId)
                BİTİR
                DUR
            BİTİR
        DEĞİLSE
            YAZ "Ödeme yapılmadığı için sipariş oluşturulmadı."
            DUR
        BİTİR
    BİTİR Siparis_Olustur

    // --- 9. BİLDİRİMLER VE TAKİP ---
    BAŞLA Bildirimler
        YAZ "E-posta ve SMS ile sipariş onayı gönderiliyor..."
        GonderSiparisOnayi(kullanici.email, siparisId)
        YAZ "Fatura oluşturuluyor ve saklanıyor."
        OlusturVeSaklaFatura(siparisId)
    BİTİR Bildirimler

    // --- 10. İADE / GERI ÖDEME SENARYOSU (Kısaca) ---
    BAŞLA Iade_Islemleri
        YAZ "İade talebi gelirse:"
        YAZ "  - İade onayı kontrol et"
        YAZ "  - Ürün sağlandıysa stok geri yatır"
        YAZ "  - Ödeme sağlayıcı ile iade uyuşması yap ve kullanıcıyı bilgilendir"
        YAZ "  - Log ve metrikleri güncelle"
    BİTİR Iade_Islemleri

    // --- 11. KAYIT, LOG ve İZLEME ---
    BAŞLA Loglama
        Logla("Kullanıcı ID", kullanici.id)
        Logla("Sepet içeriği", sepet)
        Logla("Ödeme ID", odemeId)
        Logla("Sipariş ID", siparisId)
    BİTİR Loglama

BİTİR
