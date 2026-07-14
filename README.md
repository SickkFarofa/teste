# Jogo da Velha (Android)

App Android em **tela única** com **AdMob**. Use este guia para **testar no celular** e gerar **APK/AAB** para a Play Store.

## Pré-requisitos

| Ferramenta | Para quê |
|------------|----------|
| [Node.js 20+](https://nodejs.org/) | Build web + Capacitor |
| [Android Studio](https://developer.android.com/studio) | SDK Android + Gradle |
| JDK (vem com Android Studio) | Assinar release |
| Celular Android | Testar APK debug |

Configure variáveis de ambiente (Android Studio → SDK Manager):

- `ANDROID_HOME` = pasta do SDK (ex.: `C:\Users\SEU\AppData\Local\Android\Sdk`)
- Adicione ao PATH: `%ANDROID_HOME%\platform-tools` (para `adb`)

---

## 1. Setup inicial (uma vez)

```powershell
cd C:\repos\jogo-da-velha
.\scripts\setup-android.ps1
```

Isso instala dependências, gera `www/`, cria o projeto `android/`, configura AdMob e assinatura.

---

## 2. Testar no celular (antes da Play Store)

### Opção A — APK debug (recomendado)

```powershell
.\scripts\build-debug-apk.ps1
```

Saída: **`dist\jogo-da-velha-debug.apk`**

**Instalar via USB:**

1. No celular: **Configurações → Opções do desenvolvedor → Depuração USB** (ativar)
2. Conecte o cabo USB e aceite a autorização no aparelho
3. Execute:

```powershell
.\scripts\install-debug.ps1
```

**Ou** copie o APK para o telefone (WhatsApp, e-mail, etc.) e abra o arquivo.

### Opção B — Android Studio

```powershell
npx cap open android
```

Clique em **Run** com o celular ou emulador conectado.

### O que validar nos testes

- [ ] Jogo funciona em tela cheia, sem rolagem
- [ ] Placar persiste ao fechar e reabrir
- [ ] Banner AdMob aparece na base (IDs de teste do Google)
- [ ] Interstitial após vitória/empate
- [ ] Botões "Nova partida" e "Zerar placar"

> Os IDs em `admob.config.js` são de **teste**. Mantenha `isTesting: true` até publicar.

---

## 3. Gerar APK e AAB para a Play Store

### Passo 1 — Criar keystore (uma vez)

```powershell
.\scripts\create-keystore.ps1
```

Guarde **`release.keystore`** e a **senha** em local seguro. Sem isso você não consegue atualizar o app na loja.

### Passo 2 — Configurar assinatura

Edite **`android\keystore.properties`** (criado automaticamente) com a senha que você definiu:

```properties
storeFile=../release.keystore
storePassword=SUA_SENHA
keyAlias=jogodavelha
keyPassword=SUA_SENHA
```

### Passo 3 — Trocar AdMob para produção

Em `admob.config.js`, use seus IDs reais do [AdMob](https://admob.google.com/) e `isTesting: false`.

Atualize também `android/app/src/main/res/values/strings.xml` → `admob_app_id`.

### Passo 4 — Gerar builds

**AAB (obrigatório para Play Store):**

```powershell
npm run aab:release
# ou: .\scripts\build-release.ps1 -SkipApk
```

Saída: **`dist\jogo-da-velha-release.aab`**

**APK + AAB release:**

```powershell
npm run apk:release
# ou: .\scripts\build-release.ps1
```

Saídas:

| Arquivo | Uso |
|---------|-----|
| `dist\jogo-da-velha-release.aab` | Upload na Play Console |
| `dist\jogo-da-velha-release.apk` | Instalação direta / testes finais |

---

## 4. Subir na Play Store

1. [Google Play Console](https://play.google.com/console) → Criar app
2. **Teste interno** (recomendado primeiro): faça upload do `.aab`, adicione testadores por e-mail
3. Valide o app em produção de teste
4. Depois promova para **Produção**

A Play Store exige **AAB** (não APK) para apps novos.

---

## Comandos rápidos

| Comando | Descrição |
|---------|-----------|
| `npm run dev` | Testar UI no navegador (sem AdMob) |
| `.\scripts\setup-android.ps1` | Setup inicial |
| `.\scripts\build-debug-apk.ps1` | APK para testar no celular |
| `.\scripts\install-debug.ps1` | Instalar APK debug via USB |
| `npm run aab:release` | AAB assinado para Play Store |
| `npm run apk:release` | AAB + APK assinados |
| `npx cap open android` | Abrir no Android Studio |

---

## Estrutura de saída

```
dist/
├── jogo-da-velha-debug.apk      ← testes no celular
├── jogo-da-velha-release.aab    ← Play Store
└── jogo-da-velha-release.apk    ← opcional
```

## Arquivos sensíveis (não commitar)

- `release.keystore`
- `android/keystore.properties`

Estão no `.gitignore`.
