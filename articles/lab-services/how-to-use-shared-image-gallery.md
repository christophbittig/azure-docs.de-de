---
title: Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Labkonto für die Verwendung eines Katalogs mit freigegebenen Images konfigurieren, sodass ein Benutzer ein Image für andere freigeben und ein anderer Benutzer mithilfe dieses Images eine Vorlage für virtuelle Computer im Lab erstellen kann.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: caba089d5f3f0f9ec50f575a06ce9fad1bf8ef8f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339875"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services

Ein Image enthält das Betriebssystem, Softwareanwendungen und Einstellungen für die Installation auf einer VM.  Es gibt zwei Arten von Images, die Sie beim Einrichten eines neuen Labs verwenden können:
-   Marketplace-Images, die von Microsoft für die Verwendung in Azure vordefiniert wurden.  Auf diesen Images ist entweder Windows oder Linux installiert, und sie können auch Softwareanwendungen enthalten.  Beispielsweise sind auf dem [Data Science Virtual Machine-Image (DSVM)](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) Deep Learning-Frameworks und -Tools installiert.
-   Benutzerdefinierte Images, die von der IT-Abteilung Ihrer Einrichtung und/oder anderen Lehrkräften erstellt werden.  Sie können sowohl benutzerdefinierte Windows- als auch Linux-Images erstellen und haben die Möglichkeit, Anwendungen von Microsoft und Drittanbietern zu installieren, die Ihren individuellen Anforderungen entsprechen.  Sie können außerdem Dateien hinzufügen, Anwendungseinstellungen ändern und vieles mehr.

In diesem Artikel wird erläutert, wie Lehrer/Dozenten und Lab-Administratoren ein benutzerdefiniertes Image anhand einer Vorlagen-VM erstellen und in einem [Katalog mit freigegebenen Images](../virtual-machines/shared-image-galleries.md) speichern können, damit es von anderen Benutzern zum Erstellen von Labs genutzt werden kann.

> [!IMPORTANT]
> Bei Verwendung einer Shared Image Gallery unterstützt Azure Lab Services nur Images mit weniger als 128 GB Betriebssystem-Speicherplatz. Images mit mehr als 128 GB Speicherplatz auf dem Datenträger oder mehreren Datenträgern werden während der Erstellung des Labs nicht in der Liste der Images virtueller Computer angezeigt.

## <a name="scenarios"></a>Szenarien
Im Folgenden sehen Sie ein paar der Szenarien, die von diesem Feature unterstützt werden: 

- Ein Lab-Kontoadministrator fügt dem Lab-Konto einen Katalog mit freigegebenen Images hinzu und lädt ein Image in den Katalog mit freigegebenen Images außerhalb des Kontexts eines Labs hoch. Danach können Lab-Ersteller dieses Image aus dem Katalog mit freigegebenen Images zum Erstellen von Labs verwenden. 
- Ein Lab-Kontoadministrator fügt dem Lab-Konto einen Katalogs mit freigegebenen Images an. Ein Lab-Ersteller (Kursleiter) speichert das angepasste Image seines Labs im Katalog mit freigegebenen Images. Andere Lab-Ersteller können dann dieses Image im Katalog mit freigegebenen Images auswählen, um eine Vorlage für ihre Labs zu erstellen. 

    Wenn ein Image in eine Shared Image Gallery gespeichert wird, repliziert Azure Lab Services das gespeicherte Image in andere Regionen, die in derselben [Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) verfügbar sind. Dadurch wird sichergestellt, dass das Image für Labs verfügbar ist, die in anderen Regionen innerhalb derselben Geografie erstellt wurden. Das Speichern von Images in einer Shared Image Gallery führt zu zusätzlichen Kosten, einschließlich der Kosten für alle replizierten Images. Diese Kosten entstehen zusätzlich zu den Kosten für die Azure Lab Services-Nutzung. Weitere Informationen zu Preisen der Shared Image Gallery finden Sie unter [Shared Image Gallery – Preise](../virtual-machines/shared-image-galleries.md#billing).
    
## <a name="prerequisites"></a>Voraussetzungen
- Erstellen Sie mithilfe von [Azure PowerShell](../virtual-machines/shared-images-powershell.md) oder über die [Azure-Befehlszeilenschnittstelle](../virtual-machines/shared-images-cli.md) einen Katalog mit freigegebenen Images.
- Sie haben den Katalog mit freigegebenen Images an das Lab-Konto angefügt. Schrittweise Anweisungen finden Sie unter [Anfügen oder Trennen eines Katalogs mit freigegebenen Images](how-to-attach-detach-shared-image-gallery.md).

## <a name="save-an-image-to-the-shared-image-gallery"></a>Speichern eines Images im Katalog mit freigegebenen Images
Nachdem ein Katalog mit freigegebenen Images angefügt wurde, kann ein Lab-Kontoadministrator oder ein Lehrer/Dozent ein Image im Katalog mit freigegebenen Images speichern, das dann von anderen Lehrern/Dozenten wiederverwendet werden kann. 

1. Wählen Sie auf der Seite **Vorlage** für das Lab auf der Symbolleiste die Option **Export to Shared Image Gallery** (Nach Shared Image Gallery exportieren) aus.

    ![Schaltfläche „Image speichern“](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Geben Sie im Dialogfeld **Export to Shared Image Gallery** (Nach Shared Image Gallery exportieren) einen **Namen für das Image** ein, und wählen Sie **Exportieren** aus. 

    ![Dialogfeld „Export to Shared Image Gallery“ (Nach Shared Image Gallery exportieren)](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)

3. Der Fortschritt dieses Vorgangs wird auf der Seite **Vorlage** angezeigt. Dieser Vorgang kann einige Zeit dauern. 

    ![Aktiver Exportvorgang](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. War der Exportvorgang erfolgreich, wird die folgende Meldung angezeigt:

    ![Export erfolgreich](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Nachdem Sie das Image in dem Katalog mit freigegebenen Images gespeichert haben, können Sie dieses Image beim Erstellen eines anderen Labs aus dem Katalog verwenden. Sie können ein Image auch außerhalb des Kontexts eines Labs in den Katalog mit freigegebenen Images hochladen. Weitere Informationen finden Sie unter

    - [Übersicht über Shared Image Gallery](../virtual-machines/shared-images-powershell.md)
    - [Empfohlene Vorgehensweisen zum Erstellen benutzerdefinierter Images](approaches-for-custom-image-creation.md)

    > [!IMPORTANT]
    > Wenn Sie in Azure Lab Services ein [Vorlagenimage eines Labs in einem Katalog mit freigegebenen Images speichern](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery), wird das Image als ein **spezialisiertes Image** in den Katalog hochgeladen. Bei [spezialisierten Images](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) werden computerspezifische Informationen und Benutzerprofile beibehalten. Sie können weiterhin ein generalisiertes Image direkt in den Katalog außerhalb von Azure Lab Services hochladen.    

## <a name="use-a-custom-image-from-the-shared-image-gallery"></a>Verwenden eines benutzerdefinierten Images aus Shared Image Gallery
Lehrer/Dozenten können ein benutzerdefiniertes Image, das in der Shared Image Gallery verfügbar ist, für die Vorlagen-VM auswählen, die beim Einrichten eines neuen Labs erstellt wird.

![Verwenden eines VM-Images aus dem Katalog](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Sie können eine Vorlagen-VM basierend auf **generalisierten** und **spezialisierten** Images in Azure Lab Services erstellen.

### <a name="resave-a-custom-image-to-shared-image-gallery"></a>Erneutes Speichern eines benutzerdefinierten Images in Shared Image Gallery

Nachdem Sie ein Lab aus einem benutzerdefinierten Image in einer Shared Image Gallery erstellt haben, können Sie mithilfe der Vorlagen-VM Änderungen am Image vornehmen und das Image in Shared Image Gallery erneut exportieren.  Beim erneuten Exportieren haben Sie die Möglichkeit, ein neues Image zu erstellen oder das ursprüngliche Image zu aktualisieren. 

 ![Dialogfeld „Erneut in Shared Image Gallery exportieren“](./media/how-to-use-shared-image-gallery/reexport-to-shared-image-gallery-dialog.png) 

Wenn Sie **Neues Image erstellen** auswählen, wird eine neue [Imagedefinition](../virtual-machines/shared-image-galleries.md#image-definitions) erstellt.  Dadurch können Sie ein vollständig neues benutzerdefiniertes Image speichern, ohne das ursprüngliche benutzerdefinierte Image zu ändern, das bereits in Shared Image Gallery vorhanden ist.

Wenn Sie stattdessen **Vorhandenes Image aktualisieren** auswählen, wird die Definition des ursprünglichen benutzerdefinierten Images mit einer neuen [Version](../virtual-machines/shared-image-galleries.md#image-versions) aktualisiert.  Lab Services verwendet automatisch die neueste Version, wenn das nächste Mal ein Lab mit dem benutzerdefinierten Image erstellt wird.

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Einrichten einer Shared Image Gallery durch Anfügen des Images an ein bzw. Trennen von einem Lab-Konto finden Sie unter [Anfügen an eine und Trennen von einer Shared Image Gallery](how-to-attach-detach-shared-image-gallery.md).

Weitere Möglichkeiten, benutzerdefinierte Images außerhalb des Kontexts eines Labs in einen Katalog mit freigegebene Images einzufügen, finden Sie unter [Empfohlene Vorgehensweisen zum Erstellen benutzerdefinierter Images](approaches-for-custom-image-creation.md).

Weitere allgemeine Informationen zu Katalogen mit freigegebenen Images finden Sie in der [Übersicht über den Katalog mit freigegebenen Images](../virtual-machines/shared-image-galleries.md).