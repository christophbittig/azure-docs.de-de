---
title: Planen Ihrer App – QnA Maker
description: Erfahren Sie, wie Sie Ihre QnA Maker-App planen. Erfahren Sie, wie QnA Maker funktioniert und mit anderen Azure-Diensten und einigen Konzepten der Wissensdatenbank interagiert.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b5810d7d69322793afea76bfbe29fb49b5dc13a3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086771"
---
# <a name="plan-your-qna-maker-app"></a>Planen Ihrer QnA Maker-App

Zum Planen Ihrer QnA Maker-App müssen Sie verstehen, wie QnA Maker funktioniert und mit anderen Azure-Diensten interagiert. Sie sollten auch ein fundiertes Verständnis von Konzepten der Wissensdatenbank haben.

## <a name="azure-resources"></a>Azure-Ressourcen

Jede [Azure-Ressource](azure-resources.md#resource-purposes), die mit QnA Maker erstellt wird, hat einen bestimmten Zweck. Jede Ressource hat ihren eigenen Zweck, ihre eigenen Grenzwerte und ihren eigenen [Tarif](azure-resources.md#pricing-tier-considerations). Es ist wichtig, die Funktion dieser Ressourcen zu verstehen, sodass Sie dieses Wissen in Ihren Planungsprozess integrieren können.

| Resource | Zweck |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource)-Ressource | Erstellung und Abfragevorhersage |
| [Cognitive Search](azure-resources.md#cognitive-search-resource)-Ressource | Datenspeicherung und -suche |
| [App Service-Ressource und App Plan Service](azure-resources.md#app-service-and-app-service-plan)-Ressource | Abfragevorhersage-Endpunkt |
| [Application Insights](azure-resources.md#application-insights)-Ressource | Abfragevorhersagetelemetrie |

### <a name="resource-planning"></a>Ressourcenplanung

Der Free-Tarif, `F0`, der einzelnen Ressourcen funktioniert und kann sowohl die Benutzeroberfläche für die Erstellung als auch die Abfragevorhersage bereitstellen. Sie können diesen Tarif verwenden, um die Erstellung und Abfragevorhersage zu erlernen. Wenn Sie zu einem Produktions- oder Liveszenario wechseln, sollten Sie Ihre Ressourcenauswahl neu auswerten.

### <a name="knowledge-base-size-and-throughput"></a>Größe und Durchsatz der Wissensdatenbank

Wenn Sie eine echte App erstellen, planen Sie ausreichend Ressourcen für die Größe Ihrer Wissensdatenbank und die erwarteten Anforderungen an die Abfragevorhersage ein.

Die Größe einer Wissensdatenbank wird gesteuert durch:
* Tarifgrenzwerte der [Cognitive Search-Ressource](../../../search/search-limits-quotas-capacity.md)
* [QnA Maker-Grenzwerte](../limits.md)

Die Abfragevorhersageanforderung der Wissensdatenbank wird durch den Web-App-Plan und die Web-App gesteuert. Informationen zum Planen Ihres Tarifs finden Sie unter [Empfohlene Einstellungen](azure-resources.md#recommended-settings).

### <a name="resource-sharing"></a>Gemeinsame Nutzung von Ressourcen

Wenn Sie bereits einige dieser Ressourcen verwenden, können Sie die Freigabe von Ressourcen in Erwägung ziehen. Schauen Sie, welche Ressourcen [gemeinsam genutzt](azure-resources.md#share-services-with-qna-maker) werden können, wobei hierbei das Verständnis zugrunde liegt, dass die gemeinsame Nutzung von Ressourcen ein fortgeschrittenes Szenario ist.

Alle Wissensdatenbanken, die in derselben QnA Maker-Ressource erstellt wurden, verwenden denselben **Test**-Abfragevorhersage-Endpunkt.

### <a name="understand-the-impact-of-resource-selection"></a>Grundlegendes zu den Auswirkungen der Ressourcenauswahl

Die richtige Ressourcenauswahl bedeutet, dass Ihre Wissensdatenbank die Abfragevorhersagen erfolgreich beantwortet.

Wenn Ihre Wissensdatenbank nicht ordnungsgemäß funktioniert, liegt das Problem in der Regel an einer nicht ordnungsgemäßen Ressourcenverwaltung.

Bei nicht ordnungsgemäßer Ressourcenauswahl müssen Sie untersuchen, welche [Ressource geändert werden muss](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Wissensdatenbanken

Eine Wissensdatenbank ist direkt an die QnA Maker-Ressource gebunden. Sie enthält die Frage-Antwort-Paare (QnA), die zum Beantworten von Abfragevorhersageanforderungen verwendet werden.

### <a name="language-considerations"></a>Sprachbezogene Überlegungen

Die erste in Ihrer QnA Maker-Ressource erstellte Wissensdatenbank legt die Sprache für die Ressource fest. Sie können nur eine Sprache für eine QnA Maker-Ressource festlegen.

Sie können Ihre QnA Maker-Ressourcen nach Sprache strukturieren oder [Translator](../../translator/translator-overview.md) verwenden, um eine Abfrage von einer anderen Sprache in die Sprache der Wissensdatenbank zu übersetzen, bevor Sie die Abfrage an den Abfragevorhersage-Endpunkt senden.

### <a name="ingest-data-sources"></a>Erfassen von Datenquellen

Sie können eine der folgenden erfassten [Datenquellen](../Concepts/data-sources-and-content.md) verwenden, um eine Wissensdatenbank zu erstellen:

* Öffentliche URL
* Private SharePoint-URL
* Datei

Beim Erfassungsprozess werden [unterstützte Inhaltstypen](../reference-document-format-guidelines.md) in Markdown konvertiert. Die weitere Bearbeitung der *Antwort* erfolgt mit Markdown. Nachdem Sie eine Wissensdatenbank erstellt haben, können Sie [QnA-Paare](question-answer-set.md) im QnA Maker-Portal mit [Rich Text Authoring](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer) bearbeiten.

### <a name="data-format-considerations"></a>Überlegungen zum Datenformat

Da das endgültige Format eines QnA-Paars Markdown ist, müssen Sie die [Markdown-Unterstützung](../reference-markdown-format.md) kennen.

Verknüpfte Bilder müssen über eine öffentliche URL verfügbar sein, damit sie im Testfenster des QnA Maker-Portals oder in einer Clientanwendung angezeigt werden können. QnA Maker bietet keine Authentifizierung für Inhalte, einschließlich Bilder.

### <a name="bot-personality"></a>Botpersönlichkeit

Fügen Sie Ihrer Wissensdatenbank mit [Geplauder](../how-to/chit-chat-knowledge-base.md) eine Botpersönlichkeit hinzu. Diese Persönlichkeit bietet vorgefertigte Antworten, die in einem bestimmten Konversationston bereitgestellt werden, z. B. *geschäftlich* und *freundlich*. Dieses Geplauder wird als Konversationsgruppe bereitgestellt, die Sie die mit Hinzufügen, Bearbeiten und Entfernen vollständig kontrollieren können.

Eine Botpersönlichkeit sollten Sie verwenden, wenn Ihr Bot mit Ihrer Wissensdatenbank verbunden ist. Sie können sich dafür entscheiden, Smalltalk in Ihrer Wissensdatenbank zu verwenden, auch wenn Sie gleichzeitig eine Verbindung mit anderen Diensten herstellen, aber Sie sollten überprüfen, wie der Botdienst interagiert, um zu wissen, ob dies der richtige Architekturentwurf für Ihre Nutzung ist.

### <a name="conversation-flow-with-a-knowledge-base"></a>Konversationsfluss mit einer Wissensdatenbank

Der Konversationsfluss beginnt in der Regel mit einer Begrüßung eines Benutzers, z. B. `Hi` oder `Hello`. Ihre Wissensdatenbank kann mit einer allgemeinen Antwort (z. B. `Hi, how can I help you`) antworten, und Sie kann auch eine Auswahl von Folgeaufforderungen zum Fortsetzen der Konversation bereitstellen.

Sie sollten beim Entwurf Ihres Konversationsflusses an eine Schleife denken, damit ein Benutzer weiß, wie er Ihren Bot verwenden kann, und nicht vom Bot in der Konversation stehen gelassen wird. [Folgeaufforderungen](../how-to/multiturn-conversation.md) bieten die Verknüpfung zwischen QnA-Paaren, die den Konversationsfluss ermöglichen.

### <a name="authoring-with-collaborators"></a>Erstellung mit Projektmitarbeitern

Projektmitarbeiter sind möglicherweise andere Entwickler, für die der vollständige Entwicklungsstapel der Wissensdatenbankanwendung freigeben ist, oder die nur auf die Erstellung der Wissensdatenbank beschränkt sind.

Die Erstellung der Wissensdatenbank unterstützt mehrere rollenbasierte Zugriffsberechtigungen, die Sie im Azure-Portal anwenden, um den Handlungsspielraum eines Projektmitarbeiters einzuschränken.

## <a name="integration-with-client-applications"></a>Integration in Clientanwendungen

Integration in Clientanwendungen wird durch Senden einer Abfrage an den Vorhersage-Runtimeendpunkt erreicht. Eine Abfrage wird mit einem SDK oder einer REST-basierten Anforderung an den Web-App-Endpunkt Ihres QnA Maker an Ihre bestimmte Wissensdatenbank gesendet.

Damit eine Clientanforderung ordnungsgemäß authentifiziert werden kann, muss die Clientanwendung die richtigen Anmeldeinformationen und die ID der Wissensdatenbank senden. Wenn Sie einen Azure Bot Service verwenden, konfigurieren Sie diese Einstellungen als Teil der Botkonfiguration im Azure-Portal.

### <a name="conversation-flow-in-a-client-application"></a>Konversationsfluss in einer Clientanwendung

Der Konversationsfluss in einer Clientanwendungs wie einem Azure-Bot erfordert möglicherweise vor und nach der Interaktion mit der Wissensdatenbank Funktionalität.

Unterstützt Ihre Clientanwendung den Konversationsfluss, entweder durch die Bereitstellung alternativer Mittel zur Bearbeitung von Folgeaufforderungen oder durch Smalltalk? Sofern dies der Fall ist, entwerfen Sie diese frühzeitig und stellen Sie sicher, dass die Abfrage der Clientanwendung von einem anderen Dienst oder bei der Übermittlung an Ihre Wissensdatenbank ordnungsgemäß bearbeitet wird.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Disponieren zwischen QnA Maker und Language Understanding (LUIS)

Eine Clientanwendung kann mehrere Features bereitstellen, von denen nur eines durch eine Wissensdatenbank beantwortet wird. Andere Features müssten weiterhin den Konversationstext verstehen und die Bedeutung daraus extrahieren.

Eine gängige Clientanwendungsarchitektur ist die gemeinsame Verwendung von QnA Maker und [Language Understanding (Luis)](../../LUIS/what-is-luis.md). LUIS bietet die Textklassifizierung und -extraktion für beliebige Abfragen, auch für andere Dienste. QnA Maker stellt Antworten aus Ihrer Wissensdatenbank bereit.

In einem solchen Szenario mit [gemeinsam genutzter Architektur](../choose-natural-language-processing-service.md) wird das Disponieren zwischen den beiden Diensten durch das [Dispatch](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)-Tool von Bot Framework durchgeführt.

### <a name="active-learning-from-a-client-application"></a>Aktives Lernen über eine Clientanwendung

QnA Maker nutzt _aktives Lernen_, um Ihre Wissensdatenbank durch Vorschlagen alternativer Fragen zu einer Antwort zu verbessern. Die Clientanwendung ist für einen Teil dieses [aktiven Lernens](../How-To/use-active-learning.md) verantwortlich. Die Clientanwendung kann anhand von Konversationseingabeaufforderungen feststellen, dass die Wissensdatenbank eine Antwort zurückgegeben hat, die für den Benutzer nicht hilfreich ist, und sie kann eine bessere Antwort ermitteln. Die Clientanwendung muss diese Informationen an die Wissensdatenbank zurücksenden, um die Vorhersagequalität zu verbessern.

### <a name="providing-a-default-answer"></a>Bereitstellen einer Standardantwort

Wenn Ihre Wissensdatenbank keine Antwort findet, wird die _Standardantwort_ zurückgegeben. Diese Antwort kann auf der Seite **Einstellungen** im QnA Maker-Portal oder in den [APIs](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body) konfiguriert werden.

Diese Standardantwort unterscheidet sich von der Standardantwort des Azure-Bots. Sie konfigurieren die Standardantwort für Ihren Azure-Bot im Azure-Portal als Teil der Konfigurationseinstellungen. Sie wird zurückgegeben, wenn der Schwellenwert für den Score nicht erreicht wird.

## <a name="prediction"></a>Vorhersage

Die Vorhersage ist die Antwort aus Ihrer Wissensdatenbank und enthält mehr Informationen als nur die Antwort. Um eine Abfragevorhersageantwort zu erhalten, verwenden Sie die [GenerateAnswer-API](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Fluktuationen des Vorhersageergebnisses

Ein Ergebnis kann sich auf der Grundlage verschiedener Faktoren ändern:

* Anzahl der Antworten, die Sie als Reaktion auf [GenerateAnswer](query-knowledge-base.md) mit `top`-Eigenschaft angefordert haben
* Vielzahl verfügbarer alternativer Fragen
* Filtern nach Metadaten
* An `test` oder `production` Wissensdatenbank gesendete Abfrage

Es gibt eine [zweiphasige Antwortrangfolge](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
- Cognitive Search – erster Rang. Legen Sie die Anzahl der _zulässigen Antworten_ so hoch fest, dass die besten Antworten von Cognitive Search zurückgegeben und dann an die Rangfolgefunktion von QnA Maker übergeben werden.
- QnA Maker – zweiter Rang. Ermitteln der besten Antwort durch Featurisierung und Machine Learning.

### <a name="service-updates"></a>Dienstupdates

Wenden Sie die [aktuellsten Runtimeupdates](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) an, um Dienstupdates automatisch zu verwalten.

### <a name="scaling-throughput-and-resiliency"></a>Skalierung, Durchsatz und Resilienz

Skalierung, Durchsatz und Resilienz werden durch die [Azure-Ressourcen](../how-to/set-up-qnamaker-service-azure.md), ihre Tarife und alle umgebenden Architekturen, wie [Traffic Manager](../how-to/configure-QnA-Maker-resources.md#business-continuity-with-traffic-manager) bestimmt.

### <a name="analytics-with-application-insights"></a>Analyse mit Application Insights

Alle Abfragen Ihrer Wissensdatenbank werden in Application Insights gespeichert. Verwenden Sie unsere [wichtigsten Abfragen](../how-to/get-analytics-knowledge-base.md), um Ihre Metriken zu verstehen.

## <a name="development-lifecycle"></a>Lebenszyklus der Entwicklung

Der [Entwicklungslebenszyklus](development-lifecycle-knowledge-base.md) einer Wissensdatenbank ist fortlaufend: Bearbeiten, Testen und Veröffentlichen Ihrer Wissensdatenbank.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Entwicklung von QnA Maker-Paaren in der Wissensdatenbank

Ihre QnA-Paare sollten auf Basis der Nutzung Ihrer Clientanwendung entworfen und entwickelt werden.

Jedes Paar kann Folgendes enthalten:
* Metadaten – Bei der Abfrage filterbar, damit Sie Ihre QnA-Paare mit zusätzlichen Informationen über Quelle, Inhalt, Format und Zweck Ihrer Daten versehen können.
* Folgeaufforderungen – Helfen beim Bahnen eines Pfads durch Ihre Wissensdatenbank, damit der Benutzer die richtige Antwort findet.
* Alternative Fragen – Wichtig, damit die Suche mit Ihrer Antwort aus verschiedenen Formen der Frage übereinstimmen kann. [Vorschläge für aktives Lernen](../How-To/use-active-learning.md) werden in alternative Fragen umgewandelt.

### <a name="devops-development"></a>DevOps-Entwicklung

Zum Entwickeln einer Wissensdatenbank, die in eine DevOps-Pipeline eingefügt werden soll, muss die Wissensdatenbank in Batchtests isoliert sein.

Eine Wissensdatenbank teilt den Cognitive Search-Index mit allen anderen Wissensdatenbanken der QnA Maker-Ressource. Obwohl die Wissensdatenbank durch Partition isoliert ist, kann die Freigabe des Indexes im Vergleich zur veröffentlichten Wissensdatenbank einen Unterschied im Ergebnis verursachen.

Um mit der `test`- und `production`-Wissensdatenbank das _gleiche Ergebnis_ zu erhalten, isolieren Sie eine QnA Maker-Ressource in eine einzelne Wissensdatenbank. In dieser Architektur muss die Ressource nur für die Dauer des isolierten Batchtests existieren.

## <a name="next-steps"></a>Nächste Schritte

* [Azure-Ressourcen](../how-to/set-up-qnamaker-service-azure.md)
* [Frage-Antwort-Paare](question-answer-set.md)
