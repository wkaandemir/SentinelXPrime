# SentinelXPrime

> Codex ve Claude Code icin asama farkindaligi olan guvenlik skill paketi.

[![Destek Yuzeyi](https://img.shields.io/badge/Support-Codex%20%7C%20Claude%20Code-111111?style=flat)](#kurulum)
[![Dogrulama](https://img.shields.io/badge/Validation-static%20%2B%20evals-2ea043?style=flat)](#dogrulama-ve-release)
[![Lisans: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat)](LICENSE)
[![Stackler](https://img.shields.io/badge/Stacks-ASP.NET%20Core%20%7C%20Spring%20%7C%20Node%20%7C%20Python%20%7C%20Go%20%7C%20Rails%20%7C%20Laravel%20%7C%20Rust-1f6feb?style=flat)](#desteklenen-stackler)

SentinelXPrime, ekiplerin planlama sirasinda eksik guvenlik gereksinimlerini yakalamasina, riskli implementasyon islerinde kapsamli ama dusuk gurultulu uyarilar almasina, kodlama bittikten sonra istege bagli review destegi istemesine ve release oncesinde pratik bir guvenlik kontrol plani cikarmasina yardim eder.

Bu paket varsayilan olarak danismanlik odaklidir. Sinyali ve tutarliligi artirir; bir repository'nin guvenli, tamamen incelenmis veya uretime hazir oldugunu belgelemez.

## Nasil Calisir

1. Planlama asamasi: `sentinelx-plan-gap`
2. Riskli implementasyon asamasi: `sentinelx-prime`
3. Implementasyon sonrasi review asamasi: `sentinelx-review-gate`
4. Release oncesi hardening asamasi: `sentinelx-test-rig`

Bir oturumda pakete hizli yonelim gerekiyorsa hafif bootstrap skill'i olarak `using-sentinelx` kullanilir.

## Desteklenen Stackler

- `.NET / ASP.NET Core`
- `Java / Spring`
- `Node / TypeScript`
- `Python`
- `Go`
- `Ruby on Rails`
- `PHP / Laravel`
- `Rust`

Stack net degilse SentinelXPrime ortak web guvenligi rehberligine duser ve stack cikariminin belirsiz oldugunu soyler.

Kriptoya duyarlı konularda [`skills/shared/crypto-guidance.md`](skills/shared/crypto-guidance.md) kontrol edilmelidir.

## Asama Karar Yardimi

- Kod tamamlandiysa ve siradaki soru "bu implementasyon yeterince guvenli mi?" ise `sentinelx-review-gate` kullan.
- Siradaki adim release veya handoff hardening ise `sentinelx-test-rig` kullan.
- Asama kaniti zayif ya da celiskiliyse `uncertain` modunda kal ve asama netlesene kadar rehberligi danismanlik seviyesinde tut.

## Kurulum

| Platform | Durum | Giris Noktasi |
| --- | --- | --- |
| Codex | Destekleniyor | [`.codex/INSTALL.md`](.codex/INSTALL.md) ve [`docs/README.codex.md`](docs/README.codex.md) |
| Claude Code | Destekleniyor | [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) ve [`docs/README.claude.md`](docs/README.claude.md) |

Destekleniyor ifadesi, repository icinde belgelenmis ve var olan bir kurulum yuzeyi oldugu anlamina gelir.

Codex ve Claude Code icin guncel release veya handoff iddialari [`docs/validation/release-readiness.md`](docs/validation/release-readiness.md) icindeki kayitli smoke kanitlariyla desteklenmelidir.
Harici release-ready veya handoff iddiasi yapmadan once `node scripts/check-release-readiness.mjs` ya da `Release Claim Readiness` workflow'u calistirilmalidir.

## Repository Durumu

- Paketleme, kurulum dokumanlari ve validator kapsami private collaborator review icin iyi durumdadir.
- `bash scripts/static-validation.sh`, Node.js 22 + Ruby ortamina sahip sistemlerde gecmelidir.
- `node scripts/check-release-readiness.mjs`, Claude Code icin henuz taze ve authenticated runtime `pass` satiri olmadigi icin basarisiz kalir.
- Bu kanit eklenene kadar repository'yi private review icin dogrulanmis olarak tanimla; release-ready veya handoff-ready olarak tanimlama.

Bu snapshot'taki son guncellemeler:

- Desteklenen platform yuzeyi Codex ve Claude Code ile sinirlandi.
- Codex/Claude disi kurulum rehberleri repository-facing dokumanlardan kaldirildi.
- Release-readiness kapisi ve dokuman envanteri kontrolleri iki platformlu destek yuzeyine gore guncellendi.

## Kurulumu Dogrulama

Yeni bir oturum baslat ve su promptlardan birini dene:

- `Use $sentinelx-prime while we plan this new ASP.NET Core feature.`
- `Use $sentinelx-plan-gap to review this Node/TypeScript API design for missing security requirements.`
- `Use $sentinelx-review-gate to run a focused security review on the completed auth changes.`
- `Use $sentinelx-test-rig to propose a stack-aware security check plan for this release handoff.`

Daha fazla ornek [`docs/examples/example-prompts.md`](docs/examples/example-prompts.md) icinde bulunur.

## Icerik

| Skill | Amac |
| --- | --- |
| `using-sentinelx` | Hafif bootstrap ve yonelim skill'i |
| `sentinelx-prime` | Asama farkindaligi olan guvenlik rehberligi orkestratoru |
| `sentinelx-plan-gap` | Planlama asamasi guvenlik boslugu analizi |
| `sentinelx-review-gate` | Istege bagli implementasyon sonrasi guvenlik review'u |
| `sentinelx-test-rig` | Release oncesi istege bagli guvenlik test/kontrol plani |
| `shared/*` | Ortak tehdit referanslari, finding semasi ve stack profilleri |

## Felsefe / Guvenlik Modeli

- varsayilan olarak danismanlik odakli
- yanlis guvence yok
- sessiz kurulum yok
- gizli mutasyon yok
- read-only aktif analiz yalnizca acik kullanici onayindan sonra
- kapsamli raporlar incelenen alanlari, incelenmeyen alanlari, varsayimlari ve calistirilan araclari ayirir

## Guncelleme

- Codex: kurulum dokumaninda kullandigin clone'u guncelle, sonra Codex'i yeniden baslat
- Claude Code: plugin clone'unu guncelle ve oturumu yeniden baslat
- Onemli repository degisiklikleri icin [CHANGELOG.md](CHANGELOG.md) dosyasina bak

## Sorun Giderme

- Skill'ler gorunmuyorsa platforma ozel kurulum dokumaninin aynen takip edildigini kontrol et ve yeni bir oturum baslat.
- Claude Code hook context eksikse plugin root icinde [`hooks/hooks.json`](hooks/hooks.json) ve [`hooks/session-start`](hooks/session-start) dosyalarinin varligini dogrula.
- Release paketleme sorunu varsa SentinelXPrime repo kokunden build al; wrapper workspace, nested source tree veya manuel zip kullanma.

## Dogrulama Ve Release

Dogrulama gereksinimleri:

- Node.js 22
- `scripts/static-validation.sh` icin Ruby
- Live eval calismalari icin `PATH` uzerinde `codex` CLI
- Live eval calismalari icin `$CODEX_HOME/auth.json` veya `~/.codex/auth.json` konumunda okunabilir Codex auth
- Release paketleme ve arsiv dogrulamasi icin `unzip` ve `zip` ya da `ditto`

Onerilen yerel dogrulama:

```bash
bash scripts/static-validation.sh
node evals/run-sentinelx-prime.mjs --manifest-json
node evals/run-sentinelx-prime.mjs --preflight-only
node evals/run-sentinelx-prime.mjs
node evals/run-sentinelx-prime.mjs --promote-artifacts
bash tests/hooks/test-session-start.sh
node scripts/check-doc-links.mjs
node scripts/check-legacy-names.mjs
```

Public release akisi:

```bash
bash scripts/package-release.sh
SENTINELX_PRIME_FORCE_NO_RSYNC=1 bash scripts/package-release.sh SentinelXPrime-fallback
node scripts/verify-release-archive.mjs dist/SentinelXPrime.zip
node scripts/verify-release-archive.mjs dist/SentinelXPrime-fallback.zip
node scripts/check-release-readiness.mjs
```

Release arsivlerini yalnizca temiz bir SentinelXPrime repo kokunden olustur. Wrapper workspace, nested source tree veya Finder/manual zip ile paketleme yapma.

`scripts/package-release.sh` canonical release arsiv akisidir. Finder/manual zip ciktilari desteklenen release artifact'i degildir.

## Lisans

MIT. Ayrinti icin [LICENSE](LICENSE) dosyasina bak.
