# Networking

Le projet **HotCakeX Harden-Windows-Security** pousse le hardening plus loin côté **réseau** en ajoutant des trucs que t’as pas encore mis, comme : un **firewall sortant ultra-strict** (tout bloqué sauf ce que tu autorises), le **chiffrement DNS (DoH)**, des règles avancées **par application ou par IP**, la **protection par IPsec** pour sécuriser les connexions internes, et même des options de **géoblocage** d’IP par pays. C’est du niveau "parano propre" pour stations sensibles 🔐.

| Protocole/Service | Commande PowerShell (reg add) |
| --- | --- |
| LLMNR | reg add "HKLM\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters" /v EnableMulticast /t REG_DWORD /d 0 /f |
| NetBIOS | reg add "HKLM\SYSTEM\CurrentControlSet\Services\NetBT\Parameters" /v NoNameReleaseOnDemand /t REG_DWORD /d 1 /f |
| WPAD | reg add "HKLM\Software\Policies\Microsoft\Windows\NetworkConnectivityStatusIndicator" /v NoWebProxyAutoDetect /t REG_DWORD /d 1 /f |
| mDNS | reg add "HKLM\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters" /v EnableMulticast /t REG_DWORD /d 0 /f |
| SSDP | reg add "HKLM\SYSTEM\CurrentControlSet\Services\SSDPSRV" /v Start /t REG_DWORD /d 4 /f |
| PNRP | reg add "HKLM\SYSTEM\CurrentControlSet\Services\PNRPsvc" /v Start /t REG_DWORD /d 4 /f |
| SMBv1 | reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" /v SMB1 /t REG_DWORD /d 0 /f |
| SMBGhost | reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" /v EnableSecuritySignature /t REG_DWORD /d 1 /f |
| Print Spooler | reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v Start /t REG_DWORD /d 4 /f |
| RDP sans NLA | reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f |
| WinRM HTTP | reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service" /v AllowUnencryptedTraffic /t REG_DWORD /d 0 /f |
| WMI / DCOM | reg add "HKLM\Software\Microsoft\Ole" /v EnableDCOM /t REG_SZ /d N /f |
| Telnet | reg add "HKLM\SYSTEM\CurrentControlSet\Services\TlntSvr" /v Start /t REG_DWORD /d 4 /f |
| FTP | reg add "HKLM\SYSTEM\CurrentControlSet\Services\ftpsvc" /v Start /t REG_DWORD /d 4 /f |
| TFTP | reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tftpd32" /v Start /t REG_DWORD /d 4 /f |
| SNMP | reg add "HKLM\SYSTEM\CurrentControlSet\Services\SNMP" /v Start /t REG_DWORD /d 4 /f |
| LDAP Signing | reg add "HKLM\SYSTEM\CurrentControlSet\Services\NTDS\Parameters" /v LDAPServerIntegrity /t REG_DWORD /d 2 /f |
| NTLMv1 | reg add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v LmCompatibilityLevel /t REG_DWORD /d 5 /f |
| PPTP | reg add "HKLM\SYSTEM\CurrentControlSet\Services\RasMan\PPP\EAP\25" /v Start /t REG_DWORD /d 4 /f |
| ICMP Redirects | reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v EnableICMPRedirect /t REG_DWORD /d 0 /f |
| Source Routing | reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v DisableIPSourceRouting /t REG_DWORD /d 2 /f |
| ICS | reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Network Connections" /v NC_ShowSharedAccessUI /t REG_DWORD /d 0 /f |
| Simple TCP | reg add "HKLM\SYSTEM\CurrentControlSet\Services\SimpTcp" /v Start /t REG_DWORD /d 4 /f |
| RIP | reg add "HKLM\SYSTEM\CurrentControlSet\Services\RIPService" /v Start /t REG_DWORD /d 4 /f |
| Fax | reg add "HKLM\SYSTEM\CurrentControlSet\Services\Fax" /v Start /t REG_DWORD /d 4 /f |
| Hyper-V | reg add "HKLM\SYSTEM\CurrentControlSet\Services\vmsrvc" /v Start /t REG_DWORD /d 4 /f |

```powershell
# 🔒 Désactive LLMNR (attaque Responder)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters" /v EnableMulticast /t REG_DWORD /d 0 /f

# 🔒 Bloque la libération de nom NetBIOS (évite les spoof)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NetBT\Parameters" /v NoNameReleaseOnDemand /t REG_DWORD /d 1 /f

# 🔒 Désactive l’auto-détection WPAD (attaque proxy hijack)
reg add "HKLM\Software\Policies\Microsoft\Windows\NetworkConnectivityStatusIndicator" /v NoWebProxyAutoDetect /t REG_DWORD /d 1 /f

# 🔒 Désactive SSDP (utilisé pour découverte réseau/latéral pivot)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SSDPSRV" /v Start /t REG_DWORD /d 4 /f

# 🔒 Désactive PNRP (protocole de résolution pair-à-pair)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PNRPsvc" /v Start /t REG_DWORD /d 4 /f

# 🔒 Désactive SMBv1 (faille EternalBlue, WannaCry)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" /v SMB1 /t REG_DWORD /d 0 /f

Disable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol" -NoRestart
Disable-WindowsOptionalFeature -Online -FeatureName "SMBDirect" -NoRestart

#    Désactive SMBv2 et SMBv3 coté client/serveur
New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" -Name "SMB2" -PropertyType DWORD -Value 0 -Force
New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\mrxsmb20" -Name "Start" -PropertyType DWORD -Value 4 -Force

# 🔒 Désactive la compression SMB (SMBGhost CVE-2020-0796)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" /v EnableCompressedTraffic /t REG_DWORD /d 0 /f

# 🔒 Stoppe le service Print Spooler (PrintNightmare, PetitPotam)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v Start /t REG_DWORD /d 4 /f

# 🔒 Oblige l’authentification NLA pour RDP
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

# 🔒 Interdit WinRM HTTP non chiffré (relay NTLM)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service" /v AllowUnencryptedTraffic /t REG_DWORD /d 0 /f

# 🔒 Désactive DCOM (risqué, à tester selon ton usage)
reg add "HKLM\Software\Microsoft\Ole" /v EnableDCOM /t REG_SZ /d N /f

# 🔒 Stoppe le service Telnet (clair, obsolète)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TlntSvr" /v Start /t REG_DWORD /d 4 /f

# 🔒 Stoppe FTP service (trafic non chiffré)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ftpsvc" /v Start /t REG_DWORD /d 4 /f

# 🔒 Stoppe TFTP (vieux service trop ouvert)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tftpd32" /v Start /t REG_DWORD /d 4 /f

# 🔒 Stoppe SNMP (exfiltration config réseau)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SNMP" /v Start /t REG_DWORD /d 4 /f

# 🔒 Force l’usage de NTLMv2 (bloque NTLMv1 vulnérable)
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v LmCompatibilityLevel /t REG_DWORD /d 5 /f

# 🔒 Bloque PPTP (protocole VPN cassable)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RasMan\Parameters" /v ProhibitPPTP /t REG_DWORD /d 1 /f

# 🔒 Interdit les redirections ICMP (MITM route)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v EnableICMPRedirect /t REG_DWORD /d 0 /f

# 🔒 Interdit le routage IP source (attaque spoof route)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v DisableIPSourceRouting /t REG_DWORD /d 2 /f

# 🔒 Cache l’interface de partage ICS
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Network Connections" /v NC_ShowSharedAccessUI /t REG_DWORD /d 0 /f

# 🔒 Désactive les services TCP simples (ex: echo, chargen)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SimpTcp" /v Start /t REG_DWORD /d 4 /f

# 🔒 Stoppe RIP Listener (protocole de routage obsolète)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RIPService" /v Start /t REG_DWORD /d 4 /f

# 🔒 Désactive Fax (service rarement utilisé et vulnérable)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Fax" /v Start /t REG_DWORD /d 4 /f

# 🔒 Stoppe Hyper-V compute service (si pas utilisé)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\vmcompute" /v Start /t REG_DWORD /d 4 /f

#    Désactive IPv6
New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" -Name "DisabledComponents" -PropertyType DWord -Value 0xFF -Force

# 🔒 Désactive totalement le routage IP source en IPv4 (anti-spoof routing)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v DisableIPSourceRouting /t REG_DWORD /d 2 /f

# 🔒 Désactive totalement le routage IP source en IPv6 (anti-spoof IPv6)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" /v DisableIPSourceRouting /t REG_DWORD /d 2 /f

# 🚫 Interdit les redirections ICMP (anti-MITM via fausse redirection)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v EnableICMPRedirect /t REG_DWORD /d 0 /f

# 🔐 Bloque les requêtes de libération de nom NetBIOS sauf pour les serveurs WINS
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NetBT\Parameters" /v NoNameReleaseOnDemand /t REG_DWORD /d 1 /f

# 🔒 Désactive complètement la résolution de nom multicast (LLMNR + autres)
reg add "HKLM\Software\Policies\Microsoft\Windows NT\DNSClient" /v EnableMulticast /t REG_DWORD /d 0 /f

# 🚫 Interdit les connexions invitées SMB non sécurisées (évite les accès anonymes)
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" /v AllowInsecureGuestAuth /t REG_DWORD /d 0 /f

# ⛔ Interdit le partage de connexion Internet (ICS) sur un domaine DNS
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Network Connections" /v NC_PersonalFirewallConfig /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Network Connections" /v NC_ShowSharedAccessUI /t REG_DWORD /d 0 /f

# 🚫 Désactive les connexions SMB invitées non sécurisées
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" /v AllowInsecureGuestAuth /t REG_DWORD /d 0 /f

# 🛑 Supprime les notifications pare-feu (empêche les popups d'autorisation)
reg add "HKLM\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile" /v DisableNotifications /t REG_DWORD /d 1 /f

# 🛡️ Active le pare-feu sur toutes les interfaces réseau du profil domaine
reg add "HKLM\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile" /v EnableFirewall /t REG_DWORD /d 1 /f

```

check services status

```powershell
# Liste des services à auditer
$services = @(
    "SSDPSRV", "PNRPsvc", "TlntSvr", "ftpsvc", "Tftpd32", "SNMP",
    "SimpTcp", "RIPService", "Fax", "Spooler", "vmcompute", "vmms",
    "WinRM", "LanmanServer", "LanmanWorkstation", "NetBT", "Dnscache",
    "w32time", "Winmgmt", "TermService", "BITS", "Themes", "SNMPTRAP", "wuauserv"
)

Write-Host "`n======================" -ForegroundColor Cyan
Write-Host "🔥 AUDIT DES SERVICES 🔥" -ForegroundColor Cyan
Write-Host "======================`n" -ForegroundColor Cyan

foreach ($svc in $services) {
    $status = sc.exe query $svc 2>$null
    if ($status -match "STATE\s+: 4\s+RUNNING") {
        Write-Host "$svc : 🔴 RUNNING" -ForegroundColor Red
    } elseif ($status -match "STATE\s+: 1\s+STOPPED") {
        Write-Host "$svc : ✅ STOPPED" -ForegroundColor Green
    } elseif ($status -eq $null) {
        Write-Host "$svc : ⚠️ UNKNOWN STATE (peut-être non installé)" -ForegroundColor DarkYellow
    } else {
        Write-Host "$svc : ⚠️ ÉTAT NON IDENTIFIÉ" -ForegroundColor Yellow
    }
}

# Vérification de l’état d’IPv6 (clé DisabledComponents)
Write-Host "`n======================" -ForegroundColor Cyan
Write-Host "🌐 STATUT DE IPV6 🌐" -ForegroundColor Cyan
Write-Host "======================" -ForegroundColor Cyan

try {
    $ipv6 = Get-ItemPropertyValue -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" -Name "DisabledComponents" -ErrorAction Stop
    if ($ipv6 -eq 255) {
        Write-Host "IPv6 : ✅ DÉSACTIVÉ (DisabledComponents = 0xFF)" -ForegroundColor Green
    } elseif ($ipv6 -eq 0) {
        Write-Host "IPv6 : 🔴 ACTIVÉ (DisabledComponents = 0x0)" -ForegroundColor Red
    } else {
        Write-Host "IPv6 : ⚠️ CONFIGURATION PARTIELLE (Valeur = $ipv6)" -ForegroundColor Yellow
    }
} catch {
    Write-Host "IPv6 : ⚠️ CLÉ ABSENTE (non configurée)" -ForegroundColor DarkYellow
}

```