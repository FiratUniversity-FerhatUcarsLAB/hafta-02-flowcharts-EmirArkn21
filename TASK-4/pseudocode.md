BAŞLA

    // --- 1. Değişkenler ---
    tanımla ogrenciNo, sifre, girisBasarili
    tanımla dersListesi, secilenDersler, dersSecim
    tanımla toplamKredi ← 0
    tanımla maksimumKredi ← 35
    tanımla kontenjanBos, onKosulTamam, zamanUygun, danismanOnayi
    tanımla GPA

    secilenDersler ← boş liste
    girisBasarili ← false

    // --- 2. Öğrenci Girişi ---
    DÖNGÜ maksimumDeneme > 0 İKEN
        YAZ "OKU Öğrenci No:"
        OKU ogrenciNo
        YAZ "OKU Şifre:"
        OKU sifre

        EĞER DogrulaOgrenci(ogrenciNo, sifre) = true İSE
            girisBasarili ← true
            GPA ← GetirGPA(ogrenciNo)
            YAZ "Giriş başarılı. GPA: ", GPA
            ÇIK DÖNGÜDEN
        DEĞİLSE
            maksimumDeneme ← maksimumDeneme - 1
            YAZ "Hatalı giriş. Kalan deneme: ", maksimumDeneme
            EĞER maksimumDeneme = 0 İSE
                YAZ "Giriş başarısız. Sistemden çıkılıyor."
                DUR
            BİTİR
        BİTİR
    SON

    // --- 3. Ders Listesi Gösterme ---
    dersListesi ← GetirMevcutDersler()
    YAZ "Mevcut Dersler:"
    DÖNGÜ her ders IN dersListesi
        YAZ ders
    SON

    // --- 4. Ders Seçim Döngüsü ---
    DÖNGÜ kullanıcıDersSecmekİsterMi = true İKEN
        YAZ "OKU Seçmek istediğiniz ders ID (veya çıkmak için H):"
        OKU dersSecim
        EĞER dersSecim = "H" İSE
            ÇIK DÖNGÜDEN
        BİTİR

        // --- 5. Kontrol Mekanizmaları ---
        kontenjanBos ← KontrolEtKontenjan(dersSecim)
        onKosulTamam ← KontrolEtOnKosul(ogrenciNo, dersSecim)
        zamanUygun ← KontrolEtZamanCakis(secilenDersler, dersSecim)
        dersKredi ← GetirDersKredisi(dersSecim)
        EĞER GPA < 2.5 İSE
            danismanOnayi ← KontrolEtDanisman(dersSecim)
        DEĞİLSE
            danismanOnayi ← true
        BİTİR

        // --- 6. Karar Noktaları ---
        EĞER kontenjanBos = HAYIR İSE
            YAZ "Ders kontenjanı dolu. Seçim yapılamıyor."
        DEĞİLSE EĞER onKosulTamam = HAYIR İSE
            YAZ "Ön koşul dersleri tamamlanmamış. Seçim yapılamıyor."
        DEĞİLSE EĞER zamanUygun = HAYIR İSE
            YAZ "Ders saatleri çakışıyor. Seçim yapılamıyor."
        DEĞİLSE EĞER toplamKredi + dersKredi > maksimumKredi İSE
            YAZ "Kredi limiti aşılıyor. Seçim yapılamıyor."
        DEĞİLSE EĞER danismanOnayi = HAYIR İSE
            YAZ "Danışman onayı gerekli. Seçim yapılamıyor."
        DEĞİLSE
            // Ders ekleme başarılı
            secilenDersler ← secilenDersler + dersSecim
            toplamKredi ← toplamKredi + dersKredi
            YAZ "Ders eklendi. Toplam kredi: ", toplamKredi
        BİTİR

        // --- 7. Ders Çıkarma (Opsiyonel) ---
        YAZ "Ders çıkarmak ister misiniz? (E/H)"
        OKU dersCikarmakIstemi
        EĞER dersCikarmakIstemi = E İSE
            YAZ "Çıkarmak istediğiniz ders ID:"
            OKU dersCikim
            EĞER dersCikim IN secilenDersler İSE
                secilenDersler ← secilenDersler - dersCikim
                toplamKredi ← toplamKredi - GetirDersKredisi(dersCikim)
                YAZ "Ders çıkarıldı. Toplam kredi: ", toplamKredi
            DEĞİLSE
                YAZ "Ders seçiminizde bulunamadı."
            BİTİR
        BİTİR

    SON DÖNGÜ

    // --- 8. Kayıt Özeti ve Onay ---
    EĞER secilenDersler boş DEĞİLSE İSE
        YAZ "Seçilen Dersler:"
        DÖNGÜ her ders IN secilenDersler
            YAZ ders
        SON
        OnaylaDersKayit(ogrenciNo, secilenDersler)
        YAZ "Ders kaydınız başarıyla tamamlandı."
    DEĞİLSE
        YAZ "Hiç ders seçilmedi. Kayıt tamamlanmadı."
    BİTİR

BİTİR
