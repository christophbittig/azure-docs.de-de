---
title: Sichern und Wiederherstellen von kundenspezifischen Sprachressourcen
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie sich auf Dienstausfälle von Custom Speech und Custom Voice vorbereiten.
services: cognitive-services
author: masakiitagaki
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: mitagaki
ms.custom: ignite-fall-2021
ms.openlocfilehash: 860e70f567742a418e8de75c68793b29fb691df2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058367"
---
# <a name="back-up-and-recover-speech-customer-resources"></a>Sichern und Wiederherstellen von kundenspezifischen Sprachressourcen

Der Speech-Dienst ist [in verschiedenen Regionen verfügbar](./regions.md). Dienstabonnementschlüssel sind an eine einzelne Region gebunden. Wenn Sie einen Schlüssel abrufen, wählen Sie eine bestimmte Region aus, in der sich Ihre Daten, Ihr Modell und Ihre Bereitstellungen befinden.

Datasets für kundenseitig erstellte Datenressourcen (wie z. B. benutzerdefinierte Sprachmodelle, benutzerdefinierte Voicefonts und Stimmprofile zur Sprechererkennung) stehen ebenfalls nur in der **Bereitstellungsregion des Diensts zur Verfügung**. Solche Ressourcen sind:

**Custom Speech Service**
-   Audio-/Textdaten zum Training
-   Audio-/Textdaten zum Testen
-   Angepasste Sprachmodelle
-   Protokolldaten

**Custom Voice**
-   Audio-/Textdaten zum Training
-   Audio-/Textdaten zum Testen
-   Benutzerdefinierte Voicefonts

**Sprechererkennung**
- Audio zur Sprecherregistrierung
- Sprecherstimmensignatur

Während einige Kunden unsere Standardendpunkte verwenden, um Audiomaterial oder Standardstimmen für die Sprachsynthese zu transkribieren, erstellen andere Kunden Ressourcen zur Anpassung.

Diese Ressourcen werden regelmäßig und automatisch von den Repositorys selbst gesichert, sodass **kein Datenverlust eintritt**, wenn eine Region nicht mehr verfügbar ist. Sie müssen jedoch Maßnahmen ergreifen, um die Dienstkontinuität im Fall des Ausfalls einer Region sicherzustellen.

## <a name="how-to-monitor-service-availability"></a>Überwachen der Dienstverfügbarkeit

Wenn Sie unsere Standardendpunkte verwenden, sollten Sie Ihren Clientcode so konfigurieren, dass er eine Fehlerüberwachung durchführt. Wenn wiederholt Fehler auftreten, sollten Sie darauf vorbereitet sein, den Datenverkehr zu einer Region Ihrer Wahl umzulenken, für die Sie ein Dienstabonnement besitzen.

Führen Sie diese Schritte aus, um Ihren Client für die Fehlerüberwachung zu konfigurieren:

1.  Suchen Sie die [Liste der regional verfügbaren Endpunkte in unserer Dokumentation](./rest-speech-to-text.md).
2.  Wählen Sie in der Liste eine primäre Region und mindestens eine sekundäre/Sicherungsregion aus.
3. Erstellen Sie in Azure-Portal Ressourcen für den Speech-Dienst für jede Region.
    -  Wenn Sie ein bestimmtes Kontingent festgelegt haben, ziehen Sie in Betracht, das gleiche Kontingent auch in den Sicherungsregionen festzulegen. Details dazu finden Sie unter [Speech-Dienst – Kontingente und Limits](./speech-services-quotas-and-limits.md).

4.  Beachten Sie, dass jede Region einen eigenen STS-Tokendienst aufweist. Für die primäre Region und alle Sicherungsregionen muss Ihre Clientkonfigurationsdatei über folgende Informationen verfügen:
    -  Regionale Speech-Dienst-Endpunkte
    -  [Regionaler Abonnementschlüssel und Regionscode](./rest-speech-to-text.md)

5.  Konfigurieren Sie Ihren Code für die Überwachung auf Verbindungsfehler (in der Regel Verbindungstimeouts und Dienstausfälle). Hier sehen Sie Beispielcode in C#: [GitHub: Hinzufügen eines Beispiels für das Anzeigen eines möglichen Kandidaten für den Regionswechsel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/fa6428a0837779cbeae172688e0286625e340942/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L965).

    1.  Da es in Netzwerken zu vorübergehenden Fehlern kommt, lautet die Empfehlung bei einzeln auftretenden Verbindungsproblemen, den Vorgang zu wiederholen.
    2.  Leiten Sie bei wiederholt auftretenden Fehlern den Datenverkehr auf den neuen STS-Tokendienst und den Speech-Dienst-Endpunkt um. (Referenzbeispielcode für Sprachsynthese: [GitHub: TTS Public Voice Switching Region](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L880).

Die Wiederherstellung nach regionalen Ausfällen kann für diesen Nutzungstyp sofort und zu sehr geringen Kosten erfolgen. Dazu ist lediglich die Entwicklung dieser Funktionalität auf der Clientseite erforderlich. Der entstehende Datenverlust unter der Annahme, dass keine Sicherung des Audiodatenstroms erfolgt, ist minimal.

## <a name="custom-endpoint-recovery"></a>Wiederherstellung benutzerdefinierter Endpunkte

Datenressourcen, Modelle oder Bereitstellungen in einer Region können in keiner anderen Region sichtbar oder zugänglich gemacht werden.

Sie sollten Speech-Dienst-Ressourcen sowohl in einer Hauptregion als auch in einer sekundären Region erstellen, indem Sie die gleichen Schritte wie für die Standardendpunkte ausführen.

### <a name="custom-speech"></a>Custom Speech

Der Custom Speech-Dienst unterstützt kein automatisches Failover. Wir empfehlen die folgenden Schritte zur Vorbereitung auf ein manuelles oder automatisches Failover, das in Ihrem Clientcode implementiert ist. In diesen Schritten replizieren Sie benutzerdefinierte Modelle in einer sekundären Region. Mit dieser Vorbereitung kann Ihr Clientcode zu einer sekundären Region wechseln, wenn die primäre Region ausfällt.

1.  Erstellen Sie Ihr benutzerdefiniertes Modell in einer Hauptregion (Primär).
2.  Führen Sie die [Modellkopier-API](https://eastus2.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) aus, um das benutzerdefinierte Modell in alle vorbereiteten Regionen (sekundär) zu replizieren.
3.  Wechseln Sie zu Speech Studio, um das kopierte Modell zu laden, und erstellen Sie einen neuen Endpunkt in der sekundären Region. Informationen zum Bereitstellen eines neuen Modells finden Sie unter [Trainieren und Bereitstellen eines Custom Speech Modells](./how-to-custom-speech-train-model.md).
    -  Wenn Sie ein bestimmtes Kontingent festgelegt haben, ziehen Sie in Betracht, das gleiche Kontingent auch in den Sicherungsregionen festzulegen. Details dazu finden Sie unter [Speech-Dienst – Kontingente und Limits](./speech-services-quotas-and-limits.md).
4.  Konfigurieren Sie Ihren Client dafür, bei permanenten Fehlern ein Failover auszuführen, wie bei der Verwendung von Standardendpunkten.

Ihr Clientcode kann die Verfügbarkeit der bereitgestellten Modelle in Ihrer primären Region überwachen und den Audiodatenverkehr an die sekundäre Region umleiten, wenn die primäre Region ausfällt. Wenn Sie kein Echtzeitfailover benötigen, können Sie diese Schritte trotzdem ausführen, um sich auf ein manuelles Failover vorzubereiten.

#### <a name="offline-failover"></a>Offlinefailover

Wenn Sie kein Echtzeitfailover benötigen, können Sie sich dafür entscheiden, das Importieren Ihrer Daten und das Erstellen und Bereitstellen Ihrer Modelle in der sekundären Region zu einem späteren Zeitpunkt durchzuführen, wobei Ihnen bewusst sein muss, dass diese Aufgaben Zeit in Anspruch nehmen werden.

#### <a name="failover-time-requirements"></a>Anforderungen an die Failoverzeit

Dieser Abschnitt enthält allgemeine Hinweise zur zeitlichen Steuerung. Die Zeiten wurden aufgezeichnet, um das Offlinefailover anhand eines [repräsentativen Testdatensets](https://github.com/microsoft/Cognitive-Custom-Speech-Service) zu schätzen.

-   Datenupload in neue Region: **15 Minuten**
-   Erstellung des Akustik-/Sprachmodells: **6 Stunden (je nach Datenvolumen)**
-   Modellauswertung: **30 Minuten**
-   Endpunktbereitstellung: **10 Minuten**
-   API-Aufruf zum Kopieren von Modellen: **10 Minuten**
-   Neukonfiguration und Bereitstellung von Clientcode: **Abhängig vom Clientsystem**

Dennoch ist es ratsam, Schlüssel für eine primäre und sekundäre Region für Produktionsmodelle mit Echtzeitanforderungen zu erstellen.

### <a name="custom-voice"></a>Custom Voice

Custom Voice unterstützt kein automatisches Failover. Behandeln Sie Echtzeit-Synthesefehler mithilfe dieser beiden Optionen.

**Option 1: Failover auf die öffentliche Stimme in derselben Region.**

Wenn bei der Echtzeitsynthese der benutzerdefinierten Stimme ein Fehler auftritt, führen Sie ein Failover zu einer öffentlichen Stimme durch (Clientbeispielcode: [GitHub: benutzerdefiniertes Sprachfailover auf öffentliche Stimme](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L899)).

Überprüfen Sie die [verfügbaren öffentlichen Stimmen](./language-support.md#neural-voices). Sie können den Beispielcode oben auch ändern, wenn Sie ein Failover zu einer anderen Stimme oder in einer anderen Region wünschen.

**Option 2: Failover zu benutzerdefinierter Stimme in einer anderen Region.**

1.  Erstellen Sie Ihre benutzerdefinierte Stimme, und stellen Sie sie in einer Hauptregion (primär) bereit.
2.  Kopieren Sie Ihr benutzerdefiniertes Stimmmodell in [Speech Studio](https://speech.microsoft.com) in eine andere Region (die sekundäre Region).
3.  Wechseln Sie zu Speech Studio, und wechseln Sie zur Speech-Ressource in der sekundären Region. Laden Sie das kopierte Modell, und erstellen Sie einen neuen Endpunkt.
    -   Die Bereitstellung von Sprachmodellen wird in der Regel **in 3 Minuten** abgeschlossen.
    -   Hinweis: Zusätzliche Endpunkte unterliegen zusätzlichen Gebühren. [Die Preise für das Hosting von Modellen finden Sie hier](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

4.  Konfigurieren Sie Ihren Client für das Failover in die sekundäre Region. Siehe dazu den Beispielcode in C#: [GitHub: benutzerdefiniertes Sprachfailover in die sekundäre Region](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L920).

### <a name="speaker-recognition"></a>Sprechererkennung

Die Sprechererkennung verwendet [Azure-Regionspaare](/azure/best-practices-availability-paired-regions) für automatische Failover von Prozessen. Sprecherregistrierungen und Stimmsignaturen werden regelmäßig gesichert, um Datenverluste zu vermeiden und diese im Falle eines Ausfalls wiederverwenden zu können.

Im Falle eines Ausfalls führt der Sprechererkennungsdienst automatisch ein Failover in eine gekoppelte Region durch und verwendet die gesicherten Daten, um mit der Verarbeitung von Anforderungen fortzufahren, bis die Hauptregion wieder online ist.
