# SentinelXPrime Kurulum Promptu

Asagidaki promptu bir LLM modeline vererek SentinelXPrime kurulumunu yaptirabilirsin. Prompt varsayilan olarak sadece Codex ve Claude Code icin kurulum yapar. Claude Code icin kullanilan modele/yuzeye gore otomatik olarak dogru rotaya gecer (plugin yoksa plain skill auto-discovery fallback).

```text
Sen bir kurulum asistani olarak calisiyorsun. Amacin SentinelXPrime deposunu hedef projenin icine yalnizca Codex ve Claude Code icin dogru sekilde kurmak, dogrulamak ve sonucu kisa bir raporla bildirmek.

Kapsam:
- Yalnizca Codex ve Claude Code kurulumu yap.
- Codex ve Claude Code disinda baska bir editor/agent yuzeyi icin kurulum yapma.
- Kurulumda SentinelXPrime deposunu kullan: https://github.com/alicankiraz1/SentinelXPrime.git
- Kurulum global Codex/Claude dizinlerine yapilmayacak. `~/.codex`, `~/.claude`, `$env:USERPROFILE\.codex` veya `$env:USERPROFILE\.claude` altina clone'lama.
- Kurulum hedef projenin icine yapilacak. Hedef proje koku bilinmiyorsa once kullanicidan proje kokunu iste veya mevcut calisma dizinini proje koku olarak onaylat.
- Varsa hedef proje icindeki mevcut clone'u kullan veya guncelle; yoksa asagidaki proje-ici hedefe clone'la.
- Secret, token, auth.json veya benzeri kimlik bilgilerini okuma, yazma ya da rapora koyma.
- Yikici komut calistirma. Var olan bir dosya/dizin kurulum hedefiyle cakisirsa silmeden once kullanicidan onay iste.

Varsayilan hedef:
- SentinelXPrime clone hedefi: <PROJECT_ROOT>/.vendor/sentinelxprime
- Codex kurulumu: proje scope'u, .agents/skills/ uzerinden
- Claude Code kurulumu: kullanilan modele/yuzeye gore Plugin Route (A) veya Plain Skill Auto-Discovery (B). Route A mumkun degilse otomatik olarak Route B'ye fallback yap.
- Isletim sistemi bilinmiyorsa once tespit et.

Kurulacak skill dizinleri:
- sentinelx-prime
- sentinelx-plan-gap
- sentinelx-review-gate
- sentinelx-test-rig
- using-sentinelx
- shared

Codex kurulumu:
1. Depo yoksa hedef projenin icine clone'la:
   - Tum platformlar icin hedef: <PROJECT_ROOT>/.vendor/sentinelxprime
2. Depo varsa ve git deposuysa `git pull` ile guncellemeyi dene.
3. Skill'leri proje-ici Codex discovery dizinine bagla:
   - Tum platformlar icin hedef: <PROJECT_ROOT>/.agents/skills
4. macOS/Linux icin symlink kullan:
   ```bash
   project_root="$(pwd)"
   sentinelx_root="$project_root/.vendor/sentinelxprime"
   mkdir -p "$project_root/.agents/skills"
   if [ ! -d "$sentinelx_root/.git" ]; then
     git clone https://github.com/alicankiraz1/SentinelXPrime.git "$sentinelx_root"
   else
     git -C "$sentinelx_root" pull
   fi
   for name in sentinelx-prime sentinelx-plan-gap sentinelx-review-gate sentinelx-test-rig using-sentinelx shared; do
     ln -sfn "$sentinelx_root/skills/$name" "$project_root/.agents/skills/$name"
   done
   ```
5. Windows PowerShell icin junction kullan. Hedefte ayni isimde gercek dizin varsa silme; once kullanicidan onay iste. Hedef yoksa veya junction ise kur:
   ```powershell
   $projectRoot = (Get-Location).Path
   $sentinelxRoot = Join-Path $projectRoot ".vendor\sentinelxprime"
   if (-not (Test-Path (Join-Path $sentinelxRoot ".git"))) {
     git clone https://github.com/alicankiraz1/SentinelXPrime.git $sentinelxRoot
   } else {
     git -C $sentinelxRoot pull
   }
   New-Item -ItemType Directory -Force -Path (Join-Path $projectRoot ".agents\skills") | Out-Null
   $skills = @("sentinelx-prime", "sentinelx-plan-gap", "sentinelx-review-gate", "sentinelx-test-rig", "using-sentinelx", "shared")
   foreach ($name in $skills) {
     $link = Join-Path $projectRoot ".agents\skills\$name"
     $target = Join-Path $sentinelxRoot "skills\$name"
     if (Test-Path $link) {
       $item = Get-Item $link -Force
       if (-not ($item.Attributes -band [IO.FileAttributes]::ReparsePoint)) {
         Write-Host "Cakisiyor, elle kontrol gerekiyor: $link"
         continue
       }
       Remove-Item $link -Force
     }
     cmd /c mklink /J "$link" "$target" | Out-Null
   }
   ```
6. Dogrula:
   - <PROJECT_ROOT>/.agents/skills altinda `sentinelx-prime` ve `shared` gorunmeli.
   - Kullaniciya Codex'i hedef proje kokunden yeniden baslatmasini soyle.
   - Test promptu: `Use $sentinelx-prime while we plan this API auth change.`

Claude Code kurulumu (model/yuzey duyarli):

Once kullanilan Claude Code yuzeyini tespit et:
- Eger `claude --version` calisiyor ve cikti modern bir Claude Code surumu gosteriyorsa VE shell interaktif Claude Code CLI/desktop/IDE icindeyse: Route A (Plugin) mumkun.
- Eger ortam headless/SDK ise, `claude` CLI mevcut degilse, veya `/plugin` komutu hata veriyorsa: Route B (Plain Skill Auto-Discovery) kullan.
- Tespit belirsizse kullaniciya tek sorulu sor: "Claude Code'da `/plugin` komutu calisiyor mu? (e/h)". Cevap "e" ise Route A, "h" veya bilinmiyorsa Route B.

Route A — Plugin Install (interaktif Claude Code surumlerinde):
1. Claude Code icin ayni proje-ici clone'u kullan:
   - <PROJECT_ROOT>/.vendor/sentinelxprime
2. Depo yoksa clone'la; varsa ve git deposuysa `git pull` ile guncellemeyi dene.
3. Asagidaki dosyalarin varligini dogrula:
   - .claude-plugin/plugin.json
   - .claude-plugin/marketplace.json
   - hooks/hooks.json
   - hooks/session-start
4. Claude Code plugin workflow'u ortamda otomasyonla destekleniyorsa plugin root olarak clone dizinini yukle. Desteklenmiyorsa kullaniciya su net talimati ver:
   - Claude Code'da plugin root olarak <PROJECT_ROOT>/.vendor/sentinelxprime dizinini sec/yukle (`/plugin` menusu).
   - Hedef proje kokunde yeni bir Claude Code session baslat.
5. Dogrula:
   - Plugin root .claude-plugin/plugin.json dosyasini icermeli.
   - SessionStart hook'u SentinelXPrime bootstrap context enjekte etmeli.
   - Test promptu: `Use $sentinelx-prime while we plan this admin auth change.`

Route B — Plain Skill Auto-Discovery (headless/SDK veya `/plugin` yoksa):
1. Claude Code icin ayni proje-ici clone'u kullan:
   - <PROJECT_ROOT>/.vendor/sentinelxprime
2. Depo yoksa clone'la; varsa ve git deposuysa `git pull` ile guncellemeyi dene.
3. Skill'leri proje-ici Claude Code discovery dizinine bagla:
   - Tum platformlar icin hedef: <PROJECT_ROOT>/.claude/skills
4. macOS/Linux icin symlink kullan:
   ```bash
   project_root="$(pwd)"
   sentinelx_root="$project_root/.vendor/sentinelxprime"
   mkdir -p "$project_root/.claude/skills"
   if [ ! -d "$sentinelx_root/.git" ]; then
     git clone https://github.com/alicankiraz1/SentinelXPrime.git "$sentinelx_root"
   else
     git -C "$sentinelx_root" pull
   fi
   for name in sentinelx-prime sentinelx-plan-gap sentinelx-review-gate sentinelx-test-rig using-sentinelx shared; do
     ln -sfn "$sentinelx_root/skills/$name" "$project_root/.claude/skills/$name"
   done
   ```
5. Windows PowerShell icin junction kullan:
   ```powershell
   $projectRoot = (Get-Location).Path
   $sentinelxRoot = Join-Path $projectRoot ".vendor\sentinelxprime"
   if (-not (Test-Path (Join-Path $sentinelxRoot ".git"))) {
     git clone https://github.com/alicankiraz1/SentinelXPrime.git $sentinelxRoot
   } else {
     git -C $sentinelxRoot pull
   }
   New-Item -ItemType Directory -Force -Path (Join-Path $projectRoot ".claude\skills") | Out-Null
   $skills = @("sentinelx-prime", "sentinelx-plan-gap", "sentinelx-review-gate", "sentinelx-test-rig", "using-sentinelx", "shared")
   foreach ($name in $skills) {
     $link = Join-Path $projectRoot ".claude\skills\$name"
     $target = Join-Path $sentinelxRoot "skills\$name"
     if (Test-Path $link) {
       $item = Get-Item $link -Force
       if (-not ($item.Attributes -band [IO.FileAttributes]::ReparsePoint)) {
         Write-Host "Cakisiyor, elle kontrol gerekiyor: $link"
         continue
       }
       Remove-Item $link -Force
     }
     cmd /c mklink /J "$link" "$target" | Out-Null
   }
   ```
6. Dogrula:
   - <PROJECT_ROOT>/.claude/skills altinda `sentinelx-prime` ve `shared` gorunmeli.
   - Kullaniciya Claude Code'u hedef proje kokunden yeniden baslatmasini soyle.
   - Test promptu: `Use $sentinelx-prime while we plan this admin auth change.`
   - Not: Route B'de SessionStart bootstrap hook calismaz.

Son rapor formatin:
- Kurulan yuzeyler: Codex, Claude Code (Route A veya Route B belirt)
- Hedef proje koku
- Kullanilan proje-ici clone yolu
- Olusturulan symlink/junction (Codex: .agents/skills, Claude Code Route B: .claude/skills) veya plugin yukleme durumu (Route A)
- Dogrulama sonucu
- Atlanan veya kullanici onayi gerektiren adimlar
- Kisa not: Bu kurulum SentinelXPrime'i kullanima hazirlar; repository'nin guvenli, tamamen incelenmis veya production-safe oldugunu iddia etmez.
```
