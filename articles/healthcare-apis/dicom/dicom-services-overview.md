---
title: Übersicht über den DICOM-Dienst – Azure Healthcare-APIs
description: In diesem Artikel lernen Sie konzepte des DICOM-, Medical Imaging- und DICOM-Diensts.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 7cff5c0095bdb9d230673f347cdf5b3e6d099d17
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122353657"
---
# <a name="overview-of-the-dicom-service"></a>Übersicht über den DICOM-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel werden die Konzepte von DICOM, der medizinischen Bildverarbeitung und des DICOM-Diensts beschrieben.

## <a name="medical-imaging"></a>Medizinische Bilddaten

Die medizinische Bildverarbeitung ist die Technik und der Prozess der Erstellung visueller Darstellungen des Inneren eines Körpers für die medizinische Analyse und den medizinischen Eingriff sowie die visuelle Darstellung der Funktion einiger Bzw. aller Menschlichen(n). Bei der medizinischen Bildverarbeitung wird versucht, interne Strukturen zu erkennen, die von der Skin und von Denkzellen verdeckt werden, sowie um Dies zu diagnostizieren und zu behandeln. Bei der medizinischen Bildverarbeitung wird auch eine Datenbank mit normaler Anatomie und Normalität erstellt, um Anomalien zu identifizieren. Obwohl die Bildverarbeitung von entfernten Darstellungen aus medizinischen Gründen durchgeführt werden kann, sind solche Verfahren in der Regel Teil der Menschlichen statt der medizinischen Bildverarbeitung. [Wikipedia, Medizinische Bildverarbeitung](https://en.wikipedia.org/wiki/Medical_imaging)

## <a name="dicom"></a>DICOM

DICOM (Digital Imaging and Communications in Medical) ist der internationale Standard zum Übertragen, Speichern, Abrufen, Drucken, Verarbeiten und Anzeigen von medizinischen Bildverarbeitungsinformationen und ist der primäre medizinische Bildverarbeitungsstandard, der im Gesundheitswesen akzeptiert wird. Obwohl einige Ausnahmen vorhanden sind (Verunglichung, Ungnötige), verlassen sich fast alle medizinischen Besonderheiten, Gerätehersteller, Softwarehersteller und einzelnen Anwender in irgendeiner Phase eines medizinischen Workflows im Zusammenhang mit der Bildverarbeitung auf DICOM. DICOM stellt sicher, dass medizinische Bilder Qualitätsstandards erfüllen, sodass die Genauigkeit der Diagnose beibehalten werden kann. Die meisten Bildverarbeitungsmoden, einschließlich berechneter Bildverarbeitung (CT), MrI (Magnetic Imaging) und Bildverarbeitung, müssen den DICOM-Standards entsprechen. Auf Images im DICOM-Format muss über spezialisierte DICOM-Anwendungen zugegriffen und verwendet werden.

## <a name="dicom-service"></a>DICOM-Dienst

Ein DICOM-Dienst ist ein verwalteter Dienst, für den ein Azure-Abonnement und ein Azure Active Directory für die Bereitstellung im Azure Healthcare-APIs-Arbeitsbereich benötigt werden. Sie ermöglicht die standardbasierte Kommunikation mit allen DICOMwebfähigen &trade; Systemen. Der DICOM-Dienst fügt DICOM-Metadaten in einen FHIR-Dienst oder FHIR-Server ein, wodurch eine einzige Quelle der Wahrheit sowohl für die medizinischen Daten als auch für bildgebende Metadaten ermöglicht wird. 

Die Notwendigkeit, nicht-medizinische Daten effektiv zu integrieren, hat sich zu einem 100. Um Patienten effektiv zu behandeln, neue Behandlungen zu recherchierbar zu machen, Lösungen zu diagnostizieren oder einen effektiven Überblick über den Gesundheitsverlauf eines einzelnen Patienten zu erhalten, müssen Organisationen Daten aus mehreren Quellen integrieren. Eine der dringendsten Integrationen besteht in der Integration von patienten- und bildgebenden Daten.

FHIR wird zu einem wichtigen Standard für medizinische Daten und bietet Erweiterbarkeit, um die direkte Integration anderer Datentypen oder durch &trade; Verweise zu unterstützen. Mithilfe des DICOM-Diensts können Organisationen Verweise auf Bildverarbeitungsdaten in FHIR speichern und Abfragen aktivieren, die über die datasets für die Bildverarbeitung und die Bildverarbeitung &trade; hinaus durchgeführt werden. Dies kann viele verschiedene Szenarien ermöglichen, z. B.:

- **Erstellen von Kohorten für die Forschung.** Häufig über Abfragen für Patienten, die Daten sowohl in den medizinischen als auch in den Bildverarbeitungssystemen abfragten, z.B. in diesem System (das den Aufwand für die Integration von FHIR- und DICOM-Daten ausgelöst hat): "Geben Sie mir alle Patienten, die mit allen CT-Scandokumenten und den zugehörigen Berichterstattungen für jeden Patienten vor mehr als 45 Jahren vorgeschrieben sind, der in den letzten zwei Jahren eine Diagnose von Älteren durchgeführt &trade; hat."
- **Suchen von Ergebnissen für ähnliche Patienten, um Optionen zu verstehen und Die Behandlung zu planen.** Wenn eine Patientendiagnose angezeigt wird, kann ein Patient Patientenergebnisse und Behandlungspläne für frühere Patienten mit einer ähnlichen Diagnose identifizieren, auch wenn diese Bilddaten enthalten.
- **Bereitstellen einer nahen Ansicht eines Patienten während der Diagnose.** Patienten, insbesondere Teleradiist, haben häufig keinen vollständigen Zugriff auf die Medizinische Geschichte eines Patienten und verwandte Bildverarbeitungsstudien. Durch die FHIR-Integration können diese Daten problemlos bereitgestellt werden, auch für Schützer außerhalb des &trade; lokalen Netzwerks der Organisation.
- **Schließen der Feedbackschleife mit teleradiolos.** Im Idealfall hat ein Obist Zugriff auf die medizinischen Daten eines Krankenhauses, um die Feedbackschleife nach einer Empfehlung zu schließen. Bei Teleradi-Besen ist dies jedoch häufig nicht der Fall. Stattdessen können sie die Feedbackschleife nach der Durchführung einer Diagnose häufig nicht schließen, da sie nach dem ersten Lesezugriff keinen Zugriff auf Patientendaten haben. Ohne (oder eingeschränkten) Zugriff auf medizinische Ergebnisse oder Ergebnisse können sie nicht das Feedback erhalten, das erforderlich ist, um ihre Fähigkeiten zu verbessern. Wie bei teleradiolo: "Nehmen Sie z. B. den Parathyroid. Wir tun mehr als jede andere Praxis im Land, und trotzdem muss ich nach 10000Ergnungsinsenten, um mir mitteilen zu können, was sie tatsächlich gefunden haben. Von den mehr als 500 Studien, die ich jeden Monat durchnische, habe ich nur direktes Feedback zu drei oder vier."  Durch die Integration in FHIR kann eine Organisation ganz einfach ein Tool erstellen, das teleradi versiert direktes Feedback gibt und ihnen dabei hilft, ihre Fähigkeiten zu verbessern und in Zukunft bessere Empfehlungen &trade; zu geben.
- **Schließen der Feedbackschleife für KI-/ML Modelle.** Machine Learning-Modelle sind am besten, wenn feedback aus der realen Welt verwendet werden kann, um ihre Modelle zu verbessern. Allerdings erhalten anbieter von ML-Modellen selten das Feedback, das sie benötigen, um ihre Modelle im Laufe der Zeit zu verbessern. Ein ISV hat dies beispielsweise so ausgedrückt: "Wir verwenden eine Kombination aus Computermodellen und menschlichen Experten, um einen Behandlungsplan für Diebung des Heart zu empfehlen. Wir erhalten jedoch nur selten Feedback von der Meinung, wie genau unser Plan war. Beispielsweise wird häufig eine Stentgröße empfohlen. Wir würden uns freuen, Feedback zu erhalten, wenn unsere Vorhersage richtig war, aber wir hören nur von Kunden, wenn es ein großes Problem mit unseren Empfehlungen gibt." Wie beim Feedback für Teleradi-Benutzer ermöglicht die Integration in FHIR Organisationen die Erstellung eines Mechanismus, um Feedback zur Pipeline für das erneute Training des &trade; Modells zu geben.

## <a name="deploy-dicom-service-to-azure"></a>Bereitstellen des DICOM-Diensts in Azure

Der DICOM-Dienst benötigt ein Azure-Abonnement zum Konfigurieren und Ausführen der erforderlichen Komponenten. Diese Komponenten werden standardmäßig in einer vorhandenen oder neuen Azure-Ressourcengruppe erstellt, um die Verwaltung zu vereinfachen. Darüber hinaus ist Azure Active Directory Konto erforderlich. Für jede Instanz des DICOM-Diensts erstellen wir eine Kombination aus isolierten und mehrinstanzenbasierten Ressourcen.

## <a name="summary"></a>Zusammenfassung

In diesem konzeptuellen Artikel erhalten Sie eine Übersicht über DICOM, die medizinische Bildverarbeitung und den DICOM-Dienst.
 
## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritte mit dem DICOM-Dienst finden Sie unter:

>[!div class="nextstepaction"]
>[Bereitstellen des DICOM-Diensts in Azure](deploy-dicom-services-in-azure.md)

>[!div class="nextstepaction"]
>[Verwenden von &trade; DICOMweb-Standard-APIs mit dem DICOM-Dienst](dicomweb-standard-apis-with-dicom-services.md)
