---
title: SDKs und REST-APIs für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über Azure Communication Services SDKs und REST APIs.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: baba53d797d3d530b7f71b7f87e01dd673e6a6cc
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966402"
---
# <a name="sdks-and-rest-apis"></a>SDKs und REST-APIs

Azure Communication Services-Funktionen sind vom Prinzip her in acht Bereiche unterteilt. Die meisten Bereichen weisen vollständig quelloffene SDKs auf, die gegen veröffentlichte REST-APIs programmiert sind, und die Sie direkt über das Internet nutzen können. Das Calling SDK verwendet proprietäre Netzwerkschnittstellen und ist Closed-Source.

In den folgenden Tabellen sind diese Bereiche und die Verfügbarkeit von REST-APIs und SDK-Bibliotheken zusammengefasst. Wir weisen auch darauf hin, ob APIs und SDKs für Endbenutzerclients oder vertrauenswürdige Dienstumgebungen bestimmt sind. APIs und SDKs wie SMS sollten in Umgebungen mit niedriger Vertrauenswürdigkeit nicht direkt von Endbenutzergeräten genutzt werden.

Die Entwicklung webbasierter Anruf- und Chatanwendungen kann durch [UI-Bibliotheken von Azure Communication Services](https://azure.github.io/communication-ui-library) beschleunigt werden. Die UI-Bibliothek stellt für die Produktion bereite UI-Komponenten zur Verfügung, die Sie in Ihre Anwendungen einfügen können.

## <a name="rest-apis"></a>REST-APIs
Communication Services-APIs sind unter [docs.microsoft.com](/rest/api/azure/) neben anderen Azure-REST-APIs dokumentiert. Diese Dokumentation erläutert die Strukturierung Ihrer HTTP-Nachrichten und bietet eine Anleitung zur Verwendung von Postman. Die Dokumentation zur REST-Schnittstelle wird auch im Swagger-Format auf [GitHub](https://github.com/Azure/azure-rest-api-specs) angeboten.


## <a name="sdks"></a>SDKs
| Assembly | Protokolle| Environment | Funktionen|
|--------|----------|---------|----------------------------------|
| Azure Resource Manager | [REST](/rest/api/communication/communicationservice)| Dienst| Dient zum Bereitstellen und Verwalten von Communication Services-Ressourcen|
| Allgemein | – | Client und Dienst | Stellt Basistypen für andere SDKs zur Verfügung |
| Identity | [REST](/rest/api/communication/communicationidentity) | Dienst| Verwalten von Benutzern, Zugriffstoken|
| Telefonnummern| [REST](/rest/api/communication/phonenumbers)| Dienst| Erfassen und Verwalten von Telefonnummern |
| sms| [REST](/rest/api/communication/sms) | Dienst| Dient zum Senden und Empfangen von SMS-Nachrichten|
| Chat | [REST](/rest/api/communication/) mit proprietärer Signalisierung | Client und Dienst | Dient zum Hinzufügen von Textchat in Echtzeit zu Ihren Anwendungen |
| Aufrufen| Proprietärer Transport | Client | Sprache, Video, Bildschirmfreigabe und andere Echtzeitkommunikation |
| Aufrufen des Servers | REST| Dienst| Tätigen und Verwalten von Anrufen, Wiedergabe von Audio und Konfigurieren der Aufzeichnung |
| Network Traversal| REST| Dienst| Zugreifen auf TURN-Server für Datentransporte auf niedriger Ebene |
| UI-Bibliothek | – | Client | Produktionsbereite UI-Komponenten für Chat- und Anruf-Apps |

### <a name="languages-and-publishing-locations"></a>Sprachen und Veröffentlichungsspeicherorte

Die Veröffentlichungsstandorte für die einzelnen SDK-Pakete sind unten aufgeführt.

| Bereich | JavaScript | .NET | Python | Java SE | iOS | Android | Andere|
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | [npm](https://www.npmjs.com/package/@azure/arm-communication) | [NuGet](https://www.NuGet.org/packages/Azure.ResourceManager.Communication)| [PyPi](https://pypi.org/project/azure-mgmt-communication/)| [Maven](https://search.maven.org/search?q=azure-resourcemanager-communication)| -| -| [Go über GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Allgemein | [npm](https://www.npmjs.com/package/@azure/communication-common) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Common/)| –| [Maven](https://search.maven.org/search?q=a:azure-communication-common) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common) | -|
| Identity | [npm](https://www.npmjs.com/package/@azure/communication-identity) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Identity)| [PyPi](https://pypi.org/project/azure-communication-identity/)| [Maven](https://search.maven.org/search?q=a:azure-communication-identity) | -| -| -|
| Telefonnummern | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.PhoneNumbers)| [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)| [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers) | -| -| -|
| Chat | [npm](https://www.npmjs.com/package/@azure/communication-chat)| [NuGet](https://www.NuGet.org/packages/Azure.Communication.Chat) | [PyPi](https://pypi.org/project/azure-communication-chat/) | [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | -|
| SMS| [npm](https://www.npmjs.com/package/@azure/communication-sms) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Sms)| [PyPi](https://pypi.org/project/azure-communication-sms/) | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms) | -| -| -|
| Aufrufen| [npm](https://www.npmjs.com/package/@azure/communication-calling) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Calling) | -| - | [GitHub](https://github.com/Azure/Communication/releases) | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)| -|
|Anrufautomatisierung||[NuGet](https://www.NuGet.org/packages/Azure.Communication.CallingServer/)||[Maven](https://search.maven.org/artifact/com.azure/azure-communication-callingserver)
|Network Traversal| [npm](https://www.npmjs.com/package/@azure/communication-network-traversal)|[NuGet](https://www.NuGet.org/packages/Azure.Communication.NetworkTraversal/)
| UI-Bibliothek| [npm](https://www.npmjs.com/package/@azure/communication-react) | - | - | - | - | - | [GitHub](https://github.com/Azure/communication-ui-library), [Storybook](https://azure.github.io/communication-ui-library/?path=/story/overview--page) |
| Referenzdokumentation | [docs](https://azure.github.io/azure-sdk-for-js/communication.html) | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)| -| [docs](http://azure.github.io/azure-sdk-for-java/communication.html) | [docs](/objectivec/communication-services/calling/)| [docs](/java/api/com.azure.android.communication.calling)| -|

Die Zuordnung zwischen Assemblyanzeigenamen und Namespaces lautet:

| Assembly | Namespaces |
|------------------------|--------------------------------------|
| Azure Resource Manager | Azure.ResourceManager.Communication|
| Allgemein | Azure.Communication.Common |
| Identity | Azure.Communication.Identity |
| Telefonnummern| Azure.Communication.PhoneNumbers |
| SMS| Azure.Communication.SMS|
| Chat | Azure.Communication.Chat |
| Aufrufen| Azure.Communication.Calling|
| Aufrufen des Servers | Azure.Communication.CallingServer|
| Network Traversal| Azure.Communication.NetworkTraversal |
| UI-Bibliothek | Azure.Communication.Calling|


## <a name="rest-api-throttles"></a>REST-API-Drosselungen
Bestimmte Rest-APIs und entsprechende SDK-Methoden haben Drosselungs-Limits, die Sie berücksichtigen sollten. Das Überschreiten dieser Drosselungsgrenzwerte löst eine `429 - Too Many Requests`-Fehlerantwort aus. Diese Grenzwerte können anhand [einer Anforderung an den Azure-Support](../../azure-portal/supportability/how-to-create-azure-support-request.md)erhöht werden.

| API| Drosselung|
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Alle Such-Telefonnummernplan-APIs](/rest/api/communication/phonenumbers) | 4 Anfragen/Tag|
| [Telefonnummer-Plan erwerben](/rest/api/communication/phonenumbers/purchasephonenumbers) | 1 Kauf pro Monat|
| [Senden einer SMS](/rest/api/communication/sms/send) | 200 Anfragen/Minute |


## <a name="sdk-platform-support-details"></a>Details zur SDK-Plattformunterstützung

### <a name="ios-and-android"></a>iOS und Android

- Die Communication Services iOS SDKs zielen auf iOS Version 13+, und Xcode 11+.
- Die Android Java SDKs zielen auf Android API Level 21+ und Android Studio 4.0+

### <a name="net"></a>.NET

Mit Ausnahme von Calling zielen die Communication Services-Pakete auf den .NET-Standard 2.0, der die unten aufgeführten Plattformen unterstützt.

Unterstützung über .NET Framework 4.6.1
- Windows 10, 8.1, 8 und 7
- Windows Server 2012 R2, 2012 und 2008 R2 SP1

Unterstützung über .NET Core 2.0:
- Windows 10 (ab 1607), 7 SP1+, 8.1
- Windows Server 2008 R2 SP1+
- Mac OS X ab 10.12
- Mehrere Versionen/Distributionen von Linux
- UWP 10.0.16299 (RS3) September 2017
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

Das Anrufpaket unterstützt UWP-Apps, die mit .NET Native oder C++/WinRT unter den folgenden Betriebssystemen erstellt werden:
- Windows 10 10.0.17763
- Windows Server 2019 10.0.17763

## <a name="api-stability-expectations"></a>Erwartungen an die API-Stabilität

> [!IMPORTANT]
> Dieser Abschnitt enthält Anleitungen zu REST-APIs und SDKs, die als **stabil** gekennzeichnet sind. APIs, die als Vorabversion, Vorschau oder Beta gekennzeichnet sind, können ohne Vorankündigung geändert oder als **veraltet eingestuft** werden.

In der Zukunft werden wir möglicherweise Versionen der SDKs für Kommunikationsdienste außer Betrieb nehmen, und wir könnten bahnbrechende Veränderungen an unseren REST-APIs und veröffentlichten SDKs vornehmen. Für Azure Communication Services gelten *allgemein* zwei Richtlinien zur Unterstützung des Zurückziehens von Dienstversionen:

- Sie werden mindestens drei Jahre vorher benachrichtigt, ehe Sie aufgrund einer Änderung einer Communication Services-Schnittstelle Code ändern müssen. Bei allen dokumentierten REST-APIs und SDK-APIs wird in der Regel eine mindestens dreijährige Vorwarnzeit eingehalten, bevor Schnittstellen ausgemustert werden.
- Sie werden mindestens ein Jahr, bevor Sie SDK-Assemblies auf die neueste Minor-Version aktualisieren müssen, benachrichtigt. Diese erforderlichen Aktualisierungen sollten keine Codeänderungen erfordern, da sie zur gleichen Hauptversion gehören. Die Verwendung des neuesten SDK ist besonders wichtig für die Bibliotheken „Calling“ und „Chat“, die über Echtzeitkomponenten verfügen, bei denen häufig Sicherheits- und Leistungsaktualisierungen erforderlich sind. Wir empfehlen Ihnen nachdrücklich, sämtliche Ihrer Communication Services-SDKs auf dem neuesten Stand zu halten.

### <a name="api-and-sdk-decommissioning-examples"></a>Beispiele für die Außerbetriebnahme von API und SDK

**Sie haben Version 24 der REST-API „SMS“ in Ihre Anwendung integriert. Azure Communication veröffentlicht Version 25.**

Sie erhalten eine dreijährige Vorwarnung, bevor diese APIs nicht mehr funktionieren und Sie gezwungen sind, auf v25 zu aktualisieren. Dieses Update erfordert möglicherweise eine Codeänderung.

**Sie haben die Version v2.02 des Calling SDK in Ihre Anwendung integriert. Azure Communication gibt die Version v2.05 frei.**

Es kann erforderlich sein, dass Sie innerhalb von 12 Monaten nach der Freigabe des v2.05 auf die Version v2.05 des Calling SDK aktualisieren müssen. Dabei sollte es sich um einen einfachen Austausch des Artefakts handeln, ohne dass eine Codeänderung erforderlich ist, da Version 2.05 zur Hauptversion 2 gehört und keine Breaking Changes aufweist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden SDK-Übersichten:

- [Calling SDK Übersicht](../concepts/voice-video-calling/calling-sdk-features.md)
- [Chat SDK Übersicht](../concepts/chat/sdk-features.md)
- [SMS SDK Übersicht](../concepts/telephony-sms/sdk-features.md)

Für den Einstieg in Azure Communication Services:

- [Erstellen Sie eine Azure Communication Services-Ressource](../quickstarts/create-communication-resource.md)
- Generieren von [Benutzerzugrifftoken](../quickstarts/access-tokens.md)
