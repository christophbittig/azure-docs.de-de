---
title: Löschen Ihrer Sprach-Assistent-Anwendung für Azure Percept Audio
description: In diesem Artikel wird veranschaulicht, wie Sie eine zuvor erstellte Sprach-Assistent-Anwendung löschen.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 0b62ba69695606e04e7044109818c18d30ca21c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355325"
---
# <a name="delete-your-voice-assistant-application"></a>Löschen Ihrer Sprach-Assistent-Anwendung

In dieser Anleitung wird veranschaulicht, wie Sie eine Sprach-Assistent-Anwendung von Ihrem Azure Percept Audio-Gerät löschen.

## <a name="prerequisites"></a>Voraussetzungen

- [Bereits erstellte Sprach-Assistent-Anwendung](./tutorial-no-code-speech.md)
- Ihr Azure Percept DK ist eingeschaltet, und das Azure Percept Audio-Zubehör ist über ein USB-Kabel angeschlossen

## <a name="remove-all-voice-assistant-resources-from-the-azure-portal"></a>Entfernen aller Sprach-Assistent-Ressourcen aus dem Azure-Portal

Wenn Sie mit der Arbeit an Ihrer Sprach-Assistent-Anwendung fertig sind, ist es ratsam, die während der Anwendungserstellung bereitgestellten Sprachressourcen zu bereinigen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Menü auf der linken Seite die Option **Ressourcengruppen** aus, oder geben Sie ihren Namen in der Suchleiste ein.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Screenshot: Startseite des Azure-Portals mit linkem Menübereich und Ressourcengruppen":::

1. Wählen Sie Ihre Ressourcengruppe aus.

1. Wählen Sie alle sechs Ressourcen aus, die Ihr Anwendungspräfix enthalten, und dann im oberen Menübereich das Symbol **Löschen**.

    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Screenshot: Zum Löschen ausgewählte Speech-Ressourcen":::

1. Geben Sie zum Bestätigen des Löschvorgangs im entsprechenden Feld **Ja** ein, vergewissern Sie sich, dass Sie die richtigen Ressourcen ausgewählt haben, und wählen Sie **Löschen** aus.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Screenshot: Bestätigungsfenster für den Löschvorgang":::

> [!WARNING]
> Hierbei werden alle benutzerdefinierten Schlüsselwörter entfernt, die mit den von Ihnen gelöschten Sprachressourcen erstellt wurden, und die Demo für den Sprach-Assistenten funktioniert nicht mehr.


## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihre Sprach-Assistent-Anwendung nun entfernt haben, können Sie mit den folgenden Tutorials versuchen, andere Anwendungen in Ihrem Azure Percept DK zu erstellen.
- [Erstellen einer Vision-Lösung ohne Code](./tutorial-nocode-vision.md)
- [Erstellen einer Sprach-Assistent-Anwendung ohne Code](./tutorial-no-code-speech.md)


