---
title: Was ist Azure Video Analyzer?
description: Dieses Thema enthält eine Übersicht über Azure Video Analyzer.
ms.topic: overview
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b72abcd0f3df246e441d68643456ee7134522728
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563148"
---
# <a name="what-is-azure-video-analyzer-preview"></a>Was ist Azure Video Analyzer? (Vorschauversion)
 
Azure Video Analyzer ist eine Plattform zum Erstellen intelligenter Videoanwendungen, die den Edge und die Cloud umfassen. Die Plattform besteht aus einem IoT Edge-Modul und einem Azure-Dienst. Sie bietet Ihnen die Möglichkeit, Live-Videos zu erfassen, aufzeichnen und zu analysieren und die Ergebnisse , d. h. Videos und damit verbundene Erkenntnisse, im Edge oder der Cloud zu veröffentlichen.

Das Video Analyzer-Edgemodul kann mit anderen Azure IoT Edge-Modulen, wie z. B. Stream Analytics, Cognitive Services, und anderen Azure-Clouddiensten, wie z. B. Event Hub und Cognitive Services, verwendet werden, um leistungsstarke Hybridanwendungen (d. h. Edge- und Cloudanwendungen) zu erstellen. Dieses erweiterbare Edgemodul lässt sich nahtlos in verschiedene KI-Edgemodule wie Azure Cognitive Services-Container oder benutzerdefinierte Edgemodule integrieren, die mithilfe von Open-Source-Machine Learning-Modellen und deren Trainingsdaten erstellt wurden. Auf Grundlage der Video Analyzer-Plattform können Sie Live-Videos analysieren, ohne sich Gedanken über die komplexen Erstell-, Betriebs- und Verwaltungsvorgänge eines vielschichtigen Systems machen zu müssen.

Neben der Analyse von Live-Videos können Sie mit dem Edgemodul optional auch Videos lokal am Edge oder in der Cloud aufzeichnen und damit verbundene Erkenntnisse in Azure-Diensten (am Edge und/oder in der Cloud) veröffentlichen. Der Video Analyzer-Clouddienst kann zur Verwaltung von Videos und damit verbundenen Erkenntnissen verwendet werden, die direkt in der Cloud aufgezeichnet werden.

Er ist somit geeignet, um IoT-Lösungen durch den Einsatz verschiedener Funktionen eines [Videoverwaltungssystems (VMS)](https://en.wikipedia.org/wiki/Video_management_system) zu verbessern, darunter Aufzeichnung, Wiedergabe und Export (Generieren von Videodateien, die extern freigegeben werden können). Darüber hinaus kann der Dienst zur Erstellung einer cloudnativen Lösung mit den gleichen Funktionen verwendet werden (wie im folgenden Diagramm dargestellt), mit Kameras, die direkt mit der Cloud verbunden sind.

## <a name="accelerate-iot-solutions-development"></a>Beschleunigte Entwicklung von IoT-Lösungen 

Mithilfe von IoT-Lösungen, bei denen Videoanalysen mit Signalen anderer IoT-Sensoren oder Geschäftsdaten kombiniert werden, können Sie Geschäftsentscheidungen vollständig oder teilweise automatisieren und dadurch Produktivitätsverbesserungen erzielen. Mit Video Analyzer können Sie solche Lösungen schneller erstellen. Sie können sich auf das Entwickeln der Module für die Videoanalyse und die unternehmensspezifische Logik konzentrieren, während die Komplexität der Verwaltung und Ausführung einer Videopipeline über die Plattform abstrahiert wird.

Mit Video Analyzer können Sie weiterhin Ihre [CCTV-Kameras](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) mit den vorhandenen [Videomanagementsystemen (VMS)](https://en.wikipedia.org/wiki/Video_management_system) verwenden und Anwendungen für die Videoanalyse unabhängig entwickeln. Video Analyzer kann gemeinsam mit SDKs und Tools für maschinelles Sehen verwendet werden, um innovative IoT-Lösungen zu entwickeln. Dies wird im folgenden Diagramm verdeutlicht.

![Entwickeln von IoT-Lösungen mit Video Analyzer](./media/overview/product-diagram.svg)

### <a name="concepts"></a>Konzepte

* [Pipeline](pipeline.md)
* [Video Analyzer ohne Videoaufzeichnung](analyze-live-video-without-recording.md)
* [Videoaufzeichnung](video-recording.md)


## <a name="compliance-privacy-and-security"></a>Compliance, Datenschutz und Sicherheit

Wichtig: Ihre Nutzung von Video Analyzer darf nicht gegen geltende Gesetze verstoßen, und weder Video Analyzer noch ein Azure-Dienst darf auf eine Weise verwendet werden, die die Rechte Dritter verletzt oder für Dritte schädlich ist.

Vor dem Verarbeiten eines Videos oder Bilds in Video Analyzer müssen Sie über alle entsprechenden Berechtigungen zur Verwendung des Videos verfügen, einschließlich – sofern gesetzlich erforderlich – alle notwendigen Zustimmungen von Einzelpersonen (falls vorhanden), die auf dem Video/Bild zu sehen sind, zur Nutzung, Verarbeitung und Speicherung ihrer Daten in Video Analyzer und Azure. Je nach Rechtsprechung können besondere rechtliche Anforderungen für die Sammlung, Onlineverarbeitung und Speicherung bestimmter Datenkategorien, z. B. biometrische Daten, gelten. Stellen Sie Konformität mit allen rechtlichen Anforderungen sicher, die möglicherweise für Sie gelten, bevor Sie Video Analyzer und Azure für die Verarbeitung und Speicherung von Daten verwenden, die bestimmten rechtlichen Anforderungen unterliegen.

Informationen zu Compliance, Datenschutz und Sicherheit in Video Analyzer finden Sie im [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) von Microsoft. Informationen zu den Datenschutzauflagen und zur Behandlung Ihrer Daten sowie zu Datenaufbewahrungsmethoden durch Microsoft, einschließlich Informationen zur Löschung Ihrer Daten, finden Sie in den [Datenschutzbestimmungen](https://privacy.microsoft.com/PrivacyStatement), in den [Produktbedingungen](https://www.microsoft.com/licensing/terms/welcome/welcomepage) und im [Nachtrag zum Datenschutz für Produkte und Dienste](https://www.microsoft.com/licensing/docs/view/Microsoft-Products-and-Services-Data-Protection-Addendum-DPA) („DPA“) von Microsoft. Wenn Sie Video Analyzer nutzen, erkennen Sie die Produktbedingungen, den DPA und die Datenschutzbestimmungen als verbindlich an.

## <a name="next-steps"></a>Nächste Schritte

* Befolgen Sie die Anweisungen im Artikel [Schnellstart: Erste Schritte – Azure Video Analyzer](get-started-detect-motion-emit-events.md), um zu erfahren, wie Sie die Bewegungserkennung für Live-Videos ausführen.
* Machen Sie sich mit der [Terminologie](terminology.md) vertraut.
