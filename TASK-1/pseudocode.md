BAŞLA

    // --- DEĞİŞKEN TANIMLARI ---
    tanımla dogruPIN ← 1234
    tanımla girilenPIN, bakiye, cekilecekTutar, gunlukLimit, cekilenToplam, hak
    bakiye ← 5000
    gunlukLimit ← 2000
    cekilenToplam ← 0
    hak ← 3

    // --- PIN DOĞRULAMA ---
    DÖNGÜ hak > 0 İKEN
        YAZ "Lütfen PIN kodunuzu giriniz: "
        OKU girilenPIN

        EĞER girilenPIN = dogruPIN İSE
            YAZ "PIN doğrulandı."
            ÇIK DÖNGÜDEN
        DEĞİLSE
            hak ← hak - 1
            EĞER hak > 0 İSE
                YAZ "Hatalı PIN! Kalan deneme hakkı: ", hak
            DEĞİLSE
                YAZ "3 kez hatalı giriş yaptınız. Kartınız bloke edildi!"
                DUR
            BİTİR
        BİTİR
    SON

    // --- PARA ÇEKME İŞLEMİ DÖNGÜSÜ ---
    DÖNGÜ true İKEN
        YAZ "Çekmek istediğiniz tutarı giriniz (20 TL katları olmalıdır): "
        OKU cekilecekTutar

        // --- TUTAR KONTROLÜ ---
        EĞER cekilecekTutar % 20 ≠ 0 İSE
            YAZ "Hata: Tutar 20 TL'nin katı olmalıdır!"
            DEVAM ET DÖNGÜYE
        BİTİR

        // --- BAKİYE KONTROLÜ ---
        EĞER cekilecekTutar > bakiye İSE
            YAZ "Yetersiz bakiye! Mevcut bakiye: ", bakiye, " TL"
            DEVAM ET DÖNGÜYE
        BİTİR

        // --- GÜNLÜK LİMİT KONTROLÜ ---
        EĞER cekilenToplam + cekilecekTutar > gunlukLimit İSE
            YAZ "Günlük limit aşılamaz! Kalan limit: ", gunlukLimit - cekilenToplam, " TL"
            DEVAM ET DÖNGÜYE
        BİTİR

        // --- PARA ÇEKME İŞLEMİ ---
        bakiye ← bakiye - cekilecekTutar
        cekilenToplam ← cekilenToplam + cekilecekTutar

        YAZ "İşlem başarılı! Çekilen tutar: ", cekilecekTutar, " TL"
        YAZ "Kalan bakiye: ", bakiye, " TL"
        YAZ "Bugün çekilen toplam: ", cekilenToplam, " TL"

        // --- İŞLEM TEKRARI SEÇENEĞİ ---
        YAZ "Başka bir işlem yapmak istiyor musunuz? (E/H): "
        OKU cevap

        EĞER cevap = "E" veya cevap = "e" İSE
            DEVAM ET DÖNGÜYE
        DEĞİLSE
            YAZ "Kartınızı almayı unutmayın. İyi günler!"
            ÇIK DÖNGÜDEN
        BİTİR

    SON

BİTİR
