# proje-ilerleme

Claude Code ile geliştirilen projelerde **context dolduğunda işin kopmaması** için dosya
tabanlı ilerleme takibi ve doküman düzeni sağlayan bir skill.

> **English summary:** A Claude Code skill (Turkish) for file-based progress tracking in modular
> projects. It sets up `PROGRESS.md` files per module, opens and closes sessions, resumes after a
> context reset, reorganizes a messy pile of Markdown docs into modules, and runs periodic reviews.
> Install with `/plugin marketplace add miposteknoloji/Ozi-Progress-Skill`.

## Neyi çözer

Uzun projelerde Claude Code'un context'i dolar, `/compact` veya `/clear` sonrası "nerede
kalmıştık" sorusu zorlaşır, dokümanlar zamanla onlarca dağınık dosyaya dönüşür. Bu skill:

- Her modül için bir `PROGRESS.md` tutar: sıradaki adım, yarım işler, kararlar, veri notları.
- Kök `PROGRESS.md` ile tüm projenin odağını ve açık kararlarını tek sayfada gösterir.
- Oturumu açarken yalnız ilgili modülün dosyalarını okur; context işin kendisine kalır.
- Dağınık doküman yığınını aşama aşama, `git mv` ile ve bilgi kaybetmeden modüllere taşır.
- Projeyi çekirdek + modüller olarak kurgular; sonradan modül eklemek kolaylaşır.

## Kurulum

### Plugin olarak (önerilen)

Claude Code içinde:

```
/plugin marketplace add miposteknoloji/Ozi-Progress-Skill
/plugin install proje-ilerleme@proje-ilerleme
```

Ardından Claude Code'u yeniden başlat. Skill `/proje-ilerleme:proje-ilerleme` olarak çağrılır;
ayrıca ilgili isteklerde ("oturumu kapat", "nerede kalmıştık") Claude kendisi devreye alır.

### Elle

`plugins/proje-ilerleme/skills/proje-ilerleme` klasörünü şuraya kopyala:

| Kapsam | Konum |
|---|---|
| Tüm projeler | `~/.claude/skills/proje-ilerleme/` (Windows: `%USERPROFILE%\.claude\skills\proje-ilerleme\`) |
| Tek proje | `<proje>/.claude/skills/proje-ilerleme/` |

Bu durumda skill `/proje-ilerleme` olarak çağrılır.

## Kullanım

Örnekler elle kurulum içindir; plugin kurulumunda `/proje-ilerleme` yerine
`/proje-ilerleme:proje-ilerleme` yaz.

| Durum | Komut |
|---|---|
| Yeni proje | `/proje-ilerleme kur` |
| Başlamış, düzenli proje | `/proje-ilerleme uyarla` |
| Dokümanları dağınık proje | `/proje-ilerleme duzenle` (envanter → iskelet → modul → kok → dogrula) |
| Yeni modül | `/proje-ilerleme modul-ekle M02 Depo` |
| Oturum başı | `/proje-ilerleme oturum-ac M01` |
| Yeni görev | `/proje-ilerleme gorev M01 <görev>` |
| `/compact` öncesi | `/proje-ilerleme kaydet` |
| Oturum sonu | `/proje-ilerleme oturum-kapat` |
| Context sıfırlandıysa | `/proje-ilerleme devam M01` |
| İki modülü etkileyen iş | `/proje-ilerleme moduller-arasi M01 core <görev>` |
| Uzun aradan dönüş / dağınıklık | `/proje-ilerleme toparla` |
| Yayın öncesi kontrol | `/proje-ilerleme yayin-kontrol M01` |
| Sürüm çıkarken | `/proje-ilerleme surum-kapat M01 1.0` |

`uyarla`, `kur`, `duzenle` ve `gorev` modlarını **plan modunda** başlatman önerilir.

## Oluşturduğu yapı

```
<proje>/
├── CLAUDE.md                 ← kök kurallar ve doküman haritası (≤5 KB)
├── PROGRESS.md               ← odak, modüller, açık kararlar, son toparlama
└── docs/
    ├── 00-urun/  VIZYON.md · MODUL_KATALOGU.md · YOL_HARITASI.md
    ├── core/     CLAUDE.md · PROGRESS.md · KAPSAM.md
    ├── modules/<kod>-<ad>/  CLAUDE.md · PROGRESS.md · KAPSAM.md
    ├── adr/
    └── _arsiv/
```

Mevcut projede farklı klasör adları kullanılıyorsa korunur.

## İlkeler

- Bilgi sohbette değil dosyada yaşar; her anlamlı adımda PROGRESS güncellenir.
- Bir modülde çalışırken yalnız o modülün dosyaları okunur.
- Modüller birbirini doğrudan çağırmaz; ortak ihtiyaç çekirdekte tanımlanır.
- Alt ajanlar PROGRESS dosyalarına yazmaz, "PROGRESS notu" döndürür.
- Taşıma, silme ve commit kullanıcı onayıyla; bilgi silinmez, arşive taşınır.
- Fiyat ve paket bilgisi projede tutulmaz.

## Depo yapısı

```
.claude-plugin/marketplace.json
plugins/proje-ilerleme/
├── .claude-plugin/plugin.json
└── skills/proje-ilerleme/
    ├── SKILL.md
    └── references/
        ├── sablonlar.md     ← PROGRESS, CLAUDE.md, katalog, kapsam, ADR şablonları
        └── duzenle.md       ← dağınık doküman taşıma akışı
```

## Lisans

MIT
