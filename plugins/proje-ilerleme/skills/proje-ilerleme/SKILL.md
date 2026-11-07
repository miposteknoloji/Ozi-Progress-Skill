---
name: proje-ilerleme
description: Claude Code projelerinde dosya tabanlı ilerleme takibi ve doküman düzeni. Yeni proje kurma, mevcut projeye uyarlama, dağınık MD dokümanlarını modüllere ayırma, modül ekleme, oturum açma/kapama, /compact öncesi kayıt, context sıfırlandıktan sonra devam, toparlama, yayın ve sürüm kontrolü. Kullanıcı "nerede kalmıştık", "PROGRESS'i güncelle", "oturumu kapat", "context doldu", "dokümanlar dağıldı", "yeni modül ekleyeceğiz", "projeyi toparla" dediğinde veya PROGRESS.md dosyaları olan bir projede oturuma başlarken bu skill'i kullan.
license: MIT
---

# Proje İlerleme Takibi

Context ne zaman dolarsa dolsun iş kaldığı yerden sürsün diye bilgi sohbette değil
dosyalarda tutulur. Bu skill o dosyaların yapısını kurar, düzenler ve her oturumda
güncel tutar. Projeler çekirdek + modüllerden oluşur; tek modüllü bir proje de aynı yapıyı
kullanır, ileride modül eklemek kolaylaşır.

## Mod seçimi

Argümana veya kullanıcının isteğine göre bir mod seç. Belli değilse kök PROGRESS.md var mı
bak: yoksa `kur`, `uyarla` veya `duzenle`'den hangisinin uyduğunu öner; varsa `oturum-ac` öner.

| Mod | Ne zaman |
|---|---|
| `kur` | Boş/yeni proje |
| `uyarla` | Başlamış, dokümanları makul düzende bir proje |
| `duzenle` | Dokümanları dağınık proje (çok sayıda, büyük, karışık adlı MD dosyası) |
| `modul-ekle` | Yeni modül fikri |
| `oturum-ac` | Her oturum başı |
| `gorev` | Yeni görev başlatma |
| `kaydet` | `/compact` öncesi, uzun işin ortası |
| `oturum-kapat` | Her oturum sonu |
| `devam` | Context sıfırlandıktan sonra |
| `moduller-arasi` | Birden fazla modülü veya çekirdeği etkileyen iş |
| `toparla` | Uzun aradan dönüş, büyük iş sonrası, dağınıklık hissi |
| `yayin-kontrol` | Modül yayına çıkmadan önce |
| `surum-kapat` | Sürüm çıkarken |

Modül verilmemişse kök PROGRESS.md "Odak" bölümünden en olası modülü öner, onay al. Oturumda
modül bir kez belirlendiyse sonraki modlarda aynısını kullan.

Şablonlar [references/sablonlar.md](references/sablonlar.md), `duzenle` modunun ayrıntılı akışı
[references/duzenle.md](references/duzenle.md) dosyasındadır (skill klasöründe:
`${CLAUDE_SKILL_DIR}/references/`). Yalnız ilgili mod gerektirdiğinde oku.

## Temel ilkeler

**İki katman dosya.**
- Modül ve çekirdek `PROGRESS.md`: görevler, yarım işler, kararlar, sıradaki adım. Her anlamlı
  adımda güncellenir; oturum sonunu beklemek, yarıda kesilen oturumda bilginin kaybolması demektir.
- Kök `PROGRESS.md`: odak, modüllerin genel durumu, kullanıcıdan beklenen kararlar, son toparlama.
  Yalnız **açık** maddeleri tutar; kapanan karar ilgili modülün "Kararlar"ında veya bir ADR'de
  kayıtlıdır, kökte tekrar durmasına gerek yoktur. Kök kısa kalırsa her oturum ucuza başlar.

**Modüler yapı.**
- Çekirdek (core): kimlik, kiracılık, modül açma/kapama, yetki, parametreler, loglama, ortak
  arayüz bileşenleri, dış sistem entegrasyon katmanları. Modüller bunları kendi içinde tekrar yazmaz.
- Modüller birbirini doğrudan çağırmaz; ortak ihtiyaç çekirdekte arayüz veya olay olarak
  tanımlanır. Böylece her modül tek başına açılıp kapatılabilir.
- Her modülün bir `module_key`'i vardır; kapalı modülün menüsü, API ucu ve zamanlanmış işleri
  çalışmaz. Bu kontrol çekirdektedir.
- Fiyat ve paket bilgisi projede (kod ve doküman) tutulmaz; kod yalnız `module_key` bilir.
- Olgunluk seviyeleri: Fikir → Tasarım → Geliştirme → Pilot → Yayında → Bakım.

**Varsayılan yapı:** `docs/00-urun/`, `docs/core/`, `docs/modules/<kod>-<ad>/`, `docs/adr/`,
`docs/_arsiv/`. Proje başka klasör adları kullanıyorsa (ör. `docs/01-sistem/`) mevcut adlar
korunur; kök CLAUDE.md'deki doküman haritası esas alınır.

**Durum işaretleri:** ✅ tamam · 🟨 devam ediyor · ⬜ başlanmadı · ⛔ engelli · ❓ doğrulanacak

**Okuma disiplini.** Bir modülde çalışırken yalnız o modülün CLAUDE.md ve PROGRESS.md
dosyalarını oku. Çok sayıda veya büyük dosyayı subagent ile oku, ana oturuma özet gelsin.
`docs/_arsiv/` yalnız istenirse okunur. Amaç context'i işin kendisine ayırmaktır.

**Alt ajanlar** PROGRESS dosyalarına yazmaz; aynı anda iki ajan aynı dosyayı düzenlerse
değişiklikler çakışır. Raporlarının sonunda en fazla 6 maddelik "PROGRESS notu" döndürürler,
ana oturum işler.

**Boyut sınırları:** kök CLAUDE.md 5 KB, kök PROGRESS.md 10 KB, modül PROGRESS.md 15 KB,
aktif doküman 25 KB. Sınırı aşan dosya, okunduğu her oturumda context'i boşa harcar.

**Skill'in devreye girmesi.** `kur`, `uyarla` ve `duzenle kok` modları kök CLAUDE.md'nin
"İlerleme takibi" bölümüne bu skill'in adını ve hangi işlerde kullanılacağını yazar. Proje
CLAUDE.md'si her oturumda okunduğu için skill, kullanıcı adını anmasa da doğru anda çağrılır.

**Onay.** Dosya taşıma, silme, birleştirme ve commit öncesi kullanıcı onayı al. Bilgi silme;
özetleyip arşive taşı (`git mv`). Belirsiz konuda tahmin etme, ❓ ile işaretle veya sor.

---

## Mod: kur

Plan modunda çalış. Uygulama kodu yazma; yalnız proje yönetimi iskeletini kur.

1. Eksik bilgileri kullanıcıdan al: proje adı, tek cümlede ürün, hedef kullanıcı, teknoloji,
   entegrasyonlar, 3-6 değişmez kural, çalışma dili, çekirdek kapsamı, ilk modül (kod-ad-tanım),
   planlanan modüller, bilinen bağımlılıklar.
2. `references/sablonlar.md`'yi oku. Kök CLAUDE.md, kök PROGRESS.md, `docs/00-urun/VIZYON.md`
   (1 sayfa), MODUL_KATALOGU.md, YOL_HARITASI.md; `docs/core/` ve ilk modül için CLAUDE.md,
   PROGRESS.md, KAPSAM.md oluştur. Planlanan diğer modüller yalnız katalogda satır olur.
3. `docs/adr/0001-moduler-yapi.md`: modüler yapı kararını ADR olarak yaz.
4. Belirsizlikleri kök PROGRESS.md "Açık sorular"a yaz.
5. Rapor: dosya ağacı, boyutlar, açık sorular, ilk modülde başlanacak 3-5 iş.
   Onaydan sonra commit: `docs: project management skeleton`.

## Mod: uyarla

Plan modunda çalış. Mevcut yapıyı bozmadan ilerleme katmanını ekle. Dokümanlar çok dağınıksa
(onlarca dosya, 25 KB üstü çok sayıda dosya, aynı konuda birden çok dosya) bunu söyle ve
`duzenle`'yi öner; uyarla dosya taşımaz, dağınıklığı çözmez.

1. Analiz (değiştirmeden): CLAUDE.md, README, `docs/`, varsa yol haritası ve prompt listeleri,
   `.claude/agents` ve `.claude/skills`; git log; klasörlere bakarak katmanların durumu.
   Kod, API modülleri ve ekranları çekirdek/modül olarak sınıflandır. Fiyat/paket bilgisi varsa listele.
2. Kullanıcıya iki tablo sun:
   - Eşleme: Kit dosyası | Mevcut karşılığı | İşlem (Yeni oluştur / Mevcudu kullan / Bölüm ekle)
   - Sınıflandırma: Bileşen | Çekirdek mi, hangi modül | Gerekçe
3. Onaydan sonra: eksik dosyaları şablonlarla oluştur, mevcut dosyalara bağlantı ver (içerik
   kopyalama); PROGRESS dosyalarını git geçmişi ve mevcut durumla doldur, emin olmadıklarını ❓
   yap; CLAUDE.md'ye yalnız "Modüler yapı" ve "İlerleme takibi" bölümlerini ekle, mevcut
   kurallara dokunma; salt okunur olmayan her ajanın teslim biçimine "PROGRESS notu" satırını ekle.
4. Değişen dosyaları listele; onayla commit: `docs: add modular progress tracking`.

## Mod: duzenle

Dağınık doküman yığınını aşama aşama modül yapısına taşır. Ayrıntılı akış:
`references/duzenle.md`. Alt komutlar: `envanter`, `iskelet`, `modul <ad>`, `kok`, `dogrula`.
Alt komut verilmemişse `DOCS_GECIS_PLANI.md`'nin "Aşama durumu"na bak ve sıradakini öner.

## Mod: modul-ekle

Kod yazma. Kök CLAUDE.md, MODUL_KATALOGU.md ve çekirdek CLAUDE.md'yi oku; diğer modülleri okuma.

1. Eksikse al: kod, ad, kısa tanım, bağımlılık.
2. Şablonlarla modül klasöründe CLAUDE.md, PROGRESS.md, KAPSAM.md oluştur. Olgunluk "Fikir".
   Kapsam dışını açıkça yaz.
3. Modülün çekirdekten ihtiyaç duyduğu ama çekirdekte olmayanları (yetki, parametre, olay,
   ortak bileşen) çekirdek PROGRESS.md "Yapılacaklar"a `[<KOD> için]` etiketiyle ekle.
4. Başka modülle veri alışverişi gerekiyorsa doğrudan çağrı tasarlama; çekirdek olay/arayüz öner.
5. MODUL_KATALOGU, YOL_HARITASI ve kök PROGRESS "Modüller" tablosunu güncelle.
6. Rapor: kapsam özeti, çekirdek ihtiyaçları, açık sorular.

## Mod: oturum-ac

Kök PROGRESS.md'nin yalnız "Odak" ve "Benden beklenen kararlar" bölümlerini, modülün
CLAUDE.md ve PROGRESS.md dosyalarını oku. Başka doküman okuma.
4-5 cümleyle söyle: modülün durumu, yarım kalan iş, sıradaki adım, bu oturum için önerilen iş.
Engel veya açık soru varsa önce onları sor. Kod yazmadan onay bekle.

## Mod: gorev

Görevi, modülü ve kabul kriterini netleştir. Plan modunda plan çıkar: dokunulacak dosyalar,
çekirdeğe veya başka modüle etkisi (varsa dur ve sor), testler, güncellenecek dokümanlar.
Onaydan sonra uygula; her ana adım bitince modül PROGRESS.md'yi hemen güncelle.

## Mod: kaydet

Modül PROGRESS.md'de tamamlananları işaretle, yarım işi dosya ve satır düzeyinde yaz, kararları
nedenleriyle ekle, "Sıradaki adım"ı güncelle, alt ajan notlarını işle. Sonra şunu öner:
`/compact <MODÜL> modülündeki aktif görev, değişen dosya yolları, son kararlar ve sıradaki adım korunsun`

## Mod: oturum-kapat

1. Modül PROGRESS.md: tamamlananları işaretle; kararları nedeniyle yaz; yarım işi ayrıntılı bırak;
   "Sıradaki adım"ı bir sonraki oturumun ilk işi olacak kadar somut ve kısa yaz (bir paragrafı
   geçiyorsa ayrıntıyı "Yarım kalanlar"a taşı); "Oturum günlüğü"ne ekle, son 5 kayıt kalsın.
2. Kök PROGRESS.md: "Modüller" tablosunda modülün satırını güncelle. "Odak" ve "Benden beklenen
   kararlar"dan kapanan maddeleri kaldır (karar modülün "Kararlar"ında veya ADR'de kayıtlı olsun).
3. Çekirdeğe veya başka modüle iş doğduysa o modülün "Yapılacaklar"ına `[<MODÜL>'den]` etiketiyle ekle.
4. "Kararlar" 15 maddeyi geçtiyse eski kararları `docs/_arsiv/<modül>-kararlar.md`'ye taşımayı,
   mimariyi etkileyenler için ADR yazmayı öner.
5. Testlerin durumunu söyle; Conventional Commits biçiminde commit mesajı öner.

## Mod: devam

Kök CLAUDE.md'yi, sonra modül PROGRESS.md'yi oku. Yalnız "Sıradaki adım" ve "Yarım kalanlar"ın
gerektirdiği dosyaları oku. Anladığını 3 cümleyle söyle, onayla devam et.

## Mod: moduller-arasi

İki tarafın CLAUDE.md ve PROGRESS.md dosyalarını oku. Ortak ihtiyacı çekirdekte arayüz, olay
veya servis olarak tasarla; her modül yalnız çekirdeği kullansın. Diğer modül kapalıyken de
modülün çalıştığını planda göster. Plan modunda başla; bitince iki tarafın PROGRESS.md'sini
güncelle; gerekiyorsa ADR yaz.

## Mod: toparla

Takvime bağlı değildir. Önce yalnız raporla, düzeltmeleri onayla uygula.

Oku: kök CLAUDE.md, kök PROGRESS.md, MODUL_KATALOGU.md, YOL_HARITASI.md. Modül ve çekirdek
PROGRESS dosyalarını subagent ile oku. Kök "Son toparlama" tarihinden bu yana git log'u incele
(tarih yoksa son 2 ay).

1. **Durum özeti:** son toparlamadan bu yana yapılanlar (modül bazında, commit'lerle); her
   modülün olgunluğu, sürümü, sıradaki adımı, yarım işleri; uzun süredir ⛔/🟨 bekleyenler;
   cevaplanmamış kararlar.
2. **Tutarlılık:** PROGRESS ile kod/commit uyuşuyor mu (✅ ama kodu yok, kodu var ama ⬜)?
   "Veri / entegrasyon notları" ve "Açık sorular" hâlâ geçerli mi, sonradan çözülmüş olanlar var mı?
   Kök PROGRESS, katalog ve yol haritası aynı şeyi söylüyor mu? ADR'ye dönüşmesi gereken kararlar?
3. **Mimari kurallar:** başka modülü doğrudan çağıran kod; modül açma/kapama kontrolünü atlayan
   uç, ekran veya iş; kodda/dokümanda fiyat-paket bilgisi.
4. **Dosya sağlığı:** boyut sınırını aşanlar; belirsiz veya bir paragrafı aşan "Sıradaki adım";
   kökte kalmış kapanmış maddeler; arşivlenebilecek planlar; tekrar eden veya çekirdeğe ait bilgi.
5. **Öneri:** numaralı düzeltme listesi (dosya, işlem, gerekçe) ve 3-5 maddelik modül bazlı odak.

Onaylananları uygula: arşivlemeyi `git mv` ile yap; "Tamamlananlar"ı tek satırlık özetlere indir;
kök "Odak" ve "Son toparlama"yı (bugünün tarihi + 2-3 cümle) güncelle; YOL_HARITASI
olgunluklarını güncelle. Commit: `docs: project review YYYY-MM-DD`.

## Mod: yayin-kontrol

Değiştirme; ✅/⛔/❓ ile raporla:
- Kapsam: KAPSAM.md zorunlu maddeleri tamam mı, kapsam dışı yazılı mı?
- Açma/kapama: modül kapalıyken menü, API ucu, zamanlanmış işler, bildirimler gerçekten kapalı mı;
  bunu doğrulayan test var mı?
- Bağımsızlık: bağımlı olmadığı modüller kapalıyken çalışıyor mu; başka modülü doğrudan çağıran kod?
- Katalog: module_key, bağımlılık, sürüm güncel mi?
- Kalite: testler yeşil mi, pilot doğrulaması yapıldı mı, açık kritik hata var mı?
- Doküman: kurulum/kullanım notu, sürüm notu, ilgili ADR'ler tamam mı?
- Destek: loglama ve hata mesajları sorunu bulmaya yetiyor mu?
Sonunda eksikler ve tahmini oturum sayısı; eksik yoksa olgunluğu "Yayında"ya çekmeyi öner.

## Mod: surum-kapat

1. Son sürümden bu yana git log'dan kullanıcıya dönük sürüm notu taslağı çıkar.
2. Modül PROGRESS.md: bu sürümün tamamlananlarını "Sürüm geçmişi"ne tek satırla taşı.
3. MODUL_KATALOGU.md ve kök PROGRESS "Modüller" tablosunda sürümü güncelle.
4. Etiket öner: `<modül-kodu>/v<SÜRÜM>`.
