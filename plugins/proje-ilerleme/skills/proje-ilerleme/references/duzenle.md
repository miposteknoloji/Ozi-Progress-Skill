# duzenle — Dağınık dokümanları modül yapısına taşıma

Onlarca dağınık MD dosyası olan bir projeyi, bilgi kaybetmeden ve geri alınabilir şekilde
modül yapısına taşır. Sonunda proje, skill'in günlük modlarıyla (`oturum-ac`, `oturum-kapat`,
`toparla`) çalışmaya hazır olur.

## İçindekiler
1. Genel kurallar
2. Geçiş planı dosyası (durum + hedef yapı)
3. `envanter`
4. `iskelet`
5. `modul <ad>`
6. `kok`
7. `dogrula`

## 1. Genel kurallar

- **Aşamalar ayrı oturumlarda yapılır.** Her aşama sonucunu `DOCS_GECIS_PLANI.md`'ye yazar.
  Aşama bitince kullanıcıya `/clear` yapmasını ve sıradaki komutu söyle; bir sonraki aşama
  gereken bilgiyi dosyadan okur. Böylece 60 dosyalık bir taşıma tek bir context'e sığmak zorunda kalmaz.
- **Geri alınabilirlik.** İlk aşamadan önce git çalışma alanı temiz olmalı ve iş `docs-duzenleme`
  dalında yapılmalı. Değilse kullanıcıya yedek commit ve dal açmayı öner, onaysız devam etme.
- **Bilgi kaybı yok.** Taşımalar `git mv` ile yapılır (geçmiş korunur). Birleştirilen eski dosya
  yalnız içeriği yeni dosyaya tamamen geçtikten sonra `git rm` ile kaldırılır.
- **Çelişkide seçme, sor.** İki dosya farklı tablo adı, akış veya karar söylüyorsa birini seçme;
  listele ve kullanıcıya sor.
- **Okuma subagent ile.** Dosyaları tek tek veya küçük gruplar halinde subagent'lara okut; ana
  oturuma yalnız özet gelsin. HTML ve PDF kopyalarını okuma, yalnız adlarını not et.
- **Hedef yapı esnek.** Varsayılan `docs/core/` + `docs/modules/<kod>-<ad>/`'dir. Kullanıcı
  numaralı veya farklı klasör adları isterse (ör. `docs/01-sistem/`) geçiş planındaki
  "Hedef yapı" esas alınır.

## 2. Geçiş planı dosyası

Kök dizinde `DOCS_GECIS_PLANI.md`. Bu dosya hem aşamaların durumunu hem projeye özgü bilgileri
(profil) taşır; skill'in projeden projeye değişen her şeyi buradan okunur.

```markdown
# Doküman Geçiş Planı — <Proje>

## Aşama durumu
- [ ] envanter  - [ ] iskelet  - [ ] kok  - [ ] dogrula
- Modüller: <klasör> [ ] · <klasör> [ ] · ...
- Sıradaki: <komut>

## Hedef yapı (profil)
| Klasör | Tür (çekirdek/modül/genel) | Kapsam | Alt klasörler |
|---|---|---|---|

### Kısaltmalar
| Kısaltma | Anlamı | Modül |
|---|---|---|

### Mimari kurallar (tüm modüller)
- <ör. Dış sisteme yazma yalnız <entegrasyon klasörü> üzerinden>

### Geçiş sırası
<önce bağımlı olunan çekirdek parçalar, sonra modüller; büyük modül alt klasör bazında bölünebilir>

### Doğrulama senaryoları
- <ör. "X modülünde Y akışına devam etmek">

### Özel klasörler
- Arşiv: docs/_arsiv/ · Export (HTML/PDF kopyalar): docs/_export/

## Dosya işlemleri
| # | Mevcut yol | Yeni yol | İşlem (Taşı/Birleştir/Böl/Arşivle/Export) | Gerekçe | Durum |
|---|---|---|---|---|---|

## Sorular
- [ ] <soru>

## Referans güncellemeleri
| Eski yol | Yeni yol | Referans veren dosya |
|---|---|---|
```

## 3. `envanter`

Hiçbir dosyayı taşıma, silme veya değiştirme; yalnız iki yeni dosya yaz.

1. Ön koşulları kontrol et (temiz çalışma alanı, `docs-duzenleme` dalı).
2. Projedeki tüm MD dosyalarını listele (`node_modules`, `.git`, build klasörleri hariç).
   Kök CLAUDE.md ve README'yi oku; diğerlerini subagent'larla oku.
3. `DOCS_ENVANTER.md` yaz:
   `Dosya | Boyut | Önerilen modül | Tür (Tasarım/Plan/Yapılacaklar/Oturum notu/Referans) | Durum (Aktif/Kısmen tamamlanmış/Tamamlanmış/Belirsiz) | Örtüştüğü dosyalar | Tek cümlelik özet`
4. İçeriklerden ve dosya adlarından hedef yapıyı çıkar: hangi konular ortak servis/entegrasyon
   (çekirdek), hangileri ayrı satılabilir/açılıp kapatılabilir iş alanı (modül). Bunu
   `DOCS_GECIS_PLANI.md` "Hedef yapı" bölümüne öneri olarak yaz. Kullanıcı modül listesini önceden
   verdiyse onu esas al.
5. Her dosya için işlem öner. Hedef dosya tipleri: CLAUDE.md, PROGRESS.md, KAPSAM.md,
   TASARIM.md (ne ve neden). Alt konusu çok olan modülde alt klasör öner. 25 KB'ı geçen aktif
   dosyaya bölme öner. Tamamlanmış plan ve tarihli oturum notları → arşiv. HTML/PDF → export.
6. Anlamından emin olmadığın kısaltmaları tahmin etme; "Kısaltmalar" tablosuna `?` ile yaz ve
   "Sorular"a ekle. Belirsiz modül eşleşmeleri de "Sorular"a.
7. Aşama durumunu güncelle. Rapor: dosya sayısı, modül başına dosya sayısı, birleştirme/bölme/
   arşiv önerisi sayısı, sorular.
8. Kullanıcıya söyle: "Planı gözden geçir, soruları cevapla, yanlış eşleştirmeleri düzelt.
   Sonraki aşamalar bu dosyayı esas alır. Bitince `/clear` ve `iskelet`."

## 4. `iskelet`

Yalnız içerik değişikliği gerektirmeyen işler.

1. Plandaki "Sorular" açıksa dur ve önce onları sor.
2. Hedef klasör yapısını oluştur (alt klasörler dahil).
3. HTML ve PDF kopyalarını `git mv` ile export klasörüne taşı.
4. "Arşivle" işaretli dosyaları `git mv` ile arşive taşı.
5. Genel/referans dosyalarını (klasör yapısı, kurulum, db nesneleri vb.) taşı ve planlanan adı ver.
6. Birleştirme veya bölme gerektiren dosyalara dokunma.
7. Plandaki satırları ve aşama durumunu işaretle. Onayla commit:
   `docs: folder skeleton, export and archive moves`. Sonra `/clear` ve geçiş sırasındaki ilk modül.

## 5. `modul <ad>`

Her modül (veya büyük modülün bir alt klasörü) için ayrı oturum. Diğer modüllerin dosyalarını okuma.

1. Planda bu modüle atanmış dosyaları subagent'larla oku.
2. Dönüştür:
   - Birleştirilecekleri birleştir; çelişkileri listeleyip sor.
   - 25 KB'ı geçen dosyayı anlamlı bölümlere ayır.
   - Tamamlanmış maddeleri aktif plandan çıkar; PROGRESS.md "Tamamlananlar"a tek satırlık özet
     olarak ekle. Uzun tamamlanmış bölümlerin orijinalini `docs/_arsiv/<modül>/` altına taşı.
3. Skill şablonlarıyla ([sablonlar.md](sablonlar.md)) modül klasöründe oluştur:
   - CLAUDE.md (~3 KB): amaç, modüle özel kurallar, çekirdekle ve diğer modüllerle ilişki,
     klasördeki dosyalar ve ne zaman okunacakları, plandaki ilgili mimari kurallar.
   - PROGRESS.md: mevcut planlardaki bekleyen maddelerden "Yapılacaklar", yarım işlerden
     "Yarım kalanlar", dokümanlarda geçen kararlardan "Kararlar", tablo/alan/API bilgisinden
     "Veri / entegrasyon notları". "Sıradaki adım"ı somut doldur; emin değilsen ❓ ile sor.
   - KAPSAM.md: dokümanlarda kapsam bilgisi varsa.
4. Modülün dokümanı hiç yoksa: yalnız CLAUDE.md ve PROGRESS.md iskeleti oluştur, olgunluk
   "Fikir"; diğer modül dokümanlarında bu modülle ilgili geçen gereksinimleri subagent ile tara
   ve kaynak dosyayı belirterek "Yapılacaklar"a ekle.
5. Bu modülün dosyalarına başka dokümanlarda veya kodda referans varsa planın "Referans
   güncellemeleri"ne ekle; henüz değiştirme.
6. Plandaki satırları ve aşama durumunu işaretle. Rapor: oluşan dosyalar ve boyutları,
   birleştirilen/bölünen dosyalar, arşive gidenler, çelişkiler ve sorular. Onayla commit:
   `docs: reorganize <modül>`. Sonra `/clear` ve sıradaki modül (tümü bittiyse `kok`).

## 6. `kok`

Modül detaylarını okuma; yalnız her modülün ve çekirdeğin CLAUDE.md'sini oku.

1. Kök CLAUDE.md'yi şablona göre yeniden yaz (≤5 KB): proje tanımı, değişmez kurallar, plandaki
   mimari kurallar, "Modüler yapı", doküman haritası, okuma kuralı ("bir modülde çalışırken
   yalnız onun CLAUDE.md ve PROGRESS.md'si"; arşiv yalnız istenirse; export hiçbir zaman),
   "İlerleme takibi". Eski kök CLAUDE.md'de bu kapsamın dışında kalan önemli bilgi varsa silme,
   kullanıcıya göster.
2. Kök PROGRESS.md, `docs/00-urun/MODUL_KATALOGU.md` ve `YOL_HARITASI.md`'yi oluştur; modül
   satırlarını modül PROGRESS'lerinden doldur. Plandaki kısaltmalar sözlüğünü katalogun sonuna ekle.
3. Plandaki "Referans güncellemeleri"ni uygula (kod, doküman, yorumlar).
4. Export ve arşivin Claude tarafından taranmaması için kök CLAUDE.md kuralına ek olarak gerekli
   ayarı öner (ör. `.claude/settings.json` içinde okuma izni reddi), kullanıcı onayıyla.
5. Aşama durumunu işaretle. Onayla commit: `docs: root CLAUDE.md, progress and references`.

## 7. `dogrula`

Hiçbir şeyi değiştirme, yalnız raporla:

1. Hiçbir modüle ait olmayan (yetim) MD dosyası var mı?
2. Kod veya dokümanlarda artık var olmayan bir yola referans var mı?
3. 25 KB'ı geçen aktif (arşiv ve export dışı) dosya var mı?
4. Her modülde CLAUDE.md ve PROGRESS.md var mı, "Sıradaki adım" dolu mu?
5. Plandaki her doğrulama senaryosu için: yeni bir oturumdaymış gibi kök CLAUDE.md'den başla;
   hangi dosyaları okurdun, toplam kaç KB eder? Hedef: tek bir görev için okunan toplam
   dokümanın birkaç on KB'ı geçmemesi.

Sorun varsa düzeltme önerisiyle listele. Temizse:
- `DOCS_ENVANTER.md` ve `DOCS_GECIS_PLANI.md`'yi `docs/_arsiv/` altına taşımayı,
- `docs-duzenleme` dalını ana dala birleştirmeyi öner.
Bundan sonra proje günlük modlarla (`oturum-ac`, `oturum-kapat`, `toparla`) çalışır.
