---
title: Azure Digital Twins-APIs und -SDKs
titleSuffix: Azure Digital Twins
description: Erfahren Sie mehr über Azure Digital Twins API- und SDK-Optionen, einschließlich Informationen zu SDK-Hilfsklassen und allgemeinen Nutzungshinweisen.
author: baanders
ms.author: baanders
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 1fcbeeb532e813535cc8f3adcf02b2c2e990287e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500201"
---
# <a name="azure-digital-twins-apis-and-sdks"></a>Azure Digital Twins-APIs und -SDKs

Dieser Artikel bietet eine Übersicht über die verfügbaren Azure Digital Twins APIs und die Methoden zur Interaktion mit ihnen. Sie können die REST-APIs entweder direkt mit den zugehörigen Swagger-Dateien (über ein Tool wie [Postman](how-to-use-postman.md)) oder über ein SDK verwenden.

Azure Digital Twins ist sowohl mit **Steuerungsebenen-APIs** als auch mit **Datenebenen-APIs** und **SDKs** zur Verwaltung Ihrer Instanz und ihrer Elemente ausgestattet. 
* Die Steuerungsebenen-APIs sind [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md)-APIs, die Vorgänge zur Ressourcenverwaltung wie das Erstellen und Löschen von Instanzen abdecken. 
* Bei den Datenebenen-APIs handelt es sich um Azure Digital Twins-APIs, die für Vorgänge zur Datenverwaltung wie die Verwaltung von Modellen, Zwillingen und des Graphen verwendet werden.
* Die SDKs nutzen die bestehenden APIs, um die Entwicklung von benutzerdefinierten Anwendungen unter Verwendung von Azure Digital Twins zu erleichtern. Die SDKs für die Steuerebene sind in [.NET (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true) und [Java](/java/api/overview/azure/digitaltwins/resourcemanagement?view=azure-java-stable&preserve-view=true) verfügbar, und die SDKs für die Datenebene sind in [.NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), [Java](/java/api/overview/azure/digitaltwins/client?view=azure-java-stable&preserve-view=true), [JavaScript](/javascript/api/@azure/digital-twins-core/?view=azure-node-latest&preserve-view=true) und [Python](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true) verfügbar.

## <a name="overview-control-plane-apis"></a>Übersicht: Steuerungsebenen-APIs

Bei den Steuerungsebenen-APIs handelt es sich um [ARM](../azure-resource-manager/management/overview.md)-APIs, die verwendet werden, um Ihre Azure Digital Twins-Instanz als Ganzes zu verwalten, sodass sie Vorgänge wie das Erstellen oder Löschen Ihrer gesamten Instanz abdecken. Sie werden diese APIs auch zum Erstellen und Löschen von Endpunkten verwenden.

Die aktuellste Version der Steuerungsebenen-API ist _**2020-12-01**_.

So verwenden Sie die Steuerungsebenen-APIs
* Sie können die APIs direkt aufrufen, indem Sie auf den neuesten Swagger-Ordner im [Swagger-Repository der Steuerungsebene](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable) verweisen. Dieser Ordner enthält auch einen Ordner mit Beispielen, die die Verwendung veranschaulichen.
* Sie können derzeit auf SDKs für Steuerungs-APIs in Folgendem zugreifen...
  - [.NET (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([Referenz [automatisch generiert]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([Quelle](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [Java](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([Referenz [automatisch generiert]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([Quelle](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([Quelle](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([Quelle](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([Quelle](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

Sie können auch Steuerungsebenen-APIs anwenden, indem Sie mit Azure Digital Twins über das [Azure-Portal](https://portal.azure.com) und die [CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) interagieren.

## <a name="overview-data-plane-apis"></a>Übersicht: Datenebenen-APIs

Bei den Datenebenen-APIs handelt es sich um die Azure Digital Twins-APIs, die verwendet werden, um die Elemente innerhalb Ihrer Azure Digital Twins-Instanz zu verwalten. Dazu gehören Vorgänge wie das Erstellen von Routen, das Hochladen von Modellen, das Erstellen von Beziehungen und das Verwalten von Zwillingen. Sie können ganz allgemein in die folgenden Kategorien unterteilt werden:
* **DigitalTwinModels** – Die Kategorie „DigitalTwinModels“ enthält APIs zur Verwaltung der [Modelle](concepts-models.md) in einer Azure Digital Twins-Instanz. Zu den Verwaltungsaktivitäten gehören das Hochladen, Überprüfen, Abrufen und Löschen von Modellen, die in DTDL erstellt wurden.
* **DigitalTwins** – Die Kategorie „DigitalTwins“ enthält die APIs, mit denen Entwickler [digitale Zwillinge](concepts-twins-graph.md) und ihre Beziehungen in einer Azure Digital Twins-Instanz erstellen, ändern und löschen können.
* **Query** – Die Kategorie „Query“ ermöglicht es Entwicklern, [Gruppen von digitalen Zwillingen im Zwillingsgraphen](how-to-query-graph.md) beziehungsübergreifend zu finden.
* **Event Routes** – Die Kategorie „Event Routes“ enthält APIs zur [Weiterleitung von Daten](concepts-route-events.md), durch das System und zu Downstreamdiensten.

Die aktuellste Datenebenen-API-Version ist _**2020-10-31**_.

So verwenden Sie die Datenebenen-APIs
* Sie können die APIs direkt aufrufen, indem Sie wie folgt vorgehen:
   - Verweis auf den aktuellen Swagger-Ordner im [Swagger-Repository der Datenebene](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Dieser Ordner enthält auch einen Ordner mit Beispielen, die die Verwendung veranschaulichen. 
   - Zeigen Sie die [API-Referenzdokumentation](/rest/api/azure-digitaltwins/) an.
* Sie können das **.NET (C#) SDK** verwenden. So verwenden Sie das .NET SDK
   - Sie können das Paket über NuGet anzeigen und hinzufügen: [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Sie können die [SDK-Referenzdokumentation](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) anzeigen.
   - Sie können die SDK-Quelle, einschließlich eines Ordners mit Beispielen, in GitHub: [Azure IoT Digital Twins-Clientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) finden. 
   - Ausführliche Informationen und Anwendungsbeispiele finden Sie im Abschnitt [.NET (C#) SDK (Datenebene)](#net-c-sdk-data-plane) dieses Artikels.
* Sie können das **Java SDK** verwenden. Zum Verwenden des Java-SDK...
   - Sie können das Paket über Maven anzeigen und installieren: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - Sie können die [SDK-Referenzdokumentation](/java/api/overview/azure/digitaltwins/client?view=azure-java-stable&preserve-view=true) anzeigen
   - Sie können die SDK-Quelle in GitHub finden: [Azure IoT Digital Twins-Clientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Sie können das **JavaScript SDK** verwenden. So verwenden Sie das JavaScript SDK
   - Sie können das Paket über npm anzeigen und installieren: [Azure Digital Twins-Clientbibliothek für JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - Sie können die [SDK-Referenzdokumentation](/javascript/api/@azure/digital-twins-core/?view=azure-node-latest&preserve-view=true) anzeigen.
   - Sie können die SDK-Quelle in GitHub finden: [Azure IoT Digital Twins-Clientbibliothek für Java](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Sie können das **Python SDK** verwenden. Zum Verwenden des Python SDK...
   - Sie können das Paket von PyPi aus anzeigen und installieren: [Azure Digital Twins Core Client-Bibliothek für Python](https://pypi.org/project/azure-digitaltwins-core/).
   - Sie können die [SDK-Referenzdokumentation](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true) anzeigen.
   - Sie können die SDK-Quelle in GitHub finden: [Azure IoT Digital Twins-Clientbibliothek für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)

Sie können auch Datenebenen-APIs anwenden, indem Sie mit Azure Digital Twins über die [CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) interagieren.

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (Datenebene)

Das Azure Digital Twins .NET (C#) SDK ist Teil des Azure SDK für .NET. Es ist Open Source und basiert auf den APIs der Azure Digital Twins-Datenebene.

> [!NOTE]
> Weitere Informationen zum SDK-Entwurf finden Sie in den allgemeinen [Entwurfsprinzipien für Azure SDKs](https://azure.github.io/azure-sdk/general_introduction.html) und in den spezifischen [.NET-Entwurfsrichtlinien](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Um das SDK zu verwenden, fügen Sie Ihrem Projekt das NuGet-Paket **Azure.DigitalTwins.Core** hinzu. Sie benötigen auch die aktuelle Version des Pakets **Azure.Identity**. In Visual Studio können Sie diese Pakete mit dem NuGet-Paket-Manager hinzufügen (Zugriff über *Extras > NuGet-Paket-Manager > NuGet-Pakete für Lösung verwalten*). Sie können auch das .NET-Befehlszeilentool mit den Befehlen verwenden, die Sie unter den Links zu den NuGet-Paketen unten finden, um sie Ihrem Projekt hinzuzufügen:
* [Azure.DigitalTwins.Core:](https://www.nuget.org/packages/Azure.DigitalTwins.Core) Dies ist das Paket für das [Azure Digital Twins SDK für .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 
* [Azure.Identity:](https://www.nuget.org/packages/Azure.Identity) Diese Bibliothek stellt Tools bereit, die die Authentifizierung bei Azure unterstützen.

Eine ausführliche exemplarische Vorgehensweise zur Verwendung der APIs in der Praxis finden Sie unter [Codieren einer Client-App](tutorial-code.md). 

### <a name="serialization-helpers"></a>Hilfsprogramme für die Serialisierung

Serialisierungshilfen sind im SDK verfügbare Hilfsfunktionen zum schnellen Erstellen oder Deserialisieren von Zwillingsdaten für den Zugriff auf grundlegende Informationen. Da die Kernmethoden des SDK Zwillingsdaten standardmäßig als JSON zurückgeben, kann es hilfreich sein, die Zwillingsdaten mithilfe dieser Hilfsklassen weiter zu unterteilen.

Die folgenden Hilfsklassen sind verfügbar:
* `BasicDigitalTwin`: Stellt generisch die Kerndaten eines digitalen Zwillings dar.
* `BasicDigitalTwinComponent`: Stellt generisch eine Komponente in den Eigenschaften vom Typ `Contents` eines grundlegenden digitalen Zwillings (`BasicDigitalTwin`) dar.
* `BasicRelationship`: Stellt generisch die Kerndaten einer Beziehung dar.
* `DigitalTwinsJsonPropertyName`: Enthält die Zeichenfolgenkonstanten für die Verwendung bei der JSON-Serialisierung und -Deserialisierung für benutzerdefinierte Arten von digitalen Zwillingen.

## <a name="general-apisdk-usage-notes"></a>Allgemeine Hinweise zur API/SDK-Verwendung

> [!NOTE]
> Beachten Sie, dass **Cross-Origin Resource Sharing (CORS)** in Azure Digital Twins derzeit nicht unterstützt wird. Weitere Informationen zu den Auswirkungen und Lösungsstrategien finden Sie im Abschnitt [Cross-Origin Resource Sharing (CORS)](concepts-security.md#cross-origin-resource-sharing-cors)  unter *Konzepte: Sicherheit für Azure Digital Twins-Lösungen*.

Die folgende Liste enthält zusätzliche Details und allgemeine Leitfäden für die Verwendung der APIs und SDKs.

* Sie können ein HTTP REST-Testtool wie Postman verwenden, um direkte Aufrufe an die APIs von Azure Digital Twins durchzuführen. Weitere Informationen zu diesem Prozess finden Sie unter [Senden von API-Anforderungen mit Postman](how-to-use-postman.md).
* Instanziieren Sie die Klasse `DigitalTwinsClient`, um das SDK zu verwenden. Der Konstruktor erfordert Anmeldeinformationen, die mit verschiedenen Authentifizierungsmethoden im Paket `Azure.Identity` abgerufen werden können. Weitere Informationen zu `Azure.Identity` finden Sie in der [Dokumentation zu Namespaces](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true). 
* Möglicherweise finden Sie `InteractiveBrowserCredential` zu Beginn nützlich, aber es gibt noch verschiedene andere Optionen, einschließlich Anmeldeinformationen für die [verwaltete Identität](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true), die Sie wahrscheinlich zur Authentifizierung von [mit MSI eingerichteten Azure-Funktionen](../app-service/overview-managed-identity.md?tabs=dotnet) für Azure Digital Twins verwenden werden. Weitere Informationen über `InteractiveBrowserCredential` finden Sie in dessen [Klassendokumentation](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true).
* Für Anforderungen an die Azure Digital Twins-APIs wird ein Benutzer oder Dienstprinzipal benötigt, der Teil desselben [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)-Mandanten (Azure AD) ist, in dem sich auch die Azure Digital Twins-Instanz befindet. Um böswillige Zugriffe auf Azure Digital Twins-Endpunkte zu verhindern, wird für Anforderungen mit Zugriffstoken, die nicht aus dem ursprünglichen Mandanten stammen, die Fehlermeldung „404: Unterdomäne nicht gefunden“ zurückgegeben. Dieser Fehler wird *selbst dann* zurückgegeben, wenn dem Benutzer oder Dienstprinzipal über die [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md)-Zusammenarbeit die Rolle „Azure Digital Twins-Datenbesitzer“ oder „Leseberechtigter für Azure Digital Twins-Daten“ gewährt wurde. Informationen zum Konfigurieren des mandantenübergreifenden Zugriffs finden Sie unter [Schreiben von App-Authentifizierungscode](how-to-authenticate-client.md#authenticate-across-tenants).
* Alle Aufrufe von Dienst-APIs werden als Memberfunktionen für die `DigitalTwinsClient`-Klasse verfügbar gemacht.
* Alle Dienstfunktionen sind in synchroner und asynchroner Version vorhanden.
* Alle Dienstfunktionen lösen eine Ausnahme für einen beliebigen Rückgabestatus von 400 oder höher aus. Stellen Sie sicher, dass Sie Aufrufe in einem `try`-Abschnitt umschließen und mindestens `RequestFailedExceptions` erfassen. Weitere Informationen zu dieser Art von Ausnahme finden Sie in der zugehörigen [Referenzdokumentation](/dotnet/api/azure.requestfailedexception?view=azure-dotnet&preserve-view=true).
* Die meisten Dienstmethoden geben `Response<T>` (oder `Task<Response<T>>` für die asynchronen Aufrufe) zurück, wobei `T` die Klasse des Rückgabeobjekts für den Dienstaufruf darstellt. Die Klasse [Antwort](/dotnet/api/azure.response-1?view=azure-dotnet&preserve-view=true) (response) kapselt die Dienstrückgabe und präsentiert Rückgabewerte in ihrem `Value`-Feld.  
* Dienstmethoden mit ausgelagerten Ergebnissen geben `Pageable<T>` oder `AsyncPageable<T>` als Ergebnis zurück. Weitere Informationen zur `Pageable<T>`-Klasse finden Sie in der zugehörigen [Referenzdokumentation](/dotnet/api/azure.pageable-1?view=azure-dotnet&preserve-view=true). Weitere Informationen zu `AsyncPageable<T>` finden Sie in der zugehörigen [Referenzdokumentation](/dotnet/api/azure.asyncpageable-1?view=azure-dotnet&preserve-view=true).
* Mithilfe einer `await foreach`-Schleife können Sie ausgelagerte Ergebnisse durchlaufen. Weitere Informationen zu diesem Prozess finden Sie in der [relevanten Dokumentation](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Das zugrunde liegende SDK ist `Azure.Core`. In der [Dokumentation zu Azure-Namespaces](/dotnet/api/azure?view=azure-dotnet&preserve-view=true) finden Sie Verweise auf die SDK-Infrastruktur und -Typen.


Dienstmethoden geben, wo immer möglich, stark typisierte Objekte zurück. Da Azure Digital Twins jedoch auf Modellen basiert, die vom Benutzer zur Laufzeit individuell konfiguriert werden (über DTDL-Modelle, die in den Dienst hochgeladen werden), verwenden viele Dienst-APIs das JSON-Format, um Zwillingsdaten zu übernehmen und zurückzugeben.

## <a name="monitor-api-metrics"></a>Überwachen von API-Metriken

API-Metriken wie Anforderungen, Wartezeit und Ausfallrate können im [Azure-Portal](https://portal.azure.com/) angezeigt werden. 

Suchen Sie auf der Startseite des Portals nach Ihrer Azure Digital Twins-Instanz, um die Details abzurufen. Wählen Sie die Option **Metrics** aus dem Menü der Azure Digital Twins-Instanz aus, um die Seite *Metriken* aufzurufen.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot der Seite „Metriken“ für Azure Digital Twins":::

Von hier aus können Sie die Metriken für Ihre Instanz anzeigen und benutzerdefinierte Ansichten erstellen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Ausführen direkter Anforderungen an die APIs mithilfe von Postman:
* [Senden von API-Anforderungen mit Postman](how-to-use-postman.md)

Verwenden Sie alternativ das .NET SDK, indem Sie eine Client-App mithilfe dieses Tutorials erstellen:
* [Codieren einer Client-App](tutorial-code.md)
