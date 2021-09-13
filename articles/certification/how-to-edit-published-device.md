---
title: Bearbeiten Ihres veröffentlichten Azure Certified Device
description: Eine Anleitung zum Bearbeiten Ihrer Geräteinformationen, nachdem Sie Ihr Gerät mithilfe des Azure Certified Device-Programms zertifiziert und veröffentlicht haben.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 07/13/2021
ms.custom: template-how-to
ms.openlocfilehash: 82993fb11ad6f2d1fde3d997494e5fc9151673e5
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731610"
---
# <a name="edit-your-published-device"></a>Bearbeiten Ihres veröffentlichten Geräts

Nachdem Ihr Gerät zertifiziert und im Azure Certified Device-Katalog veröffentlicht wurde, müssen Sie möglicherweise Ihre Gerätedetails aktualisieren. Der Grund hierfür kann ein Update der Liste Ihrer Vertriebspartner, geänderte URLs auf der Kaufseite oder Aktualisierungen der Hardwarespezifikationen sein (wie etwa die Betriebssystemversion oder eine neu hinzugefügte Komponente). Möglicherweise müssen Sie auch Ihr IoT Plug & Play-Gerätemodell aktualisieren, über das Sie Ihr Projekt ursprünglich in das Modellrepository hochgeladen haben.


## <a name="prerequisites"></a>Voraussetzungen

- Sie sollten angemeldet sein und über ein **genehmigtes** Projekt für Ihr Gerät im [Azure Certified Device-Portal](https://certify.azure.com) verfügen. Wenn Sie nicht über ein zertifiziertes Gerät verfügen, können Sie dieses [Tutorial](tutorial-01-creating-your-project.md) lesen, um den Einstieg zu finden.


## <a name="editing-your-published-project-information"></a>Bearbeiten der Informationen zu Ihrem veröffentlichten Projekt

In der Projektübersicht sollten Sie feststellen, dass Ihr Projekt im schreibgeschützten Modus ist, da es bereits geprüft und akzeptiert wurde. Damit Sie Änderungen vornehmen können, müssen Sie einen Bearbeitungsvorgang für Ihr Projekt anfordern und das Update erneut vom Azure-Zertifizierungsteam genehmigen lassen.

1. Klicken Sie oben auf der Seite auf die Schaltfläche `Request Metadata Edit`.  

    ![Anfordern eines Metadatenupdates](./media/images/request-metadata-edit.png)

1. Bestätigen Sie die Benachrichtigung auf der Seite, dass Sie Ihr Produkt nach der Bearbeitung zur Überprüfung einreichen müssen.
    > [!NOTE]
    > Durch die Bestätigung dieser Bearbeitung entfernen Sie Ihr Gerät **nicht** aus dem Azure Certified Device-Katalog, wenn es bereits veröffentlicht wurde. Ihre bisherige Produktversion bleibt im Katalog, bis Sie Ihr Gerät erneut veröffentlicht haben.
    > Außerdem müssen Sie den Abschnitt zum Verbinden und Testen im Portal nicht wiederholen.

1. Nach dem Bestätigen dieser Warnmeldung können Sie Ihre Gerätedetails bearbeiten. Achten Sie unbedingt darauf, im `Comments for Reviewer`-Abschnitt von `Device Details` eine Anmerkung anzugeben, was geändert wurde.

    ![Hinweis zur Metadatenbearbeitung](./media/images/edit-notes.png)

1. Klicken Sie auf der Zusammenfassungsseite des Projekts auf `Submit for review`, um Ihre Änderungen erneut vom Azure Certification-Team genehmigen zu lassen.
1. Nachdem die Änderungen überprüft und genehmigt wurden, können Sie Ihre Änderungen mithilfe des Portals erneut im Katalog veröffentlichen (Mehr dazu in unserem [Tutorial](./tutorial-04-publishing-your-device.md)).

## <a name="editing-your-iot-plug-and-play-device-model"></a>Bearbeiten Ihres IoT Plug & Play-Gerätemodells

Nachdem Sie Ihr Gerätemodell an das öffentliche Modellrepository übermittelt haben, kann es nicht entfernt werden. Wenn Sie das Gerätemodell aktualisieren und Ihr zertifiziertes Gerät wieder mit dem neuen Modell verknüpfen möchten, müssen Sie Ihr Gerät **erneut als neues Projekt zertifizieren**. Wenn Sie diesen Schritt ausführen, hinterlassen Sie einen Kommentar im Abschnitt „Kommentare für Reviewer“, damit das Zertifizierungsteam Ihren alten Geräteeintrag entfernen kann.

## <a name="next-steps"></a>Nächste Schritte

Sie haben Ihr Gerät im Azure Certified Device-Katalog jetzt erfolgreich bearbeitet. Sie können Ihre Änderungen im Katalog überprüfen oder ein weiteres Gerät zertifizieren!
- [Azure Certified Device-Katalog](https://devicecatalog.azure.com/)
- [Mit der Zertifizierung eines Geräts beginnen](./tutorial-01-creating-your-project.md)
