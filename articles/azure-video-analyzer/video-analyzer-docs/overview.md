---
title: Was ist Azure Video Analyzer?
description: Dieses Thema enthält eine Übersicht über Azure Video Analyzer.
ms.topic: overview
ms.date: 06/01/2021
ms.openlocfilehash: 4cf2ba0bb9d5388eb6017fd0717929a3f5410a7d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646915"
---
# <a name="what-is-azure-video-analyzer-preview"></a>Was ist Azure Video Analyzer? (Vorschauversion)
 
Azure Video Analyzer ist eine Plattform zum Erstellen intelligenter Videoanwendungen, die den Edge und die Cloud umfassen. Die Plattform besteht aus einem IoT Edge-Modul und einem zugeordneten Azure-Dienst. Sie bietet die Möglichkeit zum Erfassen, Aufzeichnen und Analysieren von Livevideos sowie zum Veröffentlichen der Ergebnisse, d. h. einem Video und/oder einer Videoanalyse. Videos können im Edge oder Video Analyzer-Clouddienst veröffentlicht werden, während Videoanalysen in Azure-Diensten (in der Cloud und/oder im Edge-Bereich) veröffentlicht werden können. Über die Plattform können IoT-Lösungen durch Videoanalyse verbessert werden. Die Video Analyzer-Funktionalität kann mit anderen Azure IoT Edge-Modulen, z. B. Stream Analytics in IoT Edge oder Cognitive Services in IoT Edge, und mit Azure-Diensten in der Cloud wie beispielsweise Event Hub, Cognitive Services usw. kombiniert werden, um leistungsfähige Hybridanwendungen (z. B. Edge und Cloud) zu erstellen.

Das Video Analyzer-Edgemodul ist als erweiterbare Plattform konzipiert und ermöglicht Ihnen die Verbindung mit verschiedenen Edgemodulen für die Videoanalyse (z. B. Cognitive Services-Container, von Ihnen entwickelte benutzerdefinierte Edgemodule mit Open-Source-Machine Learning-Modellen oder benutzerdefinierte Modelle, die mit Ihren eigenen Daten trainiert werden) und ihre Verwendung zum Analysieren von Livevideos, ohne dass Sie sich Gedanken über die komplexen Aufgaben zum Erstellen und Ausführen einer Livevideo-Pipeline machen müssen. Mit dem Video Analyzer-Clouddienst können Sie das Video und die Videoanalyse aus solchen Workflows abspielen.

## <a name="accelerate-iot-solutions-development"></a>Beschleunigte Entwicklung von IoT-Lösungen 

Mithilfe von IoT-Lösungen, bei denen Videoanalysen mit Signalen anderer IoT-Sensoren oder Geschäftsdaten kombiniert werden, können Sie Geschäftsentscheidungen vollständig oder teilweise automatisieren und dadurch Produktivitätsverbesserungen erzielen. Mit Video Analyzer können Sie solche Lösungen schneller erstellen. Sie können sich auf das Entwickeln der Module für die Videoanalyse und die unternehmensspezifische Logik konzentrieren, während die Komplexität der Verwaltung und Ausführung einer Videopipeline über die Plattform abstrahiert wird.

Mit Video Analyzer können Sie weiterhin Ihre [CCTV-Kameras](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) mit den vorhandenen [Videomanagementsystemen (VMS)](https://en.wikipedia.org/wiki/Video_management_system) verwenden und Anwendungen für die Videoanalyse unabhängig entwickeln. Video Analyzer kann in Verbindung mit SDKs und Tools für maschinelles Sehen verwendet werden, um innovative IoT-Lösungen zu entwickeln. Dies wird im folgenden Diagramm verdeutlicht.

![Entwickeln von IoT-Lösungen mit Video Analyzer](./media/overview/product-diagram.svg)

### <a name="concepts"></a>Konzepte

* [Pipeline](pipeline.md)
* [Videoaufzeichnung](video-recording.md)
* [Fortlaufende Videoaufzeichnung](continuous-video-recording.md)
* [Ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md)
* [Video Analyzer ohne Videoaufzeichnung](analyze-live-video-without-recording.md)

## <a name="compliance-privacy-and-security"></a>Compliance, Datenschutz und Sicherheit

Wichtig: Ihre Nutzung von Video Analyzer darf nicht gegen geltende Gesetze verstoßen, und weder Video Analyzer noch ein Azure-Dienst darf auf eine Weise verwendet werden, die die Rechte Dritter verletzt oder für Dritte schädlich ist.

Vor dem Verarbeiten eines Videos oder Bilds in Video Analyzer müssen Sie über alle entsprechenden Berechtigungen zur Verwendung des Videos verfügen, einschließlich – sofern gesetzlich erforderlich – alle notwendigen Zustimmungen von Einzelpersonen (falls vorhanden), die auf dem Video/Bild zu sehen sind, zur Nutzung, Verarbeitung und Speicherung ihrer Daten in Video Analyzer und Azure. Je nach Rechtsprechung können besondere rechtliche Anforderungen für die Sammlung, Onlineverarbeitung und Speicherung bestimmter Datenkategorien, z. B. biometrische Daten, gelten. Stellen Sie Konformität mit allen rechtlichen Anforderungen sicher, die möglicherweise für Sie gelten, bevor Sie Video Analyzer und Azure für die Verarbeitung und Speicherung von Daten verwenden, die bestimmten rechtlichen Anforderungen unterliegen.

Informationen zu Compliance, Datenschutz und Sicherheit in Video Analyzer finden Sie im [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) von Microsoft. Informationen zu den Datenschutzauflagen und zur Behandlung Ihrer Daten sowie Datenaufbewahrungsmethoden durch Microsoft, einschließlich Informationen zur Löschung Ihrer Daten, finden Sie in den [Datenschutzbestimmungen](https://privacy.microsoft.com/PrivacyStatement), in den [Lizenzbedingungen für Onlinedienste](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) (Online Services Terms, OST) und im [Nachtrag zur Datenverarbeitung](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (Data Processing Addendum, DPA) von Microsoft. Wenn Sie Video Analyzer nutzen, erklären Sie sich damit einverstanden, dass Sie den OST, DPA und Datenschutzbestimmungen unterliegen.

## <a name="next-steps"></a>Nächste Schritte

* Befolgen Sie die Anweisungen im Artikel [Schnellstart: Erste Schritte – Azure Video Analyzer](get-started-detect-motion-emit-events.md), um zu erfahren, wie Sie die Bewegungserkennung für einen Livevideofeed ausführen.
* Machen Sie sich mit der [Terminologie](terminology.md) vertraut.
