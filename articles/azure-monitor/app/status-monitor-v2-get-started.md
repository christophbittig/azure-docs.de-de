---
title: 'Azure Application Insights-Agent: erste Schritte | Microsoft-Dokumentation'
description: Eine Schnellstartanleitung für den Application Insights-Agent. Überwachen Sie die Websiteleistung ohne erneute Bereitstellung der Website. Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 01/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2918a9dc1bbf514b6ab915d9e1d691b9ae4f95d3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346125"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Erste Schritte mit dem Azure Monitor Application Insights-Agent für lokale Server

Dieser Artikel enthält die Schnellstartbefehle, die für die meisten Umgebungen geeignet sein dürften.
Die Anweisungen richten sich nach dem für das Verteilen von Updates verwendeten PowerShell-Katalog.
Diese Befehle unterstützen den `-Proxy`-Parameter von PowerShell.

Eine Erläuterung der Befehle, Anpassungsanweisungen und Informationen zur Problembehandlung finden Sie unter [Ausführliche Anleitungen](status-monitor-v2-detailed-instructions.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="download-and-install-via-powershell-gallery"></a>Herunterladen und Installieren über PowerShell-Katalog

### <a name="install-prerequisites"></a>Installieren der erforderlichen Komponenten

- Sie benötigen eine Verbindungszeichenfolge, um die Überwachung aktivieren zu können. Eine Verbindungszeichenfolge wird auf dem Übersichtsblatt Ihrer Application Insights-Ressource angezeigt. Weitere Informationen finden Sie unter [Wie finde ich meine Verbindungszeichenfolge heraus?](./sdk-connection-string.md?tabs=net#finding-my-connection-string).

> [!NOTE]
> Im April 2020 wurden TLS 1.1 und 1.0 im PowerShell-Katalog als veraltet markiert.
>
> Informationen zu weiteren möglichen Voraussetzungen finden Sie unter [PowerShell-Katalog: TLS-Unterstützung](https://devblogs.microsoft.com/powershell/powershell-gallery-tls-support).
>

Führen Sie PowerShell als Administrator aus.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Schließen Sie PowerShell.

### <a name="install-application-insights-agent"></a>Installieren des Application Insights-Agents
Führen Sie PowerShell als Administrator aus.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

> [!NOTE]
> Mit der Option `AllowPrerelease` im Cmdlet `Install-Module` wird die Installation eines Betarelease zugelassen. 
>
> Weitere Informationen finden Sie unter [Install-Module](/powershell/module/powershellget/install-module?view=powershell-7.1#parameters).
>

### <a name="enable-monitoring"></a>Aktivieren der Überwachung

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -ConnectionString 'InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Manuelles Herunterladen und Installieren (Offline-Option)
### <a name="download-the-module"></a>Herunterladen des Moduls
Laden Sie die neueste Version des Moduls aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) manuell herunter.

### <a name="unzip-and-install-application-insights-agent"></a>Entzippen und Installieren des Application Insights-Agents
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Aktivieren der Überwachung

```powershell
Enable-ApplicationInsightsMonitoring -ConnectionString 'InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```




## <a name="next-steps"></a>Nächste Schritte

 Anzeigen der Telemetrie:

- [Untersuchen Sie Metriken](../essentials/metrics-charts.md) zum Überwachen der Leistung und Nutzung.
- [Durchsuchen Sie Ereignisse und Protokolle](./diagnostic-search.md), um Probleme zu diagnostizieren.
- Verwenden Sie [Analytics](../logs/log-query-overview.md) für erweiterte Abfragen.
- [Erstellen Sie Dashboards](./overview-dashboard.md).

 Hinzufügen weiterer Telemetrieelemente:

- [Erstellen Sie Webtests](monitor-web-app-availability.md), um sicherzustellen, dass Ihre Website live bleibt.
- [Fügen Sie Webclient-Telemetriedaten hinzu](./javascript.md), um Ausnahmen von Webseitencode anzuzeigen und Ablaufverfolgungsaufrufe zu aktivieren.
- [Fügen Sie Ihrem Code das Application Insights SDK hinzu](./asp-net.md), um Ablaufverfolgungs- und Protokollaufrufe einfügen zu können.

Weitere Verwendungsmöglichkeiten für den Application Insights-Agent:

- Beachten Sie die [ausführlichen Anleitungen](status-monitor-v2-detailed-instructions.md). Dort finden Sie eine Erläuterung der hier verwendeten Befehle.
- Verwenden Sie unsere Anleitung zur [Problembehandlung](status-monitor-v2-troubleshoot.md) für den Application Insights-Agent.