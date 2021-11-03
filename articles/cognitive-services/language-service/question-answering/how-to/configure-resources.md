---
title: Konfigurieren des Frageantwortdiensts
description: In diesem Dokument werden erweiterte Konfigurationen für benutzerdefinierte Ressourcen beschrieben, die für die Beantwortung von Fragen aktiviert sind.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2eae7e38422aa597c9b093dbefaef553cb084d09
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095516"
---
# <a name="configure-custom-question-answering-enabled-resources"></a>Konfigurieren benutzerdefinierter Ressourcen, für die die Beantwortung von Fragen aktiviert ist

Sie können die Frageantwort so konfigurieren, dass eine andere Cognitive Search-Ressource verwendet wird.

## <a name="change-cognitive-search-resource"></a>Änderung der Cognitive Search-Ressource

> [!WARNING]
> Wenn Sie den Ihrer Sprachressource zugeordneten Azure Search-Dienst ändern, verlieren Sie den Zugriff auf alle bereits darin vorhandenen Projekte/Wissensdatenbanken. Stellen Sie sicher, dass Sie die vorhandenen Projekte exportieren, bevor Sie den Azure Search-Dienst ändern.

Wenn Sie eine Sprachressource und deren Abhängigkeiten (z. B. Search) im Azure-Portal erstellen, wird automatisch ein Suchdienst erstellt und mit der Sprachressource verknüpft. Nachdem diese Ressourcen erstellt wurden, können Sie die Suchressource auf der Registerkarte **Features** aktualisieren.

1.  Wechseln Sie im Azure-Portal zu Ihrer Sprachressource.

2.  Wählen Sie **Features** und den Azure Cognitive Search-Dienst aus, den Sie mit Ihrer Sprachressource verknüpfen möchten.

> [!div class="mx-imgBorder"]
> ![Hinzufügen von QnA zu Textanalyse](../media/configure-resources/update-custom-feature.png)

3.  Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Verschlüsselung ruhender Daten](./encrypt-data-at-rest.md)
