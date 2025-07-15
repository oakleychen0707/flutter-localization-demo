# flutter-localization-demo

這是一個實作 Flutter 多語系功能的範例專案，支援英文、繁體中文、簡體中文，並根據使用者的系統語言自動顯示對應翻譯。
⚠️ 本專案使用 Flutter 3.16+（支援自動語系產生 `flutter gen-l10n`）

---

## 📌 功能說明

- ✅ 使用 `.arb` 語系檔案管理翻譯文字
- ✅ 自動產生 `AppLocalizations` 類別
- ✅ 根據使用者系統語言自動切換
- ✅ 支援繁體中文 (`zh_TW`) / 簡體中文 (`zh_CN`) / 英文 (`en`)
- ✅ 自訂 `localeResolutionCallback` 處理 fallback 語系

---

## 📁 專案結構

```
lib/
├── l10n/
│   ├── app_en.arb       # 英文
│   ├── app_zh.arb       # 基底中文 fallback（必要）
│   ├── app_zh_TW.arb    # 繁體中文
│   └── app_zh_CN.arb    # 簡體中文
└── main.dart            # 設定 MaterialApp 語系支援
```

---

## 🛠️ 安裝與設定步驟

### 1️⃣ 在 `pubspec.yaml` 加入依賴

```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter

flutter:
  generate: true
```

### 2️⃣ 建立語系檔案（.arb）
放置於 lib/l10n/ 資料夾中：

#### app_en.arb
```
{
  "@@locale": "en",
  "hello": "Hello",
  "welcome": "Welcome"
}
```

#### app_zh_TW.arb
```
{
  "@@locale": "zh_TW",
  "hello": "你好",
  "welcome": "歡迎使用"
}
```

#### app_zh_CN.arb
```
{
  "@@locale": "zh_CN",
  "hello": "你好",
  "welcome": "欢迎使用这个应用程序"
}
```

#### app_zh.arb
```
{
  "@@locale": "zh",
  "hello": "你好（預設中文）",
  "welcome": "歡迎使用這個應用程式"
}
```

### 3️⃣ 執行產生語系類別

```
flutter gen-l10n
```

這個指令會根據你在 `lib/l10n/` 裡的 `.arb` 檔案，自動產生相關檔案：

lib/gen_l10n/app_localizations.dart

lib/gen_l10n/app_localizations_en.dart

lib/gen_l10n/app_localizations_zh.dart

---

## 🧩 MaterialApp 語系設定範例
```
import 'package:flutter_gen/gen_l10n/app_localizations.dart';
import 'l10n/app_localizations.dart';

MaterialApp(
  supportedLocales: const [
    Locale('en'),
    Locale('zh', 'TW'),
    Locale('zh', 'CN'),
  ],
  localizationsDelegates: const [
    AppLocalizations.delegate,
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
    GlobalCupertinoLocalizations.delegate,
  ],
  localeResolutionCallback: (locale, supportedLocales) {
    if (locale == null) return supportedLocales.first;

    // 完整比對語言 + 國碼
    for (final supportedLocale in supportedLocales) {
      if (supportedLocale.languageCode == locale.languageCode &&
          supportedLocale.countryCode == locale.countryCode) {
        return supportedLocale;
      }
    }

    // 語言 fallback
    for (final supportedLocale in supportedLocales) {
      if (supportedLocale.languageCode == locale.languageCode) {
        return supportedLocale;
      }
    }

    return supportedLocales.first;
  },
  home: const MyHomePage(),
);
```
---

### ✅ 使用方式
```
Text(AppLocalizations.of(context)!.hello)
```

---

## 🔁 測試語系切換方式

1. 前往手機或模擬器的 **系統語言設定**
2. 切換為：
   - English（會顯示 `app_en.arb`）
   - 中文（台灣）（會顯示 `app_zh_TW.arb`）
   - 中文（中國）（會顯示 `app_zh_CN.arb`）
3. 重新啟動 App，即可看到語言切換效果

