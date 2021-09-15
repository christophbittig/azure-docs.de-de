---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 6dea9948a85278c236c704562d194f18c962683b
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778476"
---
1. Geben Sie im Portal auf der Leiste **Ressourcen suchen** die Zeichenfolge **Virtual WAN** in das Suchfeld ein, und drücken Sie die **EINGABETASTE**.

1. Wählen Sie in den Ergebnissen **Virtual WAN** aus. Wählen Sie auf der Seite „Virtual WAN“ die Option **+ Erstellen** aus, um die Seite **WAN erstellen** zu öffnen.

1. Füllen Sie auf der Seite **WAN erstellen** auf der Registerkarte **Grundlagen** die Felder aus. Ändern Sie die Beispielwerte, um sie an Ihre Umgebung anzupassen.

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Screenshot: Bereich „WAN erstellen“ mit ausgewählter Registerkarte „Grundlagen“":::

   * **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten.
   * **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe, oder verwenden Sie eine vorhandene.
   * **Ressourcengruppenstandort**: Wählen Sie in der Dropdownliste einen Ressourcengruppenstandort aus. Ein WAN ist eine globale Ressource, die nicht in einer bestimmten Region angeordnet ist. Sie müssen aber eine Region auswählen, damit Sie die von Ihnen erstellte WAN-Ressource verwalten und finden können.
   * **Name**: Geben Sie den für Ihr virtuelles WAN gewünschten Namen ein.
   * **Typ**: Basic oder Standard. Wählen Sie **Standard** aus. Wenn Sie „Basic“ wählen, müssen Sie wissen, dass virtuelle Basic-WANs nur Basic-Hubs enthalten können. Basic-Hubs können nur für Site-to-Site-Verbindungen verwendet werden.

1. Nachdem Sie die Felder ausgefüllt haben, wählen Sie unten auf der Seite **Überprüfen und erstellen** aus.

1. Wählen Sie nach bestandener Überprüfung **Erstellen** aus, um das virtuelle WAN zu erstellen.
