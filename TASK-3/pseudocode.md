Modül 1 : 
BAŞLA

    // --- 1. Değişkenler ---
    tanımla tcNo, sifre, kullaniciDogrulandi
    tanımla poliklinikSecim, doktorListesi, doktorSecim
    tanımla uygunSaatler, saatSecim
    tanımla randevuOnay, smsGonderildi
    tanımla maksimumDeneme ← 3

    kullaniciDogrulandi ← false

    // --- 2. Kimlik Doğrulama ---
    BAŞLA Kimlik_Dogrulama
        DÖNGÜ maksimumDeneme > 0 İKEN
            YAZ "OKU TC No:"
            OKU tcNo
            YAZ "OKU Şifre:"
            OKU sifre

            EĞER DogrulaKullanici(tcNo, sifre) = true İSE
                kullaniciDogrulandi ← true
                YAZ "Giriş başarılı."
                ÇIK DÖNGÜDEN
            DEĞİLSE
                maksimumDeneme ← maksimumDeneme - 1
                EĞER maksimumDeneme > 0 İSE
                    YAZ "Hatalı giriş. Kalan deneme: ", maksimumDeneme
                DEĞİLSE
                    YAZ "Giriş başarısız. Daha sonra tekrar deneyin."
                    DUR
                BİTİR
            BİTİR
        SON
    BİTİR Kimlik_Dogrulama

    // --- 3. Poliklinik Seçimi ---
    BAŞLA Poliklinik_Secimi
        poliklinikListesi ← GetirPoliklinikler()
        YAZ "Mevcut Poliklinikler: ", poliklinikListesi
        YAZ "OKU Seçmek istediğiniz poliklinik ID:"
        OKU poliklinikSecim
    BİTİR Poliklinik_Secimi

    // --- 4. Doktor Listesi ve Seçimi ---
    BAŞLA Doktor_Secimi
        doktorListesi ← GetirDoktorlar(poliklinikSecim)
        YAZ "Polikliniğe ait doktorlar: ", doktorListesi
        YAZ "OKU Doktor ID seçiniz:"
        OKU doktorSecim
    BİTİR Doktor_Secimi

    // --- 5. Uygun Saatleri Göster ---
    BAŞLA Saat_Goruntuleme
        uygunSaatler ← GetirUygunSaatler(doktorSecim)
        EĞER uygunSaatler boş DEĞİLSE İSE
            YAZ "Uygun saatler: ", uygunSaatler
            YAZ "OKU Saat seçiniz:"
            OKU saatSecim
        DEĞİLSE
            YAZ "Seçilen doktor için uygun saat bulunmamaktadır."
            DUR
        BİTİR
    BİTİR Saat_Goruntuleme

    // --- 6. Randevu Onaylama ---
    BAŞLA Randevu_Onay
        randevuOnay ← RandevuOlustur(tcNo, doktorSecim, saatSecim)
        EĞER randevuOnay = true İSE
            YAZ "Randevunuz başarıyla oluşturuldu."
        DEĞİLSE
            YAZ "Randevu oluşturulamadı. Lütfen tekrar deneyin."
            DUR
        BİTİR
    BİTİR Randevu_Onay

    // --- 7. SMS Gönderme ---
    BAŞLA SMS_Gonder
        smsGonderildi ← SMSGonder(tcNo, doktorSecim, saatSecim)
        EĞER smsGonderildi = true İSE
            YAZ "Randevu bilgisi SMS ile gönderildi."
        DEĞİLSE
            YAZ "SMS gönderilemedi."
        BİTİR
    BİTİR SMS_Gonder

BİTİR


Modül 2 : 

BAŞLA

    // --- 1. Değişkenler ---
    tanımla tcNo, sifre, kullaniciDogrulandi
    tanımla tahlilListesi, tahlilSecim
    tanımla sonucHazir, pdfIndirildi
    tanımla maksimumDeneme ← 3

    kullaniciDogrulandi ← false

    // --- 2. Kimlik Doğrulama ---
    BAŞLA Kimlik_Dogrulama
        DÖNGÜ maksimumDeneme > 0 İKEN
            YAZ "OKU TC No:"
            OKU tcNo
            YAZ "OKU Şifre:"
            OKU sifre

            EĞER DogrulaKullanici(tcNo, sifre) = true İSE
                kullaniciDogrulandi ← true
                YAZ "Giriş başarılı."
                ÇIK DÖNGÜDEN
            DEĞİLSE
                maksimumDeneme ← maksimumDeneme - 1
                EĞER maksimumDeneme > 0 İSE
                    YAZ "Hatalı giriş. Kalan deneme: ", maksimumDeneme
                DEĞİLSE
                    YAZ "Giriş başarısız. Daha sonra tekrar deneyin."
                    DUR
                BİTİR
            BİTİR
        SON
    BİTİR Kimlik_Dogrulama

    // --- 3. Tahlil Listesi Kontrol ---
    BAŞLA Tahlil_Listesi_Kontrol
        tahlilListesi ← GetirTahliller(tcNo)
        EĞER tahlilListesi boş DEĞİLSE İSE
            YAZ "Mevcut tahliller: ", tahlilListesi
            YAZ "OKU Görüntülemek istediğiniz tahlil ID:"
            OKU tahlilSecim
        DEĞİLSE
            YAZ "Herhangi bir tahlil bulunmamaktadır."
            DUR
        BİTİR
    BİTİR Tahlil_Listesi_Kontrol

    // --- 4. Sonuç Hazır mı Kontrol ---
    BAŞLA Sonuc_Hazir_Mi
        sonucHazir ← KontrolEtSonucHazir(tahlilSecim)
        EĞER sonucHazir = true İSE
            YAZ "Tahlil sonucu hazır."
        DEĞİLSE
            YAZ "Sonuç henüz hazır değil. Lütfen daha sonra tekrar deneyin."
            DUR
        BİTİR
    BİTİR Sonuc_Hazir_Mi

    // --- 5. Sonucu Görüntüleme ---
    BAŞLA Sonuc_Goruntuleme
        YAZ "Tahlil sonucu: ", GetirSonuc(tahlilSecim)
    BİTİR Sonuc_Goruntuleme

    // --- 6. PDF İndirme ---
    BAŞLA PDF_Indirme
        pdfIndirildi ← PDFOlusturVeIndir(tahlilSecim)
        EĞER pdfIndirildi = true İSE
            YAZ "PDF başarıyla indirildi."
        DEĞİLSE
            YAZ "PDF indirilemedi."
        BİTİR
    BİTİR PDF_Indirme

BİTİR


Birleştirilmiş :

BAŞLA

    // --- 1. Değişkenler ---
    tanımla secim
    tanımla cikis ← false

    DÖNGÜ cikis = false İKEN

        // --- 2. Ana Menü ---
        YAZ "Ana Menü:"
        YAZ "1 - Randevu Alma"
        YAZ "2 - Tahlil Sonuçlarını Görüntüleme"
        YAZ "3 - Çıkış"
        YAZ "OKU Seçiminizi yapınız (1/2/3):"
        OKU secim

        EĞER secim = 1 İSE
            // --- Modül 1: Randevu Alma ---
            BAŞLA Randevu_Alma

                // Kimlik doğrulama
                BAŞLA Kimlik_Dogrulama
                    // (Modül 1 pseudocode’daki kimlik doğrulama adımları)
                BİTİR Kimlik_Dogrulama

                // Poliklinik ve doktor seçimi
                BAŞLA Poliklinik_ve_Doktor
                    // Poliklinik seçimi, doktor listesi, uygun saatleri gösterme
                BİTİR Poliklinik_ve_Doktor

                // Randevu oluşturma ve SMS
                BAŞLA Randevu_Onay_ve_SMS
                    // Randevu onaylama, SMS gönderme
                BİTİR Randevu_Onay_ve_SMS

            BİTİR Randevu_Alma

        DEĞİLSE EĞER secim = 2 İSE
            // --- Modül 2: Tahlil Sonuçları ---
            BAŞLA Tahlil_Sonuclari

                // Kimlik doğrulama
                BAŞLA Kimlik_Dogrulama
                    // (Modül 2 pseudocode’daki kimlik doğrulama adımları)
                BİTİR Kimlik_Dogrulama

                // Tahlil listesi ve sonuç kontrolü
                BAŞLA Tahlil_Secimi_ve_Sonuc
                    // Tahlil listesi gösterme, sonuç hazır mı kontrolü
                    // Sonucu görüntüleme ve PDF indirme
                BİTİR Tahlil_Secimi_ve_Sonuc

            BİTİR Tahlil_Sonuclari

        DEĞİLSE EĞER secim = 3 İSE
            cikis ← true
            YAZ "Sistemden çıkılıyor..."
        DEĞİLSE
            YAZ "Geçersiz seçim. Lütfen tekrar deneyin."

    SON DÖNGÜ

BİTİR
