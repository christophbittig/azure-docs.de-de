---
title: Testen Ihrer App in Azure
description: Erfahren Sie, wie Sie Desktop-/Webanwendungen in einer Dateifreigabe bereitstellen und testen.
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: af9fc320c6f08e0ef4141aac8076e121a2e6292d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131575939"
---
# <a name="test-your-app-in-azure"></a>Testen Ihrer App in Azure 

In diesem Leitfaden erfahren Sie, wie Sie Ihre Anwendung in Azure mithilfe von DevTest Labs testen. Sie verwenden Visual Studio, um Ihre App in einer Azure-Dateifreigabe bereitzustellen. Anschließend greifen Sie über eine Lab-VM auf die Freigabe zu.  

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Eine lokale Arbeitsstation mit [Visual Studio](https://visualstudio.microsoft.com/free-developer-offers/).

- Ein Lab in [DevTest Labs](devtest-lab-overview.md).

- Eine [Azure-VM](devtest-lab-add-vm.md) unter Windows in Ihrem Lab.

- Eine [Dateifreigabe](../storage/files/storage-how-to-create-file-share.md) im vorhandenen Azure-Speicherkonto Ihres Labs. Mit einem Lab wird automatisch ein Speicherkonto erstellt.

- Die [Azure-Dateifreigabe](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share), die auf Ihrer lokalen Arbeitsstation und Lab-VM eingebunden ist.

## <a name="publish-your-app-from-visual-studio"></a>Veröffentlichen der App über Visual Studio

In diesem Abschnitt veröffentlichen Sie Ihre App über Visual Studio in Ihrer Azure-Dateifreigabe.

1. Öffnen Sie Visual Studio, und wählen Sie im Fenster „Start“ die Option **Neues Projekt erstellen** aus.

    :::image type="content" source="./media/test-app-in-azure/launch-visual-studio.png" alt-text="Screenshot: Visual Studio-Startseite":::

1. Wählen Sie **Konsolenanwendung** aus, und klicken Sie dann auf **Weiter**.

    :::image type="content" source="./media/test-app-in-azure/select-console-application.png" alt-text="Screenshot: Option zum Auswählen der Konsolenanwendung":::

1. Übernehmen Sie auf der Seite **Neues Projekt konfigurieren** die Standardwerte, und klicken Sie auf **Weiter**.

1. Übernehmen Sie auf der Seite **Zusätzliche Informationen** die Standardwerte, und klicken Sie auf **Erstellen**.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Erstellen** aus.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Veröffentlichen** aus.

    :::image type="content" source="./media/test-app-in-azure/publish-application.png" alt-text="Screenshot: Option zum Veröffentlichen der Anwendung":::

1. Wählen Sie auf der Seite **Veröffentlichen** die Option **Ordner** aus, und klicken Sie dann auf **Weiter**.

    :::image type="content" source="./media/test-app-in-azure/publish-to-folder.png" alt-text="Screenshot: Option zum Veröffentlichen in einem Ordner":::

1. Wählen Sie für die Option **Bestimmtes Ziel** die Option **Ordner** aus, und klicken Sie dann auf **Weiter**.

1. Wählen Sie für die Option **Speicherort** die Option **Durchsuchen** aus, und wählen Sie die Dateifreigabe aus, die Sie zuvor eingebunden haben. Klicken Sie auf **OK** und dann auf **Fertig stellen**. 

    :::image type="content" source="./media/test-app-in-azure/selecting-file-share.png" alt-text="Screenshot: Option zum Auswählen der Dateifreigabe":::

1. Klicken Sie auf **Veröffentlichen**. Visual Studio erstellt Ihre Anwendung und veröffentlicht sie in Ihrer Dateifreigabe.

    :::image type="content" source="./media/test-app-in-azure/final-publish.png" alt-text="Screenshot: Schaltfläche „Veröffentlichen“":::

## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testen der App auf dem virtuellen Testcomputer im Lab

1. Stellen Sie eine Verbindung mit Ihrer Lab-VM her.

1. Starten Sie auf der VM den **Datei-Explorer**, und wählen Sie **Dieser PC** aus, um die zuvor eingebundene Dateifreigabe zu suchen.

    :::image type="content" source="./media/test-app-in-azure/find-share-on-vm.png" alt-text="Screenshot: Datei-Explorer":::

1. Öffnen Sie die Dateifreigabe, und überprüfen Sie, ob die über Visual Studio bereitgestellte App angezeigt wird. 

    :::image type="content" source="./media/test-app-in-azure/open-file-share.png" alt-text="Screenshot: Inhalt der Dateifreigabe":::

    Sie können nun auf die App zugreifen und sie innerhalb des virtuellen Testcomputers testen, den Sie in Azure erstellt haben.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwenden von virtuellen Computern in einem Lab finden Sie in den folgenden Artikeln. 

- [Hinzufügen eines virtuellen Computers zu einem Lab](devtest-lab-add-vm.md)
- [Neustarten eines virtuellen Computers in einem Lab](devtest-lab-restart-vm.md)
- [Ändern der Größe eines virtuellen Computers in einem Lab](devtest-lab-resize-vm.md)
