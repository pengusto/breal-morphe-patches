# Infinity+ 8.3.4 custom OAuth regression (#257)

Original: official Infinity+ 8.3.4 (`ml.docilealligator.infinityforreddit.plus`, versionCode 232), upstream release https://github.com/Docile-Alligator/Infinity-For-Reddit/releases/tag/v8.3.4 , APK SHA-256 `826d6f20476c78c52ce7a53b47d83c67e6f850be13e6eb89074fe2592f933ac6`.

## Failure and change

1. Bundle 1.4.123 suppresses Infinity's subscription launcher but leaves its wrapper state at NO_SUBSCRIPTION (4). LoginActivity, BaseActivity and account selection still check this state. The new 8.3.4-only helper locates the wrapper through LoginActivity's field reads and keeps its initial state and subsequent LiveData/byte-field writes synchronized at local ready state 1. It does not create a purchase or server entitlement.
2. Custom redirect replacement previously searched only classes under Lml/docilealligator. R8 moved token exchange to root-package helpers, leaving two infinity://localhost literals. Search the redirect literal across app code and exclude the extension fallback getter; preserve existing configured redirect resolution.
3. The browser callback manifest and AuthTab scheme also retained Infinity's original callback. Align them with the configured URI on the verified Plus 8.3.4 target. Java URI.host is null for authorities containing underscores; accept the existing raw authority only after a bounded hostname-character check. Scheme/authority/path/port stay consistent.

The custom-client options and existing extension attribution remain unchanged. This change does not publish an OAuth client, embed account credentials, auto-accept consent or solve Reddit server failures. Do not distribute the personal test APK or its signing key as part of this contribution.

## Reproduce and verify

Decode original and candidate with apktool `d -r --no-debug-info`. Run:

```
python3 tools/check-infinity-billing-state.py ORIGINAL_DECODED CANDIDATE_DECODED
python3 tools/check-infinity-oauth-redirect.py CANDIDATE_APK CANDIDATE_DECODED AAPT CONFIGURED_REDIRECT
```

The billing checker symbolically reproduces original state 4/8 and checks fixed construction/callback inputs 0–8 keep field and observer at 1. The redirect checker validates the binary manifest, AuthTab scheme and absence of the old URI throughout app smali, including root-package token exchange. The prior callback-only candidate fails this last assertion.

The full `:patches:buildAndroid` build, Desktop patch application and decoded regression checks pass with synthetic client settings; the unchanged 1.4.123 bundle fails both checks. An earlier local candidate containing these same substantive fixes plus the existing Modify login WebView patch completed live OAuth, account import, authenticated Home feed and persistence after restart on isolated Android 15/API 35 ARM64. Personal logs/screenshots are intentionally excluded. The newly scoped preparation is separately checked at build/bytecode level; it is not a new on-device Manager acceptance run.

Manager 2.x execution, physical-device installation, other Infinity variants/versions, same-key settings-update acceptance and long-duration refresh-token renewal remain unverified. Keep #257 open through normal release/installation-path validation.

A live attempt displayed Internal Server Error on Reddit's consent navigation. Back and one retry of Accept completed with the unchanged APK. Exact HTTP status/server cause was not captured; do not describe this patch as fixing Reddit outages.

## Signing and recovery

Users should keep their existing Morphe signing key. Same package and same key allow updates; an APK signed by another party is not a drop-in replacement. Export app settings and preserve local data before migration; do not suggest uninstalling to work around signature conflicts without a verified backup.
