# w11-automate
# Definir rutas
$dir = "C:\_Windows_FU\packages"
$file = "$dir\Win11Upgrade.exe"
$url = "https://go.microsoft.com/fwlink/?linkid=2171764"

# Crear directorio temporal
if (!(Test-Path $dir)) { New-Item -ItemType Directory -Force -Path $dir | Out-Null }

# Configuración de Red (Usa la configuración del sistema, funciona en Guest y Corp)
$webClient = New-Object System.Net.WebClient
$webClient.Proxy = [System.Net.WebRequest]::DefaultWebProxy
$webClient.Proxy.Credentials = [System.Net.CredentialCache]::DefaultNetworkCredentials

try {
    # 1. Descargar
    $webClient.DownloadFile($url, $file)

    # 2. Ejecutar (Deja que Tanium siga vivo, Windows gestionará si hay tráfico)
    # /SkipCompatCheck: Para que no pierda tiempo.
    # /NoRestartUI: Para que no asuste al usuario.
    Start-Process -FilePath $file -ArgumentList "/QuietInstall /SkipEULA /SkipCompatCheck /auto upgrade /NoRestartUI /UninstallUponUpgrade" -Wait
}
catch {
    Write-Error "Fallo en la descarga o ejecución: $_"
    Exit 1
}
