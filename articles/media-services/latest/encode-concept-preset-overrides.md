---
title: Verwenden von voreingestellten Außerkraftsetzungen zum Ändern der Transformationseinstellungen bei der Auftragsübermittlung
description: In diesem Artikel erfahren Sie, wie Sie voreingestellte Außerkraftsetzungen verwenden, um Transformationseinstellungen pro Auftrag anzupassen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: johndeu
ms.custom: seodec18
ms.openlocfilehash: 3b748aa9e6b8d9f79572b05d10258839aabfa8f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700291"
---
# <a name="using-preset-overrides-to-control-per-job-settings"></a>Verwenden von voreingestellten Außerkraftsetzungen zum Steuern von Einstellungen pro Auftrag

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="transforms-and-jobs-overview"></a>Übersicht über Transformationen und Aufträge

Um mit Media Services v3 codieren zu können, müssen Sie eine [Transformation](/rest/api/media/transforms) und einen [Auftrag](/rest/api/media/jobs) erstellen. Die Transformation definiert eine Anweisung für die Codierungseinstellungen und -ausgaben. Der Auftrag ist eine Instanz der Anweisung. Weitere Informationen finden Sie unter [Transformationen und Aufträge](transform-jobs-concept.md).

Beim Codieren oder Verwenden von Analysen mit Media Services können Sie benutzerdefinierte Voreinstellungen in einer Transformation definieren, um zu definieren, welche Einstellungen verwendet werden sollen. Manchmal ist es erforderlich, die Einstellungen für eine Transformation pro Auftrag zu überschreiben, um zu vermeiden, dass für jedes Szenario eine benutzerdefinierte Transformation erstellt werden muss. Zum Überschreiben aller Einstellungen für Ihre Transformationsvoreinstellung können Sie die voreingestellte Override-Eigenschaft des [Auftragsausgabe-Assets](/dotnet/api/microsoft.azure.management.media.models.joboutputasset) verwenden, bevor Sie den Auftrag an die Transformation übermitteln.

## <a name="preset-overrides"></a>Voreingestellte Außerkraftsetzungen

Mit voreingestellten Außerkraftsetzungen können Sie eine benutzerdefinierte Voreinstellung übergeben, die die Einstellungen überschreibt, die für ein Transformationsobjekt nach der ersten Erstellung bereitgestellt wurden.  Diese Eigenschaft ist für das [Auftragsausgabe-Asset](/dotnet/api/microsoft.azure.management.media.models.joboutputasset) verfügbar, wenn ein neuer Auftrag an eine Transformation übermittelt wird.

Dies kann in Situationen nützlich sein, in denen Sie einige Eigenschaften Ihrer benutzerdefinierten Transformationen oder eine Eigenschaft für eine integrierte Voreinstellung überschreiben müssen. Betrachten Sie beispielsweise das Szenario, in dem Sie eine benutzerdefinierte Transformation erstellt haben, die die [integrierte Audioanalysevoreinstellung](/rest/api/media/transforms/create-or-update#audioanalyzerpreset) verwendet, diese Voreinstellung jedoch anfänglich so eingerichtet haben, dass die Audiospracheinstellung „en-us“ für Englisch verwendet wird.  Dies würde zu einer Transformation führen, bei der alle übermittelten Aufträge nur als US-Englisch an die Transkriptions-Engine der Spracherkennung gesendet werden. Jeder Auftrag, der an diese Transformation gesendet wird, wird für die Spracheinstellung „en-us“ gesperrt. Sie könnten dieses Szenario umgehen, indem Sie eine Transformation für jede Sprache definieren. Dies wäre jedoch viel schwieriger zu verwalten, und es könnte zu Einschränkungen des Transformationskontingents in Ihrem Konto kommen.
Als beste Lösung für dieses Szenario verwenden Sie eine voreingestellte Außerkraftsetzung für das Auftragsausgabe-Asset, bevor Sie den Auftrag an die Transformation übermitteln.  Anschließend können Sie eine einzelne Transformation für die Audiotranskription definieren und die erforderlichen Spracheinstellungen pro Auftrag übergeben.

Die voreingestellte Überschreibung bietet Ihnen eine Möglichkeit, eine neue benutzerdefinierte Voreinstellungsdefinition mit jedem Auftrag zu übergeben, der an die Transformation übermittelt wird. Diese Eigenschaft ist für die [Auftragsausgabeentität](/dotnet/api/microsoft.azure.management.media.models.joboutput) in allen SDK-Versionen verfügbar, die auf der API-Version 2021-06-01 basieren.

Weitere Informationen finden Sie in der REST-Dokumentation unter der Eigenschaft [presetOverride](https://github.com/Azure/azure-rest-api-specs/blob/ce90f9b45945c73b8f38649ee6ead390ff6efe7b/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L1960) für die Auftragsausgabeentität.

> [!NOTE]
> Sie können voreingestellte Außerkraftsetzungen nur verwenden, um die Einstellungen für eine definierte Voreinstellung in der Transformation zu überschreiben.  Sie können nicht von einer bestimmten Voreinstellung zu einem anderen Typ wechseln. Wenn Sie beispielsweise versuchen, eine Transformation zu überschreiben, die mit der integrierten inhaltsorientierten Codierungsvoreinstellung erstellt wurde, um eine andere Voreinstellung wie die Audioanalyse zu verwenden, würde dies zu einer Fehlermeldung führen.


## <a name="example-of-preset-override-in-net"></a>Beispiel für voreingestellte Überschreibung in .NET

Ein vollständiges Beispiel für die Verwendung des .NET SDK für Media Services, das zeigt, wie die voreingestellte Überschreibung mit einer grundlegenden Audioanalysetransformation verwendet wird, ist in GitHub verfügbar.
Weitere Informationen zur Verwendung der voreingestellten Override-Eigenschaft der Auftragsausgabe finden Sie im Beispiel [Analysieren einer Mediendatei mit einer voreingestellten Audioanalyse](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer).

## <a name="sample-code-of-preset-override-in-net"></a>Beispielcode für voreingestellte Überschreibung in .NET

[!code-csharp[Main](../../../media-services-v3-dotnet/AudioAnalytics/AudioAnalyzer/program.cs#PresetOverride)]

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Hochladen, Codieren und Streamen mit Media Services](stream-files-tutorial-with-api.md)
* [Codieren aus einer HTTPS-URL mithilfe von integrierten Voreinstellungen](job-input-from-http-how-to.md)
* [Codieren einer lokalen Datei mithilfe von integrierten Voreinstellungen](job-input-from-local-file-how-to.md)
* [Entwickeln einer benutzerdefinierten Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen](transform-custom-presets-how-to.md)
