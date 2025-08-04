# Device Installation, DMA Protection, BitLocker, Sleep State Restrictions

```powershell
# 🚫 Empêche l’installation de périphériques appartenant à certaines classes (ex: HID, USB malveillant)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DeviceInstall\Restrictions" /v DenyDeviceClasses /t REG_DWORD /d 1 /f

# ✅ Applique aussi la restriction aux périphériques déjà installés
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DeviceInstall\Restrictions" /v DenyDeviceClassesRetroactive /t REG_DWORD /d 1 /f

# 🔐 Interdit l’écriture sur les clés USB non protégées par BitLocker
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v DenyWriteAccessToRemovableDrivesNotProtectedByBitLocker /t REG_DWORD /d 1 /f

# 🛡️ Active l’usage de PIN complexes (alphanumériques) pour BitLocker au démarrage
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v UseEnhancedPin /t REG_DWORD /d 1 /f

# 🔒 Bloque l’accès DMA aux périphériques PCI/Thunderbolt quand le PC est verrouillé
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v DisableExternalDMAUnderLock /t REG_DWORD /d 1 /f

# ❌ Interdit les états de veille S1–S3 (évite attaque sur RAM via veille)
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Power" /v CsEnabled /t REG_DWORD /d 0 /f

# 🔥 Ultimate BitLocker Hardening – Fusion TPM + Password (Local / TPM) 🔐

# 🔄 RESET des stratégies BitLocker existantes
reg delete "HKLM\SOFTWARE\Policies\Microsoft\FVE" /f

# 🔐 Active l’usage d’un mot de passe comme protecteur de démarrage
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v UsePassword /t REG_DWORD /d 1 /f

# 🚫 Désactive l’obligation du TPM seul
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v RequireActiveDirectoryBackup /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v EnableBDEWithNoTPM /t REG_DWORD /d 1 /f

# 📛 Désactive les autres méthodes de démarrage sauf mot de passe
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v UseTPM /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v UseTPMKeyPINPassword /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v UseTPMKeyPIN /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v UseTPMKey /t REG_DWORD /d 0 /f

# 🧱 Demande une saisie utilisateur (mot de passe au boot)
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v RequireStartupInput /t REG_DWORD /d 2 /f

# 🔐 Force chiffrement XTS-AES 256 bits
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v EncryptionMethodWithXtsOs /t REG_DWORD /d 7 /f

# 🛡️ Protection DMA pendant lock
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v DisableExternalDMAUnderLock /t REG_DWORD /d 1 /f

# 💾 Pas de backup dans AD
reg add "HKLM\SOFTWARE\Policies\Microsoft\FVE" /v RequireActiveDirectoryBackup /t REG_DWORD /d 0 /f

# 🔄 Redémarre la stratégie pour prise en compte
gpupdate /force

```

```powershell

# 🚀 Lance le chiffrement avec protecteur PASSWORD
manage-bde -on C: -Password

```

```powershell
# Sauvegarde de secours (à mettre sur clé USB ou autre disque)
manage-bde -protectors -add C: -RecoveryKey D:\

```