---
title: Übersicht über die Runtimeversionen von Azure Functions
description: Azure Functions unterstützt mehrere Versionen der Runtime. Lernen Sie die Unterschiede kennen, und erfahren Sie, wie Sie die Version auswählen, die sich am besten für Ihre Anforderungen eignet.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 11/3/2021
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: a3d84e01bd183feb09f9594295111d98713c0373
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058654"
---
# <a name="azure-functions-runtime-versions-overview"></a>Übersicht über die Runtimeversionen von Azure Functions

<a name="top"></a>Azure Functions unterstützt derzeit mehrere Versionen des Runtime-Hosts. In der folgenden Tabelle sind die verfügbaren Versionen, deren Supportebene und wann Sie sie benutzen sollten, aufgeführt:

| Version | Supportebene | Beschreibung |
| --- | --- | --- |
| 4.x | Allgemein verfügbar | _Empfohlene Runtime-Version für Funktionen in allen Sprachen._ Verwenden Sie diese Version, [um C#-Funktionen unter .NET 6.0 auszuführen](functions-dotnet-class-library.md#supported-versions). |
| 3.x | Allgemein verfügbar | Unterstützt alle Sprachen. Verwenden Sie diese Version, um [C#-Funktionen unter .NET Core 3.1 und .NET 5.0 auszuführen](functions-dotnet-class-library.md#supported-versions).|
| 2.x | Allgemein verfügbar | Wird für [ältere Versionen von 2.x-Apps](#pinning-to-version-20) unterstützt. Diese Version befindet sich im Wartungsmodus. Verbesserungen werden erst in späteren Versionen bereitgestellt.|
| 1.x | Allgemein verfügbar | Wird nur für C#-Apps empfohlen, die .NET Framework verwenden müssen und unterstützt nur die Entwicklung im Azure-Portal, Azure Stack Hub-Portal oder lokal auf Windows. Diese Version befindet sich im Wartungsmodus. Verbesserungen werden erst in späteren Versionen bereitgestellt. |

In diesem Artikel werden einige Unterschiede zwischen diesen Versionen, das Erstellen der einzelnen Versionen und das Ändern von Versionen, in denen Ihre Funktionen laufen, erläutert.

[!INCLUDE [functions-support-levels](../../includes/functions-support-levels.md)]

## <a name="languages"></a>Languages

Ab Version 2.x verwendet die Runtime ein Modell für die Erweiterbarkeit von Sprachen, und alle Funktionen in einer Funktions-App müssen dieselbe Sprache aufweisen. Sie haben beim Erstellen der App die Sprache der Funktionen in Ihrer Funktions-App ausgewählt. Die Sprache Ihrer Funktions-App wird in der Einstellung [FUNCTIONS\_WORKER\_RUNTIME](functions-app-settings.md#functions_worker_runtime) beibehalten und sollte nicht geändert werden, wenn Funktionen vorhanden sind. 

Die folgende Tabelle zeigt, welche Programmiersprachen derzeit in den einzelnen Runtimeversionen unterstützt werden.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Ausführen auf einer spezifischen Version

Im Azure-Portal und durch die Azure CLI erstellte Funktions-Apps sind standardmäßig auf Version 3.x festgelegt. Diese Version kann bei Bedarf geändert werden. Für die Runtimeversion kann nur ein Downgrade in 1.x ausgeführt werden, wenn Sie Ihre Funktions-App erstellt, aber noch keine Funktionen hinzugefügt haben.  Der Wechsel zwischen 2.x und 3.x ist auch bei Apps möglich, die bereits über Funktionen verfügen. Bevor Sie eine App mit vorhandenen Funktionen von 2.x nach 3.x verschieben, achten Sie auf [Breaking Changes zwischen 2.x und 3.x](#breaking-changes-between-2x-and-3x). 

Bevor Sie eine Änderung an der Hauptversion der Laufzeit vornehmen, sollten Sie zunächst Ihren vorhandenen Code testen, indem Sie ihn für eine andere Funktions-App bereitstellen, die mit der neuesten Hauptversion ausgeführt wird. Diese Tests helfen sicherzustellen, dass er nach dem Upgrade ordnungsgemäß ausgeführt wird. 

Downgrades von v3.x auf v2.x werden nicht unterstützt. Wenn möglich, sollten Sie Ihre Apps immer mit der neuesten unterstützten Version der Functions-Laufzeit ausführen. 

### <a name="changing-version-of-apps-in-azure"></a>Ändern der Version von Apps in Azure

Welche Version der Functions-Runtime von veröffentlichten Apps in Azure verwendet wird, wird durch die [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version)-Anwendungseinstellung bestimmt. Für die Hauptversion der Runtime werden folgende Werte unterstützt:

| Wert | Runtimeziel |
| ------ | -------- |
| `~4` | 4.x |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Ändern Sie diese Einstellung mit Bedacht, da hierdurch unter Umständen weitere Änderungen an App-Einstellungen und Ihrem Funktionscode nötig werden.

Weitere Informationen finden Sie unter [How to target Azure Functions runtime versions](set-runtime-version.md) (Einstellung auf bestimmte Laufzeitversionen von Azure Functions).  

### <a name="pinning-to-a-specific-minor-version"></a>Anheften an eine bestimmte Nebenversion

Zum Beheben von Problemen mit ihrer Funktions-App, die mit der neuesten Hauptversion ausgeführt wird, müssen Sie Ihre App an eine bestimmte Nebenversion anheften. Dies gibt Ihnen Zeit, Ihre App mit der neuesten Hauptversion ordnungsgemäß auszuführen. Die Art und Weise, wie Sie die App an eine Nebenversion anheften, unterscheidet sich zwischen Windows und Linux. Weitere Informationen finden Sie unter [How to target Azure Functions runtime versions](set-runtime-version.md) (Einstellung auf bestimmte Laufzeitversionen von Azure Functions).

Ältere Nebenversionen werden regelmäßig aus Functions entfernt. Aktuelle Informationen zu Azure Functions Releases (einschließlich der Entfernung bestimmter älterer Nebenversionen) finden Sie unter [Azure App Service-Ankündigungen](https://github.com/Azure/app-service-announcements/issues). 

### <a name="pinning-to-version-20"></a>Anheften an Version ~2.0

.NET-Funktions-Apps, die mit Version 2.x (`~2`) ausgeführt werden, werden automatisch auf .NET Core 3.1 aktualisiert, eine langfristige Supportversion von .NET Core 3. Wenn Sie Ihre .NET-Funktionen mit .NET Core 3.1 ausführen, können Sie die neuesten Sicherheitsupdates und Produktverbesserungen nutzen. 

Jede Funktions-App, die an `~2.0` angeheftet wird, kann weiterhin unter .NET Core 2.2 ausgeführt werden. Diese Version erhält keine Sicherheitsupdates und andere Updates mehr. Weitere Informationen finden Sie unter [Überlegungen zu Functions v2.x](functions-dotnet-class-library.md#functions-v2x-considerations).   

## <a name="migrating-from-3x-to-4x"></a><a name="migrating-from-3x-to-4x"></a>Migrieren von 3.x zu 4.x

Die Azure Functions-Version 4.x bietet eine hohe Abwärtskompatibilität mit der Version 3.x. Bei vielen Apps sollte ein Upgrade auf Version 4.x ohne erhebliche Codeänderungen problemlos möglich sein. Führen Sie ausführliche Tests durch, bevor Sie die Hauptversion in Produktions-Apps ändern.

### <a name="upgrading-an-existing-app"></a>Aktualisieren einer vorhandenen App

Wenn Sie Ihre Funktions-App lokal entwickeln, müssen Sie sowohl Ihre lokale Projektumgebung als auch Ihre in Azure ausgeführte Funktions-App aktualisieren. 

#### <a name="local-project"></a>Lokales Projekt

Upgradeanweisungen können sprachabhängig sein. Wenn Ihre Sprache nicht angezeigt wird, schalten Sie sie [oben im Artikel](#top) um.

::: zone pivot="programming-language-csharp"  
Um eine C#-Klassenbibliotheks-App auf .NET 6 und Azure Functions 4.x zu aktualisieren, aktualisieren Sie das `TargetFramework` und die `AzureFunctionsVersion`:

```xml
<TargetFramework>net6.0</TargetFramework>
<AzureFunctionsVersion>v4</AzureFunctionsVersion>
```

Außerdem müssen Sie sicherstellen, dass die NuGet-Pakete, auf die Ihre Anwendung verweist, auf die neuesten Versionen aktualisiert wurden. Weitere Informationen finden Sie unter [Breaking Changes](#breaking-changes-between-3x-and-4x). Bestimmte Pakete hängen davon ab, ob Ihre Funktionen in-process oder out-of-process ausgeführt werden. 

# <a name="in-process"></a>[In-Process](#tab/in-process)

* [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 4.0.0 oder höher

# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

* [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/) 1.5.2 oder höher
* [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/) 1.2.0 oder höher

---
::: zone-end  
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Um Ihre App auf Azure Functions 4.x zu aktualisieren, aktualisieren Sie Ihre lokal installierte Version von [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) auf 4.x, und aktualisieren Sie das [Azure Functions-Erweiterungspaket](functions-bindings-register.md#extension-bundles) für die App auf 2.x oder höher. Weitere Informationen finden Sie unter [Breaking Changes](#breaking-changes-between-3x-and-4x).

::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
> [!NOTE]
> Node.js 10 und 12 werden von Azure Functions 4.x nicht unterstützt.
::: zone-end  
::: zone pivot="programming-language-powershell"  
> [!NOTE]
> PowerShell 6 wird von Azure Functions 4.x nicht unterstützt.
::: zone-end  
::: zone pivot="programming-language-python"  
> [!NOTE]
> Python 3.6 wird von Azure Functions 4.x nicht unterstützt.
::: zone-end

#### <a name="azure"></a>Azure

Um eine App von 3.x zu 4.x zu migrieren, legen Sie die `FUNCTIONS_EXTENSION_VERSION`-Anwendungseinstellung auf `~4` fest, indem Sie den folgenden Azure CLI-Befehl verwenden:

```bash
az functionapp config appsettings set --settings FUNCTIONS_EXTENSION_VERSION=~4 -n <APP_NAME> -g <RESOURCE_GROUP_NAME>

# For Windows function apps only, also enable .NET 6.0 that is needed by the runtime
az functionapp config set --net-framework-version v6.0 -n <APP_NAME> -g <RESOURCE_GROUP_NAME>
```

### <a name="breaking-changes-between-3x-and-4x"></a>Wichtige Unterschiede zwischen 3.x und 4.x

In diesem Abschnitt werden die Änderungen erläutert, die vor einem App-Upgrade von 3.x auf 4.x beachtet werden müssen. Eine vollständige Liste finden Sie unter Azure Functions GitHub mit der Bezeichnung [*Breaking Change: Approved*](https://github.com/Azure/azure-functions/issues?q=is%3Aissue+label%3A%22Breaking+Change%3A+Approved%22+is%3A%22closed+OR+open%22). Während des Vorschauzeitraums werden weitere Änderungen erwartet. Abonnieren Sie [App Service Ankündigungen](https://github.com/Azure/app-service-announcements/issues) für Updates.

#### <a name="runtime"></a>Typ

- Azure-Funktionsproxys werden in 4.x nicht mehr unterstützt. Es wird empfohlen, [Azure API Management](../api-management/import-function-app-as-api.md) zu benutzen.

- Sich bei Azure Storage mithilfe von *AzureWebJobsDashboard* anzumelden, wird in 4.x nicht mehr unterstützt. Es wird empfohlen, [Application Insights](./functions-monitoring.md) zu benutzen. ([#1923](https://github.com/Azure/Azure-Functions/issues/1923))

- Azure Functions 4.x erzwingt [Mindestversionsanforderungen](https://github.com/Azure/Azure-Functions/issues/1987) für Erweiterungen. Führen Sie ein Upgrade auf die neueste Version der betroffenen Erweiterungen durch. Für non-.NET Sprachen führen Sie ein [Upgrade](./functions-bindings-register.md#extension-bundles) auf Version 2.x oder höher des Erweiterungspakets durch. ([#1987](https://github.com/Azure/Azure-Functions/issues/1987))

- Standardmäßige und maximale Timeouts werden jetzt in 4.x Linux-Verbrauchsfunktions-Apps erzwungen. ([#1915](https://github.com/Azure/Azure-Functions/issues/1915))

- Funktions-Apps, die Speicherkonten gemeinsam nutzen, können nicht gestartet werden, wenn ihre berechneten Hostnamen identisch sind. Verwenden Sie ein separates Speicherkonto für jede Funktions-App. ([#2049](https://github.com/Azure/Azure-Functions/issues/2049))

::: zone pivot="programming-language-csharp" 

- Azure Functions 4.x unterstützt .NET 6 In-Process und isolierte Apps.

- `InvalidHostServicesException` ist jetzt ein schwerwiegender Fehler. ([#2045](https://github.com/Azure/Azure-Functions/issues/2045))

- `EnableEnhancedScopes` ist standardmäßig aktiviert. ([#1954](https://github.com/Azure/Azure-Functions/issues/1954))

- Entfernt `HttpClient` als registrierten Dienst. ([#1911](https://github.com/Azure/Azure-Functions/issues/1911))
::: zone-end  
::: zone pivot="programming-language-java"  
- Verwenden eines Klassenladers in Java 11. ([#1997](https://github.com/Azure/Azure-Functions/issues/1997))

- Beendet das Laden von Worker-JAR-Dateien in Java 8. ([#1991](https://github.com/Azure/Azure-Functions/issues/1991))
::: zone-end    
::: zone pivot="programming-language-javascript,programming-language-typescript"  

- Node.js 10 und 12 werden von Azure Functions 4.x nicht unterstützt. ([#1999](https://github.com/Azure/Azure-Functions/issues/1999))

- Die Ausgabeserialisierung in Node.js-Apps wurde aktualisiert, um frühere Inkonsistenzen zu beheben. ([#2007](https://github.com/Azure/Azure-Functions/issues/2007))
::: zone-end  
::: zone pivot="programming-language-powershell"  
- PowerShell 6 wird von Azure Functions 4.x nicht unterstützt. ([#1999](https://github.com/Azure/Azure-Functions/issues/1999))

- Standardthreadanzahl wurde aktualisiert. Funktionen, die nicht threadsicher sind oder über eine hohe Arbeitsspeicherauslastung verfügen, können betroffen sein. ([#1962](https://github.com/Azure/Azure-Functions/issues/1962))
::: zone-end  
::: zone pivot="programming-language-python"  
- Python 3.6 wird von Azure Functions 4.x nicht unterstützt. ([#1999](https://github.com/Azure/Azure-Functions/issues/1999))

- Shared Memory-Übertragung ist standardmäßig aktiviert. ([#1973](https://github.com/Azure/Azure-Functions/issues/1973))

- Standardthreadanzahl wurde aktualisiert. Funktionen, die nicht threadsicher sind oder über eine hohe Arbeitsspeicherauslastung verfügen, können betroffen sein. ([#1962](https://github.com/Azure/Azure-Functions/issues/1962))
::: zone-end

## <a name="migrating-from-2x-to-3x"></a>Migrieren von 2.x zu 3.x

Die Azure Functions-Version 3.x bietet eine hohe Abwärtskompatibilität mit der Version 2.x.  Bei vielen Apps sollte problemlos ein Upgrade auf die Version 3.x möglich sein, ohne Codeänderungen vornehmen zu müssen. Die Migration zur Version 3.x wird zwar empfohlen, trotzdem sollten ausführliche Tests durchgeführt werden, bevor die Hauptversion in Produktions-Apps geändert wird.

### <a name="breaking-changes-between-2x-and-3x"></a>Breaking Changes zwischen 2.x und 3.x

In diesem Abschnitt werden die Änderungen erläutert, die vor einem App-Upgrade von 2.x auf 3.x beachtet werden müssen.

::: zone pivot="programming-language-csharp"
Der Hauptunterschied zwischen den Versionen bei der Ausführung von Funktionen der .NET-Klassenbibliothek ist die .NET Core-Laufzeit. Functions Version 2.x ist für die Ausführung unter .NET Core 2.2 vorgesehen, Version 3.x ist für die Ausführung mit .NET Core 3.1 konzipiert.  

* [Synchrone Servervorgänge sind standardmäßig deaktiviert.](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers)

* Von .NET Core wurden in [Version 3.1](/dotnet/core/compatibility/3.1) und [Version 3.0](/dotnet/core/compatibility/3.0) Breaking Changes eingeführt, die nicht spezifisch für Functions sind, aber dennoch Ihre App betreffen können.

>[!NOTE]
>Aufgrund von Supportproblemen mit .NET Core 2.2 werden Funktions-Apps, die an Version 2 (`~2`) angeheftet sind, im Wesentlichen mit .NET Core 3.1 ausgeführt. Weitere Informationen finden Sie unter [Functions v2.x-Kompatibilitätsmodus](functions-dotnet-class-library.md#functions-v2x-considerations).

::: zone-end  
::: zone pivot="programming-language-javascript"  

* Über `context.done` oder Rückgabewerte zugewiesene Ausgabebindungen weisen nun das gleiche Verhalten auf wie die Einstellung in `context.bindings`.

* Das Zeitgebertrigger-Objekt wird im camelCase-Format angegeben (nicht im PascalCase-Format).

* Funktionen, die von Event Hub mit binärem Datentyp (`dataType`) ausgelöst werden, erhalten ein Array vom Typ `binary` (anstelle von `string`).

* Auf die Nutzlast von HTTP-Anforderungen kann nicht mehr über `context.bindingData.req` zugegriffen werden.  Der Zugriff darauf ist aber weiterhin als Eingabeparameter (`context.req`) und in `context.bindings` möglich.

* Node.js 8 wird nicht mehr unterstützt und in Funktionen der Version 3.x nicht ausgeführt.
::: zone-end 

## <a name="migrating-from-1x-to-later-versions"></a>Migrieren von 1.x zu neueren Versionen

Eine vorhandene App, die für die Runtimeversion 1.x geschrieben wurde, kann zu einer neueren Version migriert werden. Die meisten erforderlichen Änderungen hängen mit der Sprachruntime zusammen – also beispielsweise C#-API-Änderungen zwischen .NET Framework 4.8 und .NET Core. Sie müssen auch sicherstellen, dass Ihr Code und Ihre Bibliotheken mit den ausgewählten Sprachruntimes kompatibel sind. Beachten Sie nicht zuletzt auch die unten genannten Änderungen an Triggern, Bindungen und Funktionen. Um ein optimales Migrationsergebnis zu erzielen, sollten Sie eine neue Funktions-App in einer neuen Version erstellen und Ihren vorhandenen Funktionscode der Version 1.x zur neuen App portieren.  

Es besteht zwar die Möglichkeit eines direkten Upgrades durch manuelles Aktualisieren der App-Konfiguration, die Umstellung von 1.x auf eine höhere Version beinhaltet jedoch einige Breaking Changes. In C# ändert sich beispielsweise das Debuggingobjekt von `TraceWriter` in `ILogger`. Durch die Erstellung eines neuen Projekts mit der Version 3.x stehen die aktualisierten Funktionen auf der Grundlage der neuesten Vorlagen der Version 3.x zur Verfügung.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Änderungen bei Triggern und Bindungen nach der Version 1.x

Ab der Version 2.x müssen die Erweiterungen für bestimmte Trigger und Bindungen installiert werden, die von den Funktionen in Ihrer App verwendet werden. Die einzigen Ausnahmen sind HTTP- und Timertrigger, für die keine Erweiterung erforderlich ist.  Weitere Informationen finden Sie unter [Registrieren und Installieren von Bindungserweiterungen](./functions-bindings-register.md).

Zwischen den Versionen gibt es auch einige Änderungen an der Datei *function.json* sowie an Attributen der Funktion. Die Event Hub-Eigenschaft `path` beispielsweise lautet jetzt `eventHubName`. Links zur Dokumentation für die einzelnen Bindungen finden Sie in der [Tabelle der vorhandenen Bindungen](#bindings).

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Änderungen bei Features und Funktionen nach der Version 1.x

Einige Features wurden nach der Version 1.x entfernt, aktualisiert oder ersetzt. In diesem Abschnitt werden die Änderungen nach der Version 1.x erläutert.

In Version 2.x wurden die folgenden Änderungen vorgenommen:

* Schlüssel für aufrufende HTTP-Endpunkte werden immer verschlüsselt in Azure Blob Storage gespeichert. In Version 1.x wurden Schlüssel standardmäßig in Azure Files gespeichert. Bei einem App-Upgrade von Version 1.x auf Version 2.x werden in Azure Files vorhandene Geheimnisse zurückgesetzt.

* Version 2.x der Runtime umfasst keine integrierte Unterstützung für Webhookanbieter. Diese Änderung wurde vorgenommen, um die Leistung zu verbessern. Sie können weiterhin HTTP-Trigger als Endpunkte für Webhooks verwenden.

* Die Hostkonfigurationsdatei (host.json) muss leer sein oder die Zeichenfolge `"version": "2.0"` enthalten.

* Zur Verbesserung der Überwachung wurde das WebJobs-Dashboard im Portal, das die Einstellung [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) verwendete, durch Azure Application Insights ersetzt – hierbei wird die Einstellung [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) verwendet. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).

* Alle Funktionen in einer Funktions-App müssen die gleiche Sprache verwenden. Wenn Sie eine Funktions-App erstellen, müssen Sie einen Runtimestapel für die App auswählen. Der Runtimestapel wird durch den [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime)-Wert in den Anwendungseinstellungen angegeben. Diese Anforderung wurde hinzugefügt, um den Speicherbedarf und die Startzeit zu verbessern. Bei der lokalen Entwicklung müssen Sie diese Einstellung auch in die [Datei „local.settings.json“](functions-develop-local.md#local-settings-file) einschließen.

* Das Standardzeitlimit für Funktionen in einem App Service-Plan wurde zu 30 Minuten geändert. Sie können das Zeitlimit mit der [functionTimeout](functions-host-json.md#functiontimeout)-Einstellung in der host.json-Datei manuell wieder zu „unbegrenzt“ ändern.

* HTTP-Parallelitätsdrosselungen sind standardmäßig für Verbrauchsplanfunktionen implementiert. Der Standardwert beträgt 100 gleichzeitige Anforderungen pro Instanz. Sie können diesen Wert in der [`maxConcurrentRequests`](functions-host-json.md#http)-Einstellung in der host.json-Datei ändern.

* Aufgrund der [Einschränkungen von .NET Core](https://github.com/Azure/azure-functions-host/issues/3414) wurde die Unterstützung für F#-Skriptfunktionen (FSX) entfernt. Kompilierte F#-Funktionen (FS) werden weiterhin unterstützt.

* Das URL-Format von Event Grid-Triggerwebhooks wurde zu `https://{app}/runtime/webhooks/{triggerName}` geändert.

### <a name="locally-developed-application-versions"></a>Lokal entwickelte Anwendungsversionen

Sie können folgende Aktualisierungen für Funktions-Apps vornehmen, um die Zielversionen lokal zu ändern.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio-Runtimeversionen

In Visual Studio wählen Sie die Runtimeversion beim Erstellen eines Projekts aus. Azure Functions-Tools für Visual Studio unterstützen die drei Hauptversionen der Runtime. Beim Debuggen und Veröffentlichen wird die richtige Version verwendet, basierend auf den Projekteinstellungen. Die Versionseinstellungen sind in der `.csproj`-Datei in den folgenden Einstellungen definiert:

# <a name="version-4x"></a>[Version 4.x](#tab/v4)

```xml
<TargetFramework>net6.0</TargetFramework>
<AzureFunctionsVersion>v4</AzureFunctionsVersion>
```

> [!NOTE]
> Für Azure Functions 4.x und .NET muss die `Microsoft.NET.Sdk.Functions`-Erweiterung mindestens die Version `4.0.0`haben.

# <a name="version-3x"></a>[Version 3.x](#tab/v3)

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Für Azure Functions 3.x und .NET muss die `Microsoft.NET.Sdk.Functions` Erweiterung mindestens die Version `3.0.0` haben.

# <a name="version-2x"></a>[Version 2.x](#tab/v2)

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

# <a name="version-1x"></a>[Version 1.x](#tab/v1)

```xml
<TargetFramework>net472</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```
---

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Aktualisieren von Apps der Version 2.x auf die Version 3.x in Visual Studio

Sie können eine bereits vorhandene, für die Version 2.x konzipierte Funktion öffnen und zu 3.x migrieren, indem Sie die Datei `.csproj` bearbeiten und die obigen Werte aktualisieren.  Visual Studio verwaltet Runtimeversionen automatisch auf der Grundlage der Projektmetadaten.  Sollten Sie allerdings bislang noch keine App der Version 3.x erstellt haben, kann es sein, dass Visual Studio auf Ihrem Computer noch nicht über die Vorlagen und die Runtime für die Version 3.x verfügt.  In diesem Fall tritt ggf. ein Fehler wie der folgende auf: „Es ist keine Functions-Laufzeit verfügbar, die der im Projekt angegebenen Version entspricht.“  Führen Sie die Schritte zum Erstellen eines neuen Funktionsprojekts aus, um die neuesten Vorlagen und die Runtime abzurufen.  Wenn Sie zum Auswahlbildschirm für die Version und die Vorlage gelangen, warten Sie, bis Visual Studio die neuesten Vorlagen abgerufen hat. Wenn die neuesten .NET Core 3-Vorlagen verfügbar sind und angezeigt werden, können Sie jedes beliebige Projekt ausführen und debuggen, das für Version 3.x konfiguriert wurde.

> [!IMPORTANT]
> Funktionen der Version 3.x können nur in Visual Studio entwickelt werden, wenn Sie mindestens die Visual Studio Version 16.4 verwenden.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Visual Studio Code und Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) werden für die Entwicklung über die Befehlszeile und auch von der [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) für Visual Studio Code verwendet. Wenn Sie für die Version 3.x entwickeln möchten, müssen Sie die Core Tools-Version 3.x installieren. Bei der Entwicklung für die Version 2.x benötigen Sie die Core Tools-Version 2.x. Und so weiter. Weitere Informationen finden Sie unter [Installieren der Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Für die Visual Studio Code-Entwicklung müssen Sie möglicherweise auch die Benutzereinstellung für die `azureFunctions.projectRuntime` entsprechend der installierten Version der Tools aktualisieren.  Diese Einstellung aktualisiert auch die Vorlagen und Sprachen, die während der Erstellung von Funktions-Apps verwendet werden.  Wenn Sie Apps in `~3` erstellen möchten, müssen Sie die Benutzereinstellung `azureFunctions.projectRuntime` auf `~3`aktualisieren.

![Runtimeeinstellung der Azure Functions-Erweiterung](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven- und Java-Apps

Sie können Java-Apps der Version 2.x zur Version 3.x migrieren, indem Sie die [Version 3.x von Core Tools installieren](functions-run-local.md#install-the-azure-functions-core-tools), die für die lokale Ausführung benötigt wird.  Nachdem Sie sich vergewissert haben, dass Ihre lokal ausgeführte App unter der Version 3.x ordnungsgemäß funktioniert, können Sie die Datei `POM.xml` der App aktualisieren, um die Einstellung `FUNCTIONS_EXTENSION_VERSION` in `~3` zu ändern, wie im folgenden Beispiel zu sehen:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Bindungen

Ab Version 2.x verwendet die Runtime ein neues [Modell für die Erweiterbarkeit von Bindungen](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview), das folgende Vorteile bietet:

* Unterstützung für Bindungserweiterungen von Drittanbietern.

* Entkoppeln von Runtime und Bindungen. Mit dieser Änderung können Bindungserweiterungen versioniert und unabhängig freigegeben werden. Sie können z.B. ein Upgrade auf eine Version einer Erweiterung durchführen, das auf einer neueren Version des zugrunde liegenden SDKs basiert.

* Eine schlankere Ausführungsumgebung, in der nur die tatsächlich verwendeten Bindungen bekannt sind und von der Runtime geladen werden.

Mit Ausnahme von HTTP- und Timertriggern müssen alle Bindungen explizit zum Funktions-App-Projekt hinzugefügt oder im Portal registriert werden. Weitere Informationen finden Sie unter [Registrieren von Bindungserweiterungen](./functions-bindings-expressions-patterns.md).

Die folgende Tabelle zeigt, welche Bindungen in den einzelnen Runtimeversionen unterstützt werden.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Lokales Codieren und Testen von Azure Functions](functions-run-local.md)
* [Einstellen von Runtimeversionen von Azure Functions als Ziel](set-runtime-version.md)
* [Versionshinweise](https://github.com/Azure/azure-functions-host/releases)
