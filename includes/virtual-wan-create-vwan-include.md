---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 2403ce53175457d0a433f9a6a9310f93f0165a0d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122321894"
---
Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

1. Geben Sie im Portal in der Leiste **Ressourcen suchen** die Zeichenfolge **Virtual WAN** in das Suchfeld ein, und drücken Sie die **EINGABETASTE**.

1. Wählen Sie in den Ergebnissen **Virtual WAN** aus. Wählen Sie auf der Seite „Virtual WAN“ die Option **+ Erstellen** aus, um die Seite „WAN erstellen“ zu öffnen.

1. Füllen Sie auf der Seite **WAN erstellen** auf der Registerkarte **Grundlagen** die Felder aus. Sie können die Beispielwerte ändern, um sie an Ihre Umgebung anzupassen.

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Screenshot: Bereich „WAN erstellen“ mit ausgewählter Registerkarte „Grundlagen“":::

   * **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten.
   * **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe, oder verwenden Sie eine vorhandene.
   * **Ressourcengruppenstandort**: Wählen Sie in der Dropdownliste einen Ressourcengruppenstandort aus. Ein WAN ist eine globale Ressource, die nicht in einer bestimmten Region angeordnet ist. Sie müssen aber eine Region auswählen, damit Sie die von Ihnen erstellte WAN-Ressource verwalten und finden können.
   * **Name**: Geben Sie den für Ihr WAN gewünschten Namen ein.
   * **Typ**: Basic oder Standard. Wählen Sie **Standard** aus. Wenn Sie „Basic“ wählen, müssen Sie wissen, dass virtuelle Basic-WANs nur Basic-Hubs enthalten können. Basic-Hubs können nur für Site-to-Site-Verbindungen verwendet werden.

1. Nachdem Sie die Felder ausgefüllt haben, wählen Sie unten auf der Seite **Überprüfen und erstellen** aus.

1. Klicken Sie nach bestandenen Überprüfung auf **Erstellen**, um das virtuelle WAN zu erstellen.
