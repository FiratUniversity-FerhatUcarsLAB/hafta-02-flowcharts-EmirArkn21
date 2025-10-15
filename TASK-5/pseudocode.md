BAŞLA

    // --- 1. Değişkenler ---
    tanımla sistemAktif ← true
    tanımla hareketSensoru, kapiPencereSensoru, dumanSensoru
    tanımla kameraAktif, sirenAktif
    tanımla evSahibiEvde, yanlisAlarm
    tanımla tehditSeviyesi // 0: yok, 1: düşük, 2: orta, 3: yüksek
    tanımla bildirimGonderildi
    tanımla alarmSifirla ← false

    YAZ "Akıllı Ev Güvenlik Sistemi 7/24 çalışıyor..."

    // --- 2. Ana Sonsuz Döngü ---
    DÖNGÜ sistemAktif = true İKEN

        // --- 2a. Sensörleri oku ---
        hareketSensoru ← OkuHareketSensoru()
        kapiPencereSensoru ← OkuKapiPencereSensoru()
        dumanSensoru ← OkuDumanSensoru()

        // --- 2b. Tehdit Seviyesini Belirle ---
        tehditSeviyesi ← 0

        EĞER hareketSensoru = TESPIT EDILDI VE evSahibiEvde = HAYIR İSE
            tehditSeviyesi ← max(tehditSeviyesi, 2) // Orta
        BİTİR

        EĞER kapiPencereSensoru = ACILDI VE evSahibiEvde = HAYIR İSE
            tehditSeviyesi ← max(tehditSeviyesi, 3) // Yüksek
        BİTİR

        EĞER dumanSensoru = TESPIT EDILDI İSE
            tehditSeviyesi ← max(tehditSeviyesi, 3) // Yüksek
        BİTİR

        // --- 2c. Alarm ve Kamera Aktivasyonu ---
        EĞER tehditSeviyesi = 0 İSE
            sirenAktif ← false
            kameraAktif ← false
            YAZ "Ev güvenli."
        DEĞİLSE
            kameraAktif ← true
            EĞER tehditSeviyesi = 2 İSE
                sirenAktif ← true
                YAZ "Orta seviye tehdit! Siren aktif."
            DEĞİLSE EĞER tehditSeviyesi = 3 İSE
                sirenAktif ← true
                YAZ "Yüksek seviye tehdit! Siren aktif ve alarm bildirimi gönderiliyor."
            BİTİR
        BİTİR

        // --- 2d. Bildirim Gönderme ---
        EĞER tehditSeviyesi > 1 VE yanlisAlarm = HAYIR İSE
            bildirimGonderildi ← GonderBildirim(SMS + App + Email)
            YAZ "Bildirim gönderildi."
        DEĞİLSE
            YAZ "Bildirim gönderilmedi."
        BİTİR

        // --- 2e. Alarm Sıfırlama Kontrolü ---
        EĞER alarmSifirla = true İSE
            sirenAktif ← false
            kameraAktif ← false
            tehditSeviyesi ← 0
            YAZ "Alarm sıfırlandı, sistem normal duruma döndü."
            alarmSifirla ← false
        BİTİR

        // --- 2f. Kısa Bekleme ---
        SLEEP(1 saniye)

    SON DÖNGÜ

BİTİR
