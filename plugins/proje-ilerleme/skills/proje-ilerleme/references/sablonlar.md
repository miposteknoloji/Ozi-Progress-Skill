# Şablonlar

Bu dosya `proje-ilerleme` skill'inin şablonlarını içerir. `<...>` alanları projeye göre doldurulur.
Mevcut bir projede şablonun işini gören bir dosya varsa yenisi açılmaz, ona bağlantı verilir.

## İçindekiler
1. Klasör yapısı
2. Kök CLAUDE.md (yeni proje) ve eklenecek bölümler
3. Kök PROGRESS.md
4. Modül (ve core) PROGRESS.md
5. MODUL_KATALOGU.md
6. YOL_HARITASI.md
7. Modül CLAUDE.md
8. Modül KAPSAM.md
9. ADR

## 1. Klasör yapısı

```
<proje>/
├── CLAUDE.md                 ← kök kurallar ve doküman haritası (≤5 KB)
├── PROGRESS.md               ← odak, modüller, açık kararlar, son toparlama (≤10 KB)
└── docs/
    ├── 00-urun/  VIZYON.md · MODUL_KATALOGU.md · YOL_HARITASI.md
    ├── core/     CLAUDE.md · PROGRESS.md · KAPSAM.md · (TASARIM.md)
    ├── modules/<kod>-<ad>/  CLAUDE.md · PROGRESS.md · KAPSAM.md · (TASARIM.md)
    ├── adr/
    └── _arsiv/               ← yalnız istenirse okunur
```

Çekirdek birden fazla parçadan oluşuyorsa (ör. sistem servisleri + dış sistem entegrasyonu)
`docs/core/<parça>/` alt klasörleri açılır; her parçanın kendi CLAUDE.md ve PROGRESS.md'si olur.

## 2. Kök CLAUDE.md

Yeni projede tam dosya:

```markdown
# CLAUDE.md — <Proje>

## Ürün tek cümlede
<…> Ayrıntı: docs/00-urun/VIZYON.md

## Değişmez kurallar
1. <kural>
2. <kural>

[aşağıdaki "eklenecek bölümler"]

## Dil ve biçim
<ör. Dokümanlar Türkçe; kod, değişken ve commit İngilizce (Conventional Commits)>
```

Mevcut projede yalnız şu bölümler eklenir:


```markdown
## Modüler yapı
- Ürün çekirdek + modüllerden oluşur. Liste: docs/00-urun/MODUL_KATALOGU.md
- Modüller birbirini doğrudan çağırmaz; ortak ihtiyaç çekirdekte arayüz/olay olarak tanımlanır.
- Modül açma/kapama kontrolü çekirdektedir; modülde tekrar yazılmaz.
- Fiyat ve paket bilgisi projede (kod ve doküman) tutulmaz; kod yalnız module_key bilir.
- Yetki, parametre, loglama çekirdeğin ortak servisleriyle yapılır.

## Doküman haritası
| Çalışılan konu | Oku |
|---|---|
| Ürün, modüller, yol haritası | docs/00-urun/ |
| Çekirdek | docs/core/ |
| <Modül adı> | docs/modules/<kod>-<ad>/ |
| Kararlar | docs/adr/ |
Bir modülde çalışırken YALNIZ o modülün CLAUDE.md ve PROGRESS.md dosyasını oku.
docs/_arsiv/ yalnız istenirse okunur.

## İlerleme takibi
- Oturum açma/kapama, /compact öncesi kayıt, context sıfırlandıktan sonra devam, yeni modül ve
  toparlama işlerinde `proje-ilerleme` skill'ini kullan (ilgili modla).
- Oturum başında ilgili modülün PROGRESS.md'si okunur; durum özetlenir, onay beklenir.
- Her anlamlı adımda PROGRESS.md hemen güncellenir; oturum sonu beklenmez.
- Kararlar nedenleriyle yazılır; mimariyi etkileyen karar ADR olur.
- "Sıradaki adım" her zaman somut bırakılır.
- Başka modüle doğan iş o modülün PROGRESS.md'sine "[<kaynak>'den]" etiketiyle yazılır.
- Alt ajanlar PROGRESS dosyalarına yazmaz; "PROGRESS notu" döndürür.
- Boyut: kök CLAUDE.md 5 KB, kök PROGRESS.md 10 KB, modül PROGRESS.md 15 KB, aktif doküman 25 KB.
```

## 3. Kök PROGRESS.md

```markdown
# PROGRESS — <Proje>
Son güncelleme: GG.AA.YYYY — <kısa açıklama>

## Odak
- [ ] <iş> — <modül>

## Modüller
| Kod | Modül | Olgunluk | Sürüm | Sıradaki adım (kısa) |
|---|---|---|---|---|
| core | Çekirdek | | | |
| M01 | | | | |

## Benden beklenen kararlar
<!-- Yalnız açık maddeler. Kapanan karar modülün "Kararlar"ına veya ADR'ye yazılır ve buradan silinir. -->
- [ ] <karar> — <neden gerekli, neyi blokluyor>

## Açık sorular / engeller
- [ ] <soru> — <kimden, neyi bekliyor>

## Son toparlama
GG.AA.YYYY — <2-3 cümlelik durum özeti>
```

## 4. Modül (ve core) PROGRESS.md

```markdown
# PROGRESS — <KOD> <Modül adı>
Son güncelleme: GG.AA.YYYY — <kısa açıklama>
Olgunluk: <seviye> · Sürüm: <x.y>

## Sıradaki adım
<Bir sonraki oturumun ilk işi: dosya, komut, kabul kriteri>

## Yarım kalanlar
- [ ] <iş> — nerede kaldı (dosya/satır), neden durdu

## Yapılacaklar
- [ ] <madde>
- [ ] [<kaynak modül>'den] <başka modülden gelen iş>

## Tamamlananlar (kısa)
- [x] GG.AA — <tek satır> — <commit>

## Kararlar
- GG.AA — <karar> → Neden: <gerekçe> (ADR-<no> varsa)

## Veri / entegrasyon notları
- <tablo, alan, filtre, API ucu, dikkat edilecek nokta>

## Açık sorular / engeller
- [ ] <soru> ⛔ <neyi blokluyor>

## Sürüm geçmişi
- v<x.y> — GG.AA.YYYY — <tek satır>

## Oturum günlüğü (son 5)
- GG.AA — <ne yapıldı> — <commit>
```

## 5. MODUL_KATALOGU.md

```markdown
# Modül Kataloğu — <Proje>
Projedeki modüllerin teknik listesi. Fiyat ve paket bilgisi burada tutulmaz.

## Modüller
| Kod | module_key | Modül | Kısa tanım | Bağımlılık | Olgunluk | Sürüm |
|---|---|---|---|---|---|---|
| core | core | Çekirdek | Kimlik, tenant, modül açma/kapama, yetki, parametre, log | – | | |

## Bağımlılık kuralları
- <ör. M03, M02 açık değilse açılamaz>

## Modüller arası olaylar / arayüzler
| Olay/arayüz | Yayınlayan | Dinleyen | Tanım yeri |
|---|---|---|---|
```

## 6. YOL_HARITASI.md

```markdown
# Yol Haritası — <Proje>
Takvime değil sıraya dayanır; toparlamada güncellenir.

## Modül olgunlukları
| Sıra | Modül | Şu an | Sonraki hedef | Hedefe ulaşmak için gereken |
|---|---|---|---|---|

## Sıradaki modül adayları
- <modül fikri> — <neden, kim için>

## Bilinçli olarak ertelenenler
- <madde> — <neden>
```

Mevcut projede faz bazlı bir yol haritası varsa onu silme; bu dosya ondan bağlantı alır veya ona bölüm olarak eklenir.

## 7. Modül CLAUDE.md

```markdown
# <KOD> — <Modül adı>

## Amaç
<2-3 cümle>

## Sınırlar
- module_key: <…>; modül kapalıysa hiçbir ucu/ekranı/işi çalışmaz (çekirdek kontrol eder).
- Kullandığı çekirdek servisleri: <…>
- Bağımlı olduğu modüller: <yok / …> — yalnız çekirdek arayüz/olaylarıyla.
- Başka modülleri doğrudan çağırmaz.

## Modüle özel kurallar
- <…>

## Kod yerleri
- <ör. api/src/Modules/<Modül>/, app/lib/modules/<modül>/>

## Dokümanlar
| Dosya | Ne zaman okunur |
|---|---|
| PROGRESS.md | Her oturum başında |
| KAPSAM.md | Yeni özellik eklerken, kapsam sorusunda |
| TASARIM.md | Mimari/veri akışı gerektiren işte |
```

## 8. Modül KAPSAM.md

```markdown
# <KOD> — Kapsam

## Hedef kullanıcı ve problem

## Kapsamda (sürüm <x.y>)
| # | Özellik | Öncelik | Durum |
|---|---|---|---|

## Kapsam dışı (bilerek)

## Sonraki sürüm adayları

## Bekleyen öneriler
<Kapsam dışı istekler buraya yazılır ve kullanıcıya sorulur.>
```

Mevcut projede kapsam dosyası varsa (ör. MVP kapsam) yeni KAPSAM.md açma; modül CLAUDE.md'den ona bağlantı ver.


## 9. ADR

```markdown
# ADR-<NNNN>: <Başlık>
Tarih: GG.AA.YYYY · Durum: Önerildi / Kabul / Reddedildi / Yerini aldı: ADR-<no>

## Bağlam
<Hangi sorun, hangi kısıtlar>

## Karar
<Ne yapılacak>

## Sonuçlar
<Kazanılan, kaybedilen, etkilenen modüller>

## Değerlendirilen alternatifler
- <alternatif> — <neden seçilmedi>
```
