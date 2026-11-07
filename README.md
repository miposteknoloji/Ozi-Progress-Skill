# proje-ilerleme

**[English](#english)** · **[Türkçe](#türkçe)**

---

## English

A Claude Code skill for file-based progress tracking and document organization, so that
**work doesn't get lost when the context fills up**.

> The skill itself, its mode names and the files it generates are in **Turkish**. You can talk
> to Claude in any language; the commands below stay as they are.

### What it solves

In long projects Claude Code's context fills up, "where were we?" gets hard to answer after
`/compact` or `/clear`, and docs slowly turn into dozens of scattered files. This skill:

- Keeps a `PROGRESS.md` per module: next step, unfinished work, decisions, data notes.
- Shows the whole project's focus and open decisions on one page in the root `PROGRESS.md`.
- Reads only the relevant module's files when opening a session, leaving context for the work itself.
- Moves a messy pile of docs into modules step by step, with `git mv` and without losing information.
- Structures the project as core + modules, so adding modules later is easy.

### Installation

#### As a plugin (recommended)

Inside Claude Code:

```
/plugin marketplace add miposteknoloji/Ozi-Progress-Skill
/plugin install proje-ilerleme@ozi-progress-skill
```

Then restart Claude Code. The skill is invoked as `/proje-ilerleme:proje-ilerleme`; Claude also
picks it up on its own for related requests ("close the session", "where were we").

#### Manually

Copy the `plugins/proje-ilerleme/skills/proje-ilerleme` folder to:

| Scope | Location |
|---|---|
| All projects | `~/.claude/skills/proje-ilerleme/` (Windows: `%USERPROFILE%\.claude\skills\proje-ilerleme\`) |
| Single project | `<project>/.claude/skills/proje-ilerleme/` |

In this case the skill is invoked as `/proje-ilerleme`.

### Usage

With the plugin install each mode shows up as its own command in the slash menu (e.g.
`/proje-ilerleme:kaydet`). All modes can also be called through one command:
`/proje-ilerleme:proje-ilerleme <mode> [module]`. A manual install only has the single command:
`/proje-ilerleme <mode> [module]`.

| Situation | Plugin install | Manual install |
|---|---|---|
| New project | `/proje-ilerleme:kur` | `/proje-ilerleme kur` |
| Started, reasonably organized project | `/proje-ilerleme:uyarla` | `/proje-ilerleme uyarla` |
| Project with scattered docs | `/proje-ilerleme:duzenle` (envanter → iskelet → modul → kok → dogrula) | `/proje-ilerleme duzenle` |
| New module | `/proje-ilerleme:modul-ekle M02 Depo` | `/proje-ilerleme modul-ekle M02 Depo` |
| Start of session | `/proje-ilerleme:oturum-ac M01` | `/proje-ilerleme oturum-ac M01` |
| New task | `/proje-ilerleme:gorev M01 <task>` | `/proje-ilerleme gorev M01 <task>` |
| Before `/compact` | `/proje-ilerleme:kaydet` | `/proje-ilerleme kaydet` |
| End of session | `/proje-ilerleme:oturum-kapat` | `/proje-ilerleme oturum-kapat` |
| After a context reset | `/proje-ilerleme:devam M01` | `/proje-ilerleme devam M01` |
| Work touching two modules | `/proje-ilerleme:moduller-arasi M01 core <task>` | `/proje-ilerleme moduller-arasi M01 core <task>` |
| Back after a long break / feels messy | `/proje-ilerleme:toparla` | `/proje-ilerleme toparla` |
| Pre-release check | `/proje-ilerleme:yayin-kontrol M01` | `/proje-ilerleme yayin-kontrol M01` |
| Cutting a release | `/proje-ilerleme:surum-kapat M01 1.0` | `/proje-ilerleme surum-kapat M01 1.0` |

Starting the `uyarla`, `kur`, `duzenle` and `gorev` modes in **plan mode** is recommended.

### Structure it creates

```
<project>/
├── CLAUDE.md                 ← root rules and doc map (≤5 KB)
├── PROGRESS.md               ← focus, modules, open decisions, last review
└── docs/
    ├── 00-urun/  VIZYON.md · MODUL_KATALOGU.md · YOL_HARITASI.md   (product: vision, catalog, roadmap)
    ├── core/     CLAUDE.md · PROGRESS.md · KAPSAM.md               (KAPSAM = scope)
    ├── modules/<code>-<name>/  CLAUDE.md · PROGRESS.md · KAPSAM.md
    ├── adr/
    └── _arsiv/                                                     (archive)
```

If an existing project uses different folder names, they are kept.

### Principles

- Information lives in files, not in the chat; PROGRESS is updated after every meaningful step.
- While working on a module, only that module's files are read.
- Modules never call each other directly; shared needs are defined in the core.
- Subagents don't write to PROGRESS files; they return a "PROGRESS note".
- Moves, deletions and commits need user approval; information is never deleted, it is archived.
- Pricing and package information is not kept in the project.

The skill is designed with multi-module products (e.g. SaaS with modules that can be switched on
and off) in mind, but works for single-module projects too.

### Repository layout

```
.claude-plugin/marketplace.json
plugins/proje-ilerleme/
├── .claude-plugin/plugin.json
├── commands/            ← one shortcut command per mode (kur.md, kaydet.md, …)
└── skills/proje-ilerleme/
    ├── SKILL.md
    └── references/
        ├── sablonlar.md     ← templates: PROGRESS, CLAUDE.md, catalog, scope, ADR
        └── duzenle.md       ← flow for reorganizing scattered docs
```

### License

MIT

---

## Türkçe

Claude Code ile geliştirilen projelerde **context dolduğunda işin kopmaması** için dosya
tabanlı ilerleme takibi ve doküman düzeni sağlayan bir skill.

### Neyi çözer

Uzun projelerde Claude Code'un context'i dolar, `/compact` veya `/clear` sonrası "nerede
kalmıştık" sorusu zorlaşır, dokümanlar zamanla onlarca dağınık dosyaya dönüşür. Bu skill:

- Her modül için bir `PROGRESS.md` tutar: sıradaki adım, yarım işler, kararlar, veri notları.
- Kök `PROGRESS.md` ile tüm projenin odağını ve açık kararlarını tek sayfada gösterir.
- Oturumu açarken yalnız ilgili modülün dosyalarını okur; context işin kendisine kalır.
- Dağınık doküman yığınını aşama aşama, `git mv` ile ve bilgi kaybetmeden modüllere taşır.
- Projeyi çekirdek + modüller olarak kurgular; sonradan modül eklemek kolaylaşır.

### Kurulum

#### Plugin olarak (önerilen)

Claude Code içinde:

```
/plugin marketplace add miposteknoloji/Ozi-Progress-Skill
/plugin install proje-ilerleme@ozi-progress-skill
```

Ardından Claude Code'u yeniden başlat. Skill `/proje-ilerleme:proje-ilerleme` olarak çağrılır;
ayrıca ilgili isteklerde ("oturumu kapat", "nerede kalmıştık") Claude kendisi devreye alır.

#### Elle

`plugins/proje-ilerleme/skills/proje-ilerleme` klasörünü şuraya kopyala:

| Kapsam | Konum |
|---|---|
| Tüm projeler | `~/.claude/skills/proje-ilerleme/` (Windows: `%USERPROFILE%\.claude\skills\proje-ilerleme\`) |
| Tek proje | `<proje>/.claude/skills/proje-ilerleme/` |

Bu durumda skill `/proje-ilerleme` olarak çağrılır.

### Kullanım

Plugin kurulumunda her mod komut menüsünde ayrı bir komut olarak görünür (`/proje-ilerleme:kaydet`
gibi). Tüm modlar tek komuttan da çağrılabilir: `/proje-ilerleme:proje-ilerleme <mod> [modül]`.
Elle kurulumda yalnız tek komut vardır: `/proje-ilerleme <mod> [modül]`.

| Durum | Plugin kurulumu | Elle kurulum |
|---|---|---|
| Yeni proje | `/proje-ilerleme:kur` | `/proje-ilerleme kur` |
| Başlamış, düzenli proje | `/proje-ilerleme:uyarla` | `/proje-ilerleme uyarla` |
| Dokümanları dağınık proje | `/proje-ilerleme:duzenle` (envanter → iskelet → modul → kok → dogrula) | `/proje-ilerleme duzenle` |
| Yeni modül | `/proje-ilerleme:modul-ekle M02 Depo` | `/proje-ilerleme modul-ekle M02 Depo` |
| Oturum başı | `/proje-ilerleme:oturum-ac M01` | `/proje-ilerleme oturum-ac M01` |
| Yeni görev | `/proje-ilerleme:gorev M01 <görev>` | `/proje-ilerleme gorev M01 <görev>` |
| `/compact` öncesi | `/proje-ilerleme:kaydet` | `/proje-ilerleme kaydet` |
| Oturum sonu | `/proje-ilerleme:oturum-kapat` | `/proje-ilerleme oturum-kapat` |
| Context sıfırlandıysa | `/proje-ilerleme:devam M01` | `/proje-ilerleme devam M01` |
| İki modülü etkileyen iş | `/proje-ilerleme:moduller-arasi M01 core <görev>` | `/proje-ilerleme moduller-arasi M01 core <görev>` |
| Uzun aradan dönüş / dağınıklık | `/proje-ilerleme:toparla` | `/proje-ilerleme toparla` |
| Yayın öncesi kontrol | `/proje-ilerleme:yayin-kontrol M01` | `/proje-ilerleme yayin-kontrol M01` |
| Sürüm çıkarken | `/proje-ilerleme:surum-kapat M01 1.0` | `/proje-ilerleme surum-kapat M01 1.0` |

`uyarla`, `kur`, `duzenle` ve `gorev` modlarını **plan modunda** başlatman önerilir.

### Oluşturduğu yapı

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

### İlkeler

- Bilgi sohbette değil dosyada yaşar; her anlamlı adımda PROGRESS güncellenir.
- Bir modülde çalışırken yalnız o modülün dosyaları okunur.
- Modüller birbirini doğrudan çağırmaz; ortak ihtiyaç çekirdekte tanımlanır.
- Alt ajanlar PROGRESS dosyalarına yazmaz, "PROGRESS notu" döndürür.
- Taşıma, silme ve commit kullanıcı onayıyla; bilgi silinmez, arşive taşınır.
- Fiyat ve paket bilgisi projede tutulmaz.

Skill çok modüllü ürünler (ör. modülleri açılıp kapatılabilen SaaS) düşünülerek tasarlandı;
tek modüllü projelerde de aynı yapıyla çalışır.

### Depo yapısı

```
.claude-plugin/marketplace.json
plugins/proje-ilerleme/
├── .claude-plugin/plugin.json
├── commands/            ← her mod için kısa komut (kur.md, kaydet.md, …)
└── skills/proje-ilerleme/
    ├── SKILL.md
    └── references/
        ├── sablonlar.md     ← PROGRESS, CLAUDE.md, katalog, kapsam, ADR şablonları
        └── duzenle.md       ← dağınık doküman taşıma akışı
```

### Lisans

MIT
