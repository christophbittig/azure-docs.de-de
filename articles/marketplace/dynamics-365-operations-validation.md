---
title: Funktionale Überprüfung eines Dynamics 365 for Operations-Angebots in Microsoft AppSource (Azure Marketplace)
description: Führen Sie eine funktionale Überprüfung eines Dynamics 365 for Operations-Angebots in Microsoft AppSource (Azure Marketplace) durch.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: vamahtan
ms.author: vamahtan
ms.date: 05/19/2021
ms.openlocfilehash: 05b7c223823522b7c511df7cfa66c7dffa2a8b4b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112281285"
---
# <a name="dynamics-365-for-operations-functional-validation"></a>Funktionale Überprüfung eines Dynamics 365 for Operations-Angebots

Die Veröffentlichung eines Angebots für Dynamics 365 for Operations in [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002) erfordert zwei funktionale Überprüfungen:

- Hochladen eines Demovideos der Dynamics 365-Umgebung zur Beschreibung der grundlegenden Funktionen
- Bereitstellen von Screenshots zur Veranschaulichung der [LCS-Umgebung](https://lcs.dynamics.com/) (Lifecycle Services) der Lösung

> [!NOTE]
> Spätere Veröffentlichungen von Neuzertifizierungen erfordern keine Veranschaulichung. Weitere Informationen finden Sie im [AppSource-Richtliniendokument](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Überprüfen

Es gibt zwei Optionen für die funktionale Überprüfung:

- Sie stellen uns die [LCS](https://lcs.dynamics.com/)-Umgebung und die Lösung in einem 30-minütigen Konferenzgespräch während unserer Geschäftszeiten (PST) vor.
- Navigieren Sie in Partner Center zum [kommerziellen Marketplace](https://go.microsoft.com/fwlink/?linkid=2165290), und laden Sie auf der Registerkarte „Ergänzender Inhalt“ eine Demovideo-URL und LCS-Screenshots hoch.

Das Microsoft-Zertifizierungsteam überprüft das Video und die Dateien und genehmigt dann entweder die Lösung oder sendet eine E-Mail mit den nächsten Schritten.

### <a name="option-1-30-minute-conference-call"></a>Option 1: 30-minütiges Konferenzgespräch

Wenden Sie sich für einen Termin für das finale Überprüfungsgespräch an [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com), und halten Sie den Namen Ihres Angebots und potenzielle Termine zwischen 8:00 und 17:00 Uhr PST bereit.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Option 2: Hochladen eines Demovideos und von LCS-Screenshots

1. Nehmen Sie ein Video auf, und laden Sie die Adresse der Hostingsite Ihrer Wahl hoch. Beachten Sie diese Vorgaben:

    - Verfügbarkeit für das Microsoft-Zertifizierungsteam
    - kürzer als 20 Minuten
    - enthält bis zu drei wichtige Kernfunktionen Ihrer Lösung in der Dynamics 365-Umgebung

    > [!NOTE]
    > Sie können ein vorhandenes Marketingvideo verwenden, wenn es die Vorgaben erfüllt.

2. Erstellen Sie die folgenden Screenshots der [LCS](https://lcs.dynamics.com/)-Umgebung für das Angebot oder die Lösung, die Sie veröffentlichen möchten. Die Screenshots müssen scharf genug sein, damit das Zertifizierungsteam den Text darauf lesen kann. Speichern Sie die Screenshots als JPG-Dateien. Sie müssen [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) möglicherweise die Berechtigung für Ihre LCS-Umgebung erteilen, damit wir das Setup auf diese Weise und nicht nur anhand der Screenshots überprüfen können.

    1. Navigieren Sie zu **LCS** > **Business Process Modeler** > **Project library** (LCS > Geschäftsprozessmodellierer > Projektbibliothek). Erstellen Sie bei allen Prozessschritten Screenshots. Beachten Sie, dass die Spalten **Diagramme** und **Überprüft** wie hier gezeigt ebenfalls erfasst werden:

       :::image type="content" source="media/dynamics-365-operations/project-library.png" alt-text="Abbildung des Projektbibliothekfensters":::

      2. Wechseln Sie zu **LCS** > **Solution Management** > **Test Solution Package** (LCS > Lösungsverwaltung > Lösungspaket testen). Erstellen Sie Screenshots, die die Paketübersicht und die in den folgenden Beispielen gezeigten Inhalte enthalten:

    | Feld | Image |
    | --- | --- |
    | Paketübersicht | [![Screenshot: Fenster „Paketübersicht“](media/dynamics-365-operations/package-overview-45.png)](media/dynamics-365-operations/package-overview.png#lightbox) |
    | <ul><li>Lösungsgenehmiger</li></ul> | [![Bildschirm „Paketübersicht“](media/dynamics-365-operations/solution-approvers-45.png)](media/dynamics-365-operations/solution-approvers.png#lightbox) |
    | Paketinhalt<ul><li>Modell</li><li>Bereitstellbares Softwarepaket</li></ul> | [![Paketinhalt (Bildschirm 1)](media/dynamics-365-operations/package-contents-1-45.png)](media/dynamics-365-operations/package-contents-1.png#lightbox) |
    | <ul><li>GER-Konfiguration</li><li>Datenbanksicherung</li></ul><br>Artefakte sind im Abschnitt **GER-Konfiguration** nicht erforderlich. | [![Paketinhalt (Bildschirm 2)](media/dynamics-365-operations/package-contents-2-45.png)](media/dynamics-365-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI-Berichtsmodell</li><li>BPM-Artefakt</li></ul><br>Im Abschnitt **Power BI** sind keine Artefakte erforderlich. | [![Paketinhalt (Bildschirm 3)](media/dynamics-365-operations/package-contents-3-45.png)](media/dynamics-365-operations/package-contents-3.png#lightbox) |
    | <ul><li>Prozessdatenpaket</li><li>Solution license agreement and privacy policy (Lösungslizenzvereinbarung und Datenschutzrichtlinie)</li></ul><br>Die Abschnitte **GER-Konfiguration** und **Power BI-Berichtsmodell** sind bei Operations-Angeboten optional. | [![Paketinhalt (Bildschirm 4)](media/dynamics-365-operations/package-contents-4-45.png)](media/dynamics-365-operations/package-contents-4.png#lightbox) |

    Weitere Informationen zu den einzelnen Abschnitten des LCS-Portals finden Sie im [LCS-Benutzerhandbuch](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide).

3. Laden Sie das Dokument in Partner Center hoch.

    1. Erstellen Sie ein Textdokument, das die Adresse des Demovideos und die Screenshots enthält, oder speichern Sie die Screenshots als separate JPG-Dateien.
    2. Fügen Sie in [Partner Center](https://go.microsoft.com/fwlink/?linkid=2165290) auf der Registerkarte **Ergänzende Inhalte** des Angebots den Text und die Bilder zu einer ZIP-Datei hinzu.

    [![Abbildung des Projektbibliothekfensters](media/dynamics-365-operations/supplemental-content.png)](media/dynamics-365-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen eines Angebots finden Sie unter [Planen eines Microsoft Dynamics 365-Angebots](marketplace-dynamics-365.md).
- Wenn Sie mit der Erstellung Ihres Angebots fertig sind, können Sie es [überprüfen und veröffentlichen](dynamics-365-review-publish.md).
