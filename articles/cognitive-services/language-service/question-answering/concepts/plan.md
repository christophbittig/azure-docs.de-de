---
title: 'Planen Ihrer App: Fragen und Antworten'
description: Hier erfahren Sie, wie Sie Ihre „Fragen und Antworten“-App planen. Hier erfahren Sie, wie das „Fragen und Antworten“-Feature funktioniert und mit anderen Azure-Diensten und einigen Wissensdatenbank-Konzepten interagiert.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 0dbd8e670f652ff598c9b65c07687ab38a097050
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478749"
---
# <a name="plan-your-question-answering-app"></a>Planen Ihrer „Fragen und Antworten“-App

Um Ihre „Fragen und Antworten“-App zu planen, müssen Sie verstehen, wie das Feature „Fragen und Antworten“ funktioniert und mit anderen Azure-Diensten interagiert. Sie sollten auch ein fundiertes Verständnis von Konzepten der Wissensdatenbank haben.

## <a name="azure-resources"></a>Azure-Ressourcen

Jede mit „Fragen und Antworten“ erstellte [Azure-Ressource](azure-resources.md#resource-purposes) hat einen bestimmten Zweck. Jede Ressource hat ihren eigenen Zweck, ihre eigenen Grenzwerte und ihren eigenen [Tarif](azure-resources.md#pricing-tier-considerations). Es ist wichtig, die Funktion dieser Ressourcen zu verstehen, sodass Sie dieses Wissen in Ihren Planungsprozess integrieren können.

| Resource | Zweck |
|--|--|
| Die Ressource [Sprachenressource](azure-resources.md) | Verfassen, Abfragevorhersage-Endpunkt und Telemetrie|
| [Cognitive Search](azure-resources.md#azure-cognitive-search-resource)-Ressource | Datenspeicherung und -suche |

### <a name="resource-planning"></a>Ressourcenplanung

Der Durchsatz von „Fragen und Antworten“ ist derzeit sowohl für Verwaltungs-APIs als auch für Vorhersage-APIs auf zehn Transaktionen pro Sekunde begrenzt. Um 10 Transaktionen pro Sekunde für Ihren Dienst zu erreichen, empfehlen wir die SKU S1 (eine Instanz) von Azure Cognitive Search.

### <a name="language-resource"></a>Sprachressource

Eine einzelne Sprachressource mit aktiviertem benutzerdefiniertem „Fragen und Antworten“-Feature kann mehrere Projekte bzw. Wissensdatenbanken hosten. Die Anzahl der Projekte bzw. Wissensdatenbanken wird durch die Menge der Indizes bestimmt, die der Cognitive Search-Tarif unterstützt. Erfahren Sie mehr über die [Beziehung zwischen Indizes und Wissensdatenbanken](azure-resources.md#index-usage).

### <a name="knowledge-base-size-and-throughput"></a>Größe und Durchsatz der Wissensdatenbank

Wenn Sie eine echte App erstellen, planen Sie ausreichend Ressourcen für die Größe Ihrer Wissensdatenbank und die erwarteten Anforderungen an die Abfragevorhersage ein.

Die Größe einer Wissensdatenbank wird gesteuert durch:
* Tarifgrenzwerte der [Cognitive Search-Ressource](../../../../search/search-limits-quotas-capacity.md)
* [Grenzwerte für „Fragen und Antworten“](./limits.md)

Die Abfragevorhersageanforderung der Wissensdatenbank wird durch den Web-App-Plan und die Web-App gesteuert. Informationen zum Planen Ihres Tarifs finden Sie unter [Empfohlene Einstellungen](azure-resources.md#recommended-settings).

### <a name="understand-the-impact-of-resource-selection"></a>Grundlegendes zu den Auswirkungen der Ressourcenauswahl

Die richtige Ressourcenauswahl bedeutet, dass Ihre Wissensdatenbank die Abfragevorhersagen erfolgreich beantwortet.

Wenn Ihre Wissensdatenbank nicht ordnungsgemäß funktioniert, liegt das Problem in der Regel an einer nicht ordnungsgemäßen Ressourcenverwaltung.

Bei nicht ordnungsgemäßer Ressourcenauswahl müssen Sie untersuchen, welche [Ressource geändert werden muss](azure-resources.md#pricing-tier-considerations).

## <a name="project"></a>Projekt

Ein Projekt bzw. eine Wissensdatenbank ist direkt an seine Sprachressource gebunden. Sie enthält die Frage-Antwort-Paare (QnA), die zum Beantworten von Abfragevorhersageanforderungen verwendet werden.

### <a name="language-considerations"></a>Sprachbezogene Überlegungen

Sie können jetzt Projekte in verschiedenen Sprachen innerhalb derselben Sprachressource haben, in der das benutzerdefinierte Feature „Fragen und Antworten“ aktiviert ist. Wenn Sie Ihr erstes Projekt erstellen, können Sie auswählen, ob Sie die Ressource für Projekte bzw. Wissensdatenbanken in einer einzelnen Sprache verwenden möchten, die auf alle folgenden Projekte angewendet wird, oder ob Sie jedes Mal, wenn ein Projekt erstellt wird, eine Sprachauswahl treffen möchten.

### <a name="ingest-data-sources"></a>Erfassen von Datenquellen

Das Feature „Fragen und Antworten“ unterstützt auch unstrukturierte Inhalte. Sie können eine Datei mit unstrukturiertem Inhalt hochladen.

Derzeit unterstützen wir keine URLs für unstrukturierten Inhalt.

Beim Erfassungsprozess werden unterstützte Inhaltstypen in Markdown konvertiert. Die weitere Bearbeitung der *Antwort* erfolgt mit Markdown. Nachdem Sie eine Wissensdatenbank erstellt haben, können Sie F&A-Paare im Language Studio-Portal mit Rich-Text-Dokumenterstellung bearbeiten.

### <a name="data-format-considerations"></a>Überlegungen zum Datenformat

Da das endgültige Format eines QnA-Paars Markdown ist, müssen Sie die Markdown-Unterstützung kennen.

### <a name="bot-personality"></a>Botpersönlichkeit

Fügen Sie Ihrem Projekt bzw. Ihrer Wissensdatenbank eine Botpersönlichkeit zum [Smalltalk](../how-to/chit-chat.md) hinzu. Diese Persönlichkeit bietet vorgefertigte Antworten, die in einem bestimmten Konversationston bereitgestellt werden, z. B. *geschäftlich* und *freundlich*. Dieses Geplauder wird als Konversationsgruppe bereitgestellt, die Sie die mit Hinzufügen, Bearbeiten und Entfernen vollständig kontrollieren können.

Eine Botpersönlichkeit sollten Sie verwenden, wenn Ihr Bot mit Ihrer Wissensdatenbank verbunden ist. Sie können sich dafür entscheiden, Smalltalk in Ihrer Wissensdatenbank zu verwenden, auch wenn Sie gleichzeitig eine Verbindung mit anderen Diensten herstellen, aber Sie sollten überprüfen, wie der Botdienst interagiert, um zu wissen, ob dies der richtige Architekturentwurf für Ihre Nutzung ist.

### <a name="conversation-flow-with-a-project"></a>Konversationsfluss bei einem Projekt

Der Konversationsfluss beginnt in der Regel mit einer Begrüßung eines Benutzers, z. B. `Hi` oder `Hello`. Ihre Wissensdatenbank kann mit einer allgemeinen Antwort (z. B. `Hi, how can I help you`) antworten, und Sie kann auch eine Auswahl von Folgeaufforderungen zum Fortsetzen der Konversation bereitstellen.

Sie sollten beim Entwurf Ihres Konversationsflusses an eine Schleife denken, damit ein Benutzer weiß, wie er Ihren Bot verwenden kann, und nicht vom Bot in der Konversation stehen gelassen wird. [Folgeaufforderungen](../tutorials/guided-conversations.md) bieten die Verknüpfung zwischen QnA-Paaren, die den Konversationsfluss ermöglichen.

### <a name="authoring-with-collaborators"></a>Erstellung mit Projektmitarbeitern

Projektmitarbeiter sind möglicherweise andere Entwickler, für die der vollständige Entwicklungsstapel der Wissensdatenbankanwendung freigeben ist, oder die nur auf die Erstellung der Wissensdatenbank beschränkt sind.

Die Erstellung der Wissensdatenbank unterstützt mehrere rollenbasierte Zugriffsberechtigungen, die Sie im Azure-Portal anwenden, um den Handlungsspielraum eines Projektmitarbeiters einzuschränken.

## <a name="integration-with-client-applications"></a>Integration in Clientanwendungen

Integration in Clientanwendungen wird durch Senden einer Abfrage an den Vorhersage-Runtimeendpunkt erreicht. Eine Abfrage wird an Ihr spezifisches Projekt bzw. Ihre Wissensdatenbank mit einer SDK- oder REST-basierten Anforderung an den Endpunkt Ihrer „Fragen und Antworten“-Web-App gesendet.

Damit eine Clientanforderung ordnungsgemäß authentifiziert werden kann, muss die Clientanwendung die richtigen Anmeldeinformationen und die ID der Wissensdatenbank senden. Wenn Sie einen Azure Bot Service verwenden, konfigurieren Sie diese Einstellungen als Teil der Botkonfiguration im Azure-Portal.

### <a name="conversation-flow-in-a-client-application"></a>Konversationsfluss in einer Clientanwendung

Der Konversationsfluss in einer Clientanwendungs wie einem Azure-Bot erfordert möglicherweise vor und nach der Interaktion mit der Wissensdatenbank Funktionalität.

Unterstützt Ihre Clientanwendung den Konversationsfluss, entweder durch die Bereitstellung alternativer Mittel zur Bearbeitung von Folgeaufforderungen oder durch Smalltalk? Sofern dies der Fall ist, entwerfen Sie diese frühzeitig und stellen Sie sicher, dass die Abfrage der Clientanwendung von einem anderen Dienst oder bei der Übermittlung an Ihre Wissensdatenbank ordnungsgemäß bearbeitet wird.

### <a name="active-learning-from-a-client-application"></a>Aktives Lernen über eine Clientanwendung

Das Feature „Fragen und Antworten“ verwendet _aktives Lernen_, um Ihre Wissensdatenbank zu verbessern, indem alternative Fragen zu einer Antwort vorgeschlagen werden. Die Clientanwendung ist für einen Teil dieses [aktiven Lernens](../tutorials/active-learning.md) verantwortlich. Die Clientanwendung kann anhand von Konversationseingabeaufforderungen feststellen, dass die Wissensdatenbank eine Antwort zurückgegeben hat, die für den Benutzer nicht hilfreich ist, und sie kann eine bessere Antwort ermitteln. Die Clientanwendung muss diese Informationen an die Wissensdatenbank zurücksenden, um die Vorhersagequalität zu verbessern.

### <a name="providing-a-default-answer"></a>Bereitstellen einer Standardantwort

Wenn Ihre Wissensdatenbank keine Antwort findet, wird die _Standardantwort_ zurückgegeben. Die Antwort kann auf der Seite **Einstellungen** konfiguriert werden.

Diese Standardantwort unterscheidet sich von der Standardantwort des Azure-Bots. Sie konfigurieren die Standardantwort für Ihren Azure-Bot im Azure-Portal als Teil der Konfigurationseinstellungen. Sie wird zurückgegeben, wenn der Schwellenwert für den Score nicht erreicht wird.

## <a name="prediction"></a>Vorhersage

Die Vorhersage ist die Antwort aus Ihrer Wissensdatenbank und enthält mehr Informationen als nur die Antwort. Um eine Abfragevorhersageantwort zu erhalten, verwenden Sie die „Fragen und Antworten“-API.

### <a name="prediction-score-fluctuations"></a>Fluktuationen des Vorhersageergebnisses

Ein Ergebnis kann sich auf der Grundlage verschiedener Faktoren ändern:

* Anzahl der Antworten, die Sie als Antwort mit der Eigenschaft `top` angefordert haben
* Vielzahl verfügbarer alternativer Fragen
* Filtern nach Metadaten
* Abfrage, die an das Projekt bzw. die Wissensdatenbank `test` oder `production` gesendet wurde

### <a name="analytics-with-azure-monitor"></a>Analysen mit Azure Monitor

In „Fragen und Antworten“ wird Telemetrie über den [Azure Monitor-Dienst](../../../../azure-monitor/index.yml) angeboten. Verwenden Sie unsere [wichtigsten Abfragen](../how-to/analytics.md), um Ihre Metriken zu verstehen.

## <a name="development-lifecycle"></a>Lebenszyklus der Entwicklung

Der Entwicklungslebenszyklus einer Wissensdatenbank ist fortlaufend: Bearbeiten, Testen und Veröffentlichen Ihrer Wissensdatenbank.

### <a name="knowledge-base-development-of-question-answer-pairs"></a>Die Wissensdatenbankentwicklung von Frage-Antwort-Paaren

Ihre QnA-Paare sollten auf Basis der Nutzung Ihrer Clientanwendung entworfen und entwickelt werden.

Jedes Paar kann Folgendes enthalten:
* Metadaten – Bei der Abfrage filterbar, damit Sie Ihre QnA-Paare mit zusätzlichen Informationen über Quelle, Inhalt, Format und Zweck Ihrer Daten versehen können.
* Folgeaufforderungen – Helfen beim Bahnen eines Pfads durch Ihre Wissensdatenbank, damit der Benutzer die richtige Antwort findet.
* Alternative Fragen – Wichtig, damit die Suche mit Ihrer Antwort aus verschiedenen Formen der Frage übereinstimmen kann. [Vorschläge für aktives Lernen](../tutorials/active-learning.md) werden in alternative Fragen umgewandelt.

### <a name="devops-development"></a>DevOps-Entwicklung

Zum Entwickeln einer Wissensdatenbank, die in eine DevOps-Pipeline eingefügt werden soll, muss die Wissensdatenbank in Batchtests isoliert sein.

Eine Wissensdatenbank teilt den Cognitive Search-Index mit allen anderen Wissensdatenbanken für die Sprachressource. Obwohl die Wissensdatenbank durch Partition isoliert ist, kann die Freigabe des Indexes im Vergleich zur veröffentlichten Wissensdatenbank einen Unterschied im Ergebnis verursachen.

Um die _gleiche Bewertung_ für die Wissensdatenbanken `test` und `production` zu erhalten, isolieren Sie eine Sprachressource in einer einzelnen Wissensdatenbank. In dieser Architektur muss die Ressource nur für die Dauer des isolierten Batchtests existieren.

## <a name="next-steps"></a>Nächste Schritte

* [Azure-Ressourcen](./azure-resources.md)
