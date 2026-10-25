# Infinity+ 8.3.4 evidence for brealorg/breal-morphe-patches#257

Android 15 / API 35 ARM64 AOSP emulator, original Infinity+ 8.3.4 (versionCode 232).
Billing wrapper set to state 4 before opening LoginActivity.

- `before-1.4.123-subscription.png`: Breal 1.4.123 Spoof client keeps state 4 and shows the Subscription dialog.
- `after-fix-login.png`: with the fix the state is normalized to 1 and Login is shown.

No account data is included.
