# lock screen

```powershell
# 🔒 Empêche l'utilisation de la caméra sur l'écran de verrouillage
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Personalization" /v NoLockScreenCamera /t REG_DWORD /d 1 /f

#  Bloque l'affichage de diaporamas en fond d'écran de verrouillage
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Personalization" /v NoLockScreenSlideshow /t REG_DWORD /d 1 /f

# 🔒 Désactive complètement l’écran de verrouillage (bypass = direct écran de login)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Personalization" /v NoLockScreen /t REG_DWORD /d 1 /f

# 🚫 Désactive la possibilité de modifier l’image de fond de l’écran de verrouillage
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Personalization" /v NoChangingLockScreen /t REG_DWORD /d 1 /f

# ❌ Supprime l’image d’arrière-plan de l’écran de connexion (login screen)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v DisableLogonBackgroundImage /t REG_DWORD /d 1 /f

# ⛔ Désactive les notifications sur l’écran de verrouillage (ex: mails, calendrier, etc.)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v DisableLockScreenAppNotifications /t REG_DWORD /d 1 /f

# 🛑 Supprime les applications (calendrier, météo…) de l’écran de verrouillage
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Notifications\Settings\Windows.SystemToast.SecurityAndMaintenance" /v Enabled /t REG_DWORD /d 0 /f

# ⚠️ Empêche l’accès rapide à Cortana depuis l’écran de verrouillage
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v AllowCortanaAboveLock /t REG_DWORD /d 0 /f

# 🔐 Verrouille la session automatiquement après inactivité
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v InactivityTimeoutSecs /t REG_DWORD /d 300 /f

# 🛑 Empêche les comptes d'afficher des infos (nom, mail) sur l'écran de login
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v BlockUserFromShowingAccountDetailsOnSignin /t REG_DWORD /d 1 /f
```