# UAC, SEHOP, WDigest, Printer , UNC,notifications  & Driver Policies

```powershell
# 🔒 Applique les restrictions UAC sur les comptes locaux lors de connexions réseau
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 0 /f

# 🛡️ Active SEHOP (Structured Exception Handling Overwrite Protection)
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\kernel" /v DisableExceptionChainValidation /t REG_DWORD /d 0 /f

# 🔐 Désactive WDigest (empêche stockage des creds en clair dans LSASS)
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest" /v UseLogonCredential /t REG_DWORD /d 0 /f

# 🖨️ Ne jamais permettre l’installation de pilotes d’imprimantes, même pour un admin
reg add "HKLM\Software\Policies\Microsoft\Windows NT\Printers\PointAndPrint" /v RestrictDriverInstallationToAdministrators /t REG_DWORD /d 1 /f

# ⛔ Refuse tous les serveurs de pilotes (aucune source approuvée)
reg add "HKLM\Software\Policies\Microsoft\Windows NT\Printers\PointAndPrint" /v TrustedServers /t REG_DWORD /d 0 /f

# ⚠️ Affiche les avertissements et bloque si le pilote n’est pas déjà approuvé
reg add "HKLM\Software\Policies\Microsoft\Windows NT\Printers\PointAndPrint" /v NoWarningNoElevationOnInstall /t REG_DWORD /d 0 /f
reg add "HKLM\Software\Policies\Microsoft\Windows NT\Printers\PointAndPrint" /v NoWarningNoElevationOnUpdate /t REG_DWORD /d 0 /f

# 🚫 Interdit toute installation de driver non signé
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DriverSearching" /v DontSearchWindowsUpdate /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DriverSearching" /v ExcludeWUDriversInQualityUpdate /t REG_DWORD /d 1 /f

# 🛡️ Active UAC globalement
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA /t REG_DWORD /d 1 /f

# 🔐 Pour les comptes administrateurs : demande le mot de passe (pas juste "Oui/Non")
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v ConsentPromptBehaviorAdmin /t REG_DWORD /d 1 /f

# 🔐 Pour les utilisateurs standards : demande aussi les identifiants admin
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v ConsentPromptBehaviorUser /t REG_DWORD /d 1 /f

# 🔒 Affiche l'UAC sur le "secure desktop" (fond d'écran noir, protégé contre keyloggers)
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v PromptOnSecureDesktop /t REG_DWORD /d 1 /f

# 🚫 Désactive la détection automatique des installateurs (pas d'élévation automatique)
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableInstallerDetection /t REG_DWORD /d 0 /f

# 🔐 Active la stratégie "Hardened UNC Paths" pour renforcer l’accès aux partages réseau
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\NetworkProvider\HardenedPaths" -Force | Out-Null

# 📁 Renforce tous les accès à \\*\NETLOGON avec authentification mutuelle et intégrité
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\NetworkProvider\HardenedPaths" `
  -Name "\\*\NETLOGON" -PropertyType String -Value "RequireMutualAuthentication=1,RequireIntegrity=1" -Force

# 📁 Renforce tous les accès à \\*\SYSVOL (scripts, GPOs, etc.) avec authentification mutuelle et intégrité
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\NetworkProvider\HardenedPaths" `
  -Name "\\*\SYSVOL" -PropertyType String -Value "RequireMutualAuthentication=1,RequireIntegrity=1" -Force

# 🔒 Désactive les connexions automatiques aux hotspots publics, réseaux partagés ou payants (WiFi douteux)
reg add "HKLM\SOFTWARE\Microsoft\WcmSvc\wifinetworkmanager\config" /v AutoConnectAllowedOEM /t REG_DWORD /d 0 /f

# 🔒 Applique la politique de chargement des pilotes au démarrage (SEHOP - sécurité noyau)
reg add "HKLM\SYSTEM\CurrentControlSet\Policies\EarlyLaunch" /v DriverLoadPolicy /t REG_DWORD /d 3 /f

# 🖨️ Empêche le téléchargement de pilotes d'impression via Windows Update (HTTP)
reg add "HKLM\Software\Policies\Microsoft\Windows\DriverSearching" /v DontSearchWindowsUpdate /t REG_DWORD /d 1 /f

# 🌐 Bloque les assistants Web (publication, commande en ligne, etc.)
reg add "HKLM\Software\Policies\Microsoft\Windows\Explorer" /v NoWebServices /t REG_DWORD /d 1 /f

# 🔐 Blocage des périphériques non compatibles Kernel DMA (protection contre accès mémoire physique)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Kernel DMA Protection" /v DeviceEnumerationPolicy /t REG_DWORD /d 1 /f

# 🧑‍💻 Masque les comptes locaux de l'écran de connexion
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System" /v DontEnumerateLocalUsers /t REG_DWORD /d 1 /f

# ❌ Interdit la connexion avec un code PIN (Windows Hello)
reg add "HKLM\Software\Policies\Microsoft\Windows\System" /v AllowDomainPINLogon /t REG_DWORD /d 0 /f

# 🔐 Requiert un mot de passe au réveil
reg add "HKLM\SOFTWARE\Policies\Microsoft\Power\PowerSettings\0e796bdb-100d-47d6-a2d5-f7d2daa51f51" /v ACSettingIndex /t REG_DWORD /d 1 /f  # secteur secteur
reg add "HKLM\SOFTWARE\Policies\Microsoft\Power\PowerSettings\0e796bdb-100d-47d6-a2d5-f7d2daa51f51" /v DCSettingIndex /t REG_DWORD /d 1 /f  # batterie

# ❌ Désactive l'assistance à distance sollicitée
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fAllowToGetHelp /t REG_DWORD /d 0 /f

# ⛔ Bloque les clients RPC non authentifiés
reg add "HKLM\Software\Policies\Microsoft\Windows NT\Rpc" /v RestrictRemoteClients /t REG_DWORD /d 1 /f

# 🎤 Interdit l'activation vocale depuis l'écran de verrouillage
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v LetAppsActivateWithVoiceAboveLock /t REG_DWORD /d 2 /f

# ❎ Rends optionnels les comptes Microsoft pour les apps
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v MSAOptional /t REG_DWORD /d 1 /f

# ⛔ Désactive l'autorun et autoplay totalement
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v NoDriveTypeAutoRun /t REG_DWORD /d 255 /f
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v NoAutoRun /t REG_DWORD /d 1 /f

# 👁 Active l'anti-spoofing facial (Windows Hello)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Biometrics\FacialFeatures" /v EnhancedAntiSpoofing /t REG_DWORD /d 1 /f

# ❌ Désactive les expériences consommateurs Microsoft
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v DisableConsumerAccountStateContent /t REG_DWORD /d 1 /f

# ❌ Cache les comptes admin dans les UAC
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\CredUI" /v EnumerateAdministrators /t REG_DWORD /d 0 /f

# 📂 Limite la taille des logs Application / Security / System
wevtutil sl Application /ms:32768
wevtutil sl Security /ms:196608
wevtutil sl System /ms:32768

# ❌ Interdit l'enregistrement de mot de passe dans RDP client
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v DisablePasswordSaving /t REG_DWORD /d 1 /f

# ⛔ Interdit redirection de lecteur dans RDP
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDisableCdm /t REG_DWORD /d 1 /f

# 🔐 RDP : toujours demander un mot de passe + RPC sécurisé + chiffrage fort
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fPromptForPassword /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEncryptRPCTraffic /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MinEncryptionLevel /t REG_DWORD /d 3 /f

# ⛔ Interdit les téléchargements d'"enclosures" RSS
reg add "HKLM\SOFTWARE\Policies\Microsoft\Internet Explorer\Feeds" /v DisableEnclosureDownload /t REG_DWORD /d 1 /f

# ❌ Interdit l'indexation des fichiers chiffrés
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v AllowIndexingEncryptedStoresOrItems /t REG_DWORD /d 0 /f

# 🛡️ Active SmartScreen avec blocage et avertissement
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v EnableSmartScreen /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v ShellSmartScreenLevel /t REG_SZ /d Block /f

# ❌ Désactive Windows Game Bar + Recording
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\GameDVR" /v AllowGameDVR /t REG_DWORD /d 0 /f

# 🔚 Bloque Ink Workspace
reg add "HKLM\SOFTWARE\Policies\Microsoft\WindowsInkWorkspace" /v AllowWindowsInkWorkspace /t REG_DWORD /d 0 /f

# ⛔ Désactive contrôle utilisateur sur les installs Windows Installer
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer" /v EnableUserControl /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer" /v AlwaysInstallElevated /t REG_DWORD /d 0 /f

# 🔐 Désactive l'auto-login post-reboot
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v DisableAutomaticRestartSignOn /t REG_DWORD /d 1 /f

# ✅ Active le PowerShell Script Block Logging (pour forensics)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" /v EnableScriptBlockLogging /t REG_DWORD /d 1 /f

# ❌ WinRM : aucune auth faible, aucun stockage RunAs
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WinRM\Client" /v AllowBasic /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WinRM\Client" /v AllowUnencryptedTraffic /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WinRM\Client" /v AllowDigest /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service" /v AllowBasic /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service" /v AllowUnencryptedTraffic /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service" /v DisableRunAs /t REG_DWORD /d 1 /f

# 🔕 Désactive les notifications toast sur l’écran de verrouillage (évite les fuites d’infos comme mails, calendrier, etc.)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v DisableLockScreenAppNotifications /t REG_DWORD /d 1 /f

# ☁️ Désactive les contenus cloud Microsoft (empêche l’affichage et la synchronisation de suggestions Microsoft comme les apps recommandées ou pub dans le menu démarrer)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v DisableWindowsConsumerFeatures /t REG_DWORD /d 1 /f

# 🚫 Empêche les suggestions de contenu tiers dans Windows Spotlight (plus de pub ou recommandations externes sur écran de verrouillage)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v DisableThirdPartySuggestions /t REG_DWORD /d 1 /f

```