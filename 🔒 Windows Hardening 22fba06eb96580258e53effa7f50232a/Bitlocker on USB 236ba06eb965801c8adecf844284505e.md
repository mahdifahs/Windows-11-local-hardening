# Bitlocker on USB

```powershell
# ✅ Définit la lettre de la clé USB
$driveLetter = "E:"

# ✅ Demande à l'utilisateur de saisir un mot de passe sécurisé
$securePassword = Read-Host -AsSecureString "🔐 Entrez un mot de passe pour chiffrer la clé USB"

# ✅ Applique BitLocker avec un protecteur par mot de passe et chiffrement fort XTS-AES 256
Enable-BitLocker -MountPoint $driveLetter -PasswordProtector -Password $securePassword -EncryptionMethod XtsAes256 -UsedSpaceOnly

# ✅ Affiche le statut du volume pour vérifier que le chiffrement a bien démarré
Get-BitLockerVolume -MountPoint $driveLetter

```