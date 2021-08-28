---
title: Überwachen eines Azure-Clouddiensts (klassisch) | Microsoft-Dokumentation
description: Hier wird beschrieben, welche Aspekte die Überwachung eines Azure-Clouddiensts umfasst und welche Optionen verfügbar sind.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3c0ac84551905f365f7b5e728fecb40eb4a963d9
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823696"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Einführung in die Überwachung von Clouddiensten (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Sie können wichtige Leistungsmetriken für beliebige Clouddienste überwachen. Für jede Clouddienstrolle werden minimale Daten zur CPU-Nutzung, Netzwerkverwendung und Datenträgerverwendung erfasst. Wenn auf eine Rolle des Clouddiensts die Erweiterung `Microsoft.Azure.Diagnostics` angewendet wurde, können für die Rolle zusätzliche Datenpunkte erfasst werden. Dieser Artikel bietet eine Einführung in die Azure-Diagnose für Clouddienste.

Bei der grundlegenden Überwachung werden alle drei Minuten Leistungsindikatordaten aus den Rolleninstanzen abgefragt und erfasst. Daten aus der grundlegenden Überwachung werden nicht unter Ihrem Speicherkonto gespeichert und verursachen keine zusätzlichen Kosten.

Bei der erweiterten Überwachung werden in Intervallen von fünf Minuten, einer Stunde und zwölf Stunden zusätzliche Metriken abgefragt und erfasst. Die aggregierten Daten werden unter einem Speicherkonto in Tabellen gespeichert und nach zehn Tagen gelöscht. Das verwendete Speicherkonto wird nach der Rolle konfiguriert. Sie können verschiedene Speicherkonten für verschiedene Rollen verwenden. Die Konfiguration erfolgt mithilfe einer Verbindungszeichenfolge in der [CSDEF](cloud-services-model-and-package.md#servicedefinitioncsdef)- und [CSCFG](cloud-services-model-and-package.md#serviceconfigurationcscfg)-Datei.


## <a name="basic-monitoring"></a>Grundlegende Überwachung

Wie in der Einführung beschrieben, erfasst ein Clouddienst automatisch grundlegende Überwachungsdaten vom virtuellen Hostcomputer. Zu diesen Daten gehören: CPU-Prozentsatz, Netzwerk-E/A und Lese-/Schreibzugriffe auf Datenträger. Die erfassten Überwachungsdaten werden automatisch auf den Übersichts- und Metrikseiten des Clouddiensts im Azure-Portal angezeigt. 

Für die grundlegende Überwachung wird kein Speicherkonto benötigt. 

![Kacheln der grundlegenden Clouddienstüberwachung](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Erweiterte Überwachung

Bei der erweiterten Überwachung wird die **Azure-Diagnose** (und optional das Application Insights-SDK) für die zu überwachende Rolle verwendet. Die Diagnoseerweiterung verwendet (pro Rolle) eine Konfigurationsdatei mit dem Namen **diagnostics.wadcfgx** zur Konfiguration der überwachten Diagnosemetriken. Die Azure-Diagnoseerweiterung sammelt und speichert Daten in einem Azure Storage-Konto. Diese Einstellungen werden in den **WADCFGX**-, [CSDEF](cloud-services-model-and-package.md#servicedefinitioncsdef)- und [CSCFG](cloud-services-model-and-package.md#serviceconfigurationcscfg)-Dateien konfiguriert. Dies bedeutet, dass die erweiterte Überwachung mit zusätzlichen Kosten verbunden ist.

Während der Erstellung einer Rolle wird ihr von Visual Studio die Azure-Diagnoseerweiterung hinzugefügt. Diese Diagnoseerweiterung kann die folgenden Informationstypen erfassen:

* Benutzerdefinierte Leistungsindikatoren
* Anwendungsprotokolle
* Windows-Ereignisprotokolle
* .NET-Ereignisquelle
* IIS-Protokolle
* Manifestbasiertes ETW
* Absturzabbilder
* Benutzerdefinierte Fehlerprotokolle

> [!IMPORTANT]
> Die Daten werden zwar vollständig unter dem Speicherkonto aggregiert, allerdings bietet das Portal **keine** native Möglichkeit, die Daten in einem Diagramm darzustellen. Es wird dringend empfohlen, dass Sie einen anderen Dienst, z.B. Application Insights, in die Anwendung integrieren.

## <a name="setup-diagnostics-extension"></a>Einrichten der Diagnoseerweiterung

Wenn Sie noch nicht über ein **klassisches** Speicherkonto verfügen, [erstellen Sie zunächst ein Konto](../storage/common/storage-account-create.md). Stellen Sie sicher, dass das Speicherkonto mit dem **klassischen Bereitstellungsmodell** erstellt wird.

Navigieren Sie als Nächstes zur Ressource **Speicherkonto (klassisch)**. Wählen Sie **Einstellungen** > **Zugriffsschlüssel** aus, und kopieren Sie den Wert unter **Primäre Verbindungszeichenfolge**. Sie benötigen diesen Wert für den Clouddienst. 

Es gibt zwei Konfigurationsdateien, die Sie zur Aktivierung der erweiterten Diagnose ändern müssen: **ServiceDefinition.csdef** und **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

Fügen Sie in der Datei **ServiceDefinition.csdef** für jede Rolle, für die die erweiterte Diagnose verwendet werden soll, eine neue Einstellung mit dem Namen `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` hinzu. Dieser Wert wird der Datei von Visual Studio hinzugefügt, wenn Sie ein neues Projekt erstellen. Falls sie fehlt, können Sie sie jetzt hinzufügen. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Dadurch wird eine neue Einstellung definiert, die jeder Datei **ServiceConfiguration.cscfg** hinzugefügt werden muss. 

Wahrscheinlich verfügen Sie über zwei **.cscfg**-Dateien, eine mit dem Namen **ServiceConfiguration.cloud.cscfg** für die Bereitstellung in Azure und eine mit dem Namen **ServiceConfiguration.local.cscfg**, die für lokale Bereitstellungen in der emulierten Umgebung verwendet wird. Öffnen und ändern Sie jede **CSCFG**-Datei. Fügen Sie eine Einstellung mit dem Namen `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` hinzu. Legen Sie den Wert auf die **primäre Verbindungszeichenfolge** des klassischen Speicherkontos fest. Zur Nutzung des lokalen Speichers auf dem Entwicklungscomputer verwenden Sie `UseDevelopmentStorage=true`.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Verwenden von Application Insights

Wenn Sie den Clouddienst über Visual Studio veröffentlichen, können Sie die Diagnosedaten an Application Insights senden. Sie können die Azure-Ressource von Application Insights dann erstellen oder die Daten an eine vorhandene Azure-Ressource senden. Ihr Clouddienst kann von Application Insights auf Verfügbarkeit, Leistung, Fehler und Auslastung überwacht werden. Application Insights können benutzerdefinierte Diagramme hinzugefügt werden. So haben Sie immer die relevanten Daten im Blick. Rolleninstanzdaten können mithilfe des Application Insights SDKs in Ihrem Clouddienstprojekt erfasst werden. Weitere Informationen zur Integration von Application Insights finden Sie unter [Application Insights für Azure Cloud Services](../azure-monitor/app/cloudservices.md).

Hinweis: Sie können die Leistungsindikatoren (und weiteren Einstellungen), die Sie über die Microsoft Azure-Diagnoseerweiterung festgelegt haben, mithilfe von Application Insights anzeigen, erhalten allerdings noch bessere Darstellungsmöglichkeiten, wenn Sie das Application Insights SDK in die Worker- und Webrollen integrieren.


## <a name="next-steps"></a>Nächste Schritte

- [Informationen zu Application Insights mit Cloud Services](../azure-monitor/app/cloudservices.md)
- [Einrichten von Leistungsindikatoren](diagnostics-performance-counters.md)




