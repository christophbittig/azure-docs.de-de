---
title: Verbinden eines klassischen Video Analyzer für Medienkonten mit ARM
description: In diesem Thema wird erläutert, wie Sie einen vorhandenen klassischen kostenpflichtigen Azure Video Analyzer für ein Medienkonto mit einem ARM-basierten Konto verbinden
ms.topic: how-to
ms.author: itnorman
ms.date: 10/19/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14ab4e2b3c95823359dc1f1f88b8368ce58afef9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312593"
---
# <a name="connect-an-existing-classic-paid-video-analyzer-for-media-account-to-arm-based-account"></a>Verbinden eines vorhandenen klassischen kostenpflichtigen Video Analyzer für ein Medienkonto mit einem ARM-basierten Konto  

In diesem Artikel wird beschrieben, wie Sie einen vorhandenen klassischen kostenpflichtigen Azure Video Analyzer für ein Medienkonto mit einem Azure Resource Manager (ARM)-basierten Konto.
Heute ist der Azure Video Analyzer für Medien (früher Video Indexer) ein allgemein verfügbares Produkt, das keine ARM-Ressource in Azure ist.
In diesem Artikel werden Optionen zum Verbinden Ihres **vorhandenen** Video Analyzer für Medienkonten mit [ARM][docs-arm-overview] erläutert.

## <a name="prerequisites"></a>Voraussetzungen

* Unbegrenzter kostenpflichtiger Video Analyzer für Medienkonten (klassisches Konto).
  * Um die Aktion "Verbindung mit ARM herstellen" auszuführen, sollten Sie über die Besitzerberechtigungen für den Video Analyzer für das Medienkonto verfügen.
* Ein Azure-Abonnement.
* Dem Benutzer zugewiesene verwaltete Identität (kann entlang des Flows erstellt werden).

#### <a name="transition-state-and-recommended-steps-before-connecting-a-classic-account-to-be-arm-based"></a>Übergangsstatus und empfohlene Schritte vor dem Herstellen einer Verbindung mit einem klassischen Konto zu ARM-basierter Verbindung

Im Verbindungsprozess ist die Kontoverwaltung mit ARM verbunden, wodurch 30 Tage lang ein Übergangszustand für das Konto ausgelöst wird. In diesem Zustand kann auf ein verbundenes Konto über die API zugegriffen werden, und zwar sowohl [über API Management](https://aka.ms/avam-dev-portal)(klassische Methode) als auch über einen über ARM generierten Zugriffstoken. Der Übergangszustand, der die gesamte Kontoverwaltung zur Verwaltung durch ARM verschiebt, deaktiviert das Feature zum Einladen von Benutzern im Video Analyzer für das Medienportal, da die Kontoverwaltung von [Azure RBAC][docs-rbac-overview]durchgeführt wird. Dies führt dazu, dass alle eingeladenen Benutzer dieses Kontos ihren Zugriff auf das Medienportal des Video Analyzer für Medienkonten verlieren. Dies kann natürlich einfach behoben werden, indem allen diesen Benutzern über Azure RBAC die richtige Rollenzuweisung zugewiesen wird. [So weisen Sie RBAC zu][docs-rbac-assignment]. Nur der Kontobesitzer, der die Verbindungsaktion ausgeführt hat, wird automatisch als Besitzer des verbundenen Kontos zugewiesen. Wenn Benutzer dem Konto nach 30 Tagen nicht über Azure RBAC hinzugefügt werden, verlieren sie den Zugriff auch über die API, da nach dem Ende des Übergangszustands kein Benutzer mehr einen gültigen Zugriffstoken über APIM (klassische Methode) generieren kann, sondern nur noch über ARM. Azure RBAC ist die ausschließliche Methode zum Verwalten der rollenbasierten Zugriffssteuerung für das Konto.

> [!NOTE]
> Wenn eingeladene Benutzer vorhanden sind, die Ihren Zugriff vor dem Ende des Übergangszustands von 30 Tagen entfernen möchten, sollten Sie dies über die Kontoeinstellungen im Azure Video Analyzer für Medienkonten tun, **bevor** Sie das Konto mit ARM verbinden 

## <a name="get-started"></a>Erste Schritte

### <a name="browse-to-video-analyzer-for-media-portal"></a>[Video Analyzer für das Media-Portal](https://aka.ms/vi-portal-link) durchsuchen

1. Anmelden mit Ihrem AAD Konto.
1. Klicken Sie auf der oberen rechten Leiste auf *Benutzerkonto*, um die Kontoliste im seitlichen Bereich zu öffnen.
3. Wählen Sie den klassischen Video Analyzer für das Medienkonto aus, das Sie mit ARM verbinden möchten (klassische Konten werden mit einem *klassischen Tag* gekennzeichnet).
4. Klicken Sie auf **Einstellungen**.

  ![Kontoeinstellungen](media/connect-classic-account-to-arm/user-account-settings.png)
   
5. Klicken Sie auf **Verbinden zu einem ARM-basierten Konto**.

  ![Verbindungsschaltflächen-Portal](media/connect-classic-account-to-arm/connect-button.png)

7. Melden Sie sich beim Azure-Portal an.
8. Das Blatt Video Analyzer für Medien erstellen wird geöffnet
10. Geben Sie im Abschnitt zum **Erstellen eines Video Analyzer-Kontos** die erforderlichen Werte ein.
    * Wenn Sie die Schritte befolgt haben, sollten die Felder automatisch aufgefüllt werden. Überprüfen Sie die zulässigen Werte.

 ![Herstellen einer Verbindung zu ARM](media/connect-classic-account-to-arm/connect-blade-new.png)

 | Name | Beschreibung |
 | ---|---|
 |**Abonnement**| Das Abonnement enthält derzeit das klassische Konto und andere zugehörige Ressourcen, z. B. die Medienservices.|
 |**Ressourcengruppe**|Wählen Sie eine vorhandene Ressource aus, oder erstellen Sie eine neue. Die Ressourcengruppe muss sich am gleichen Standort befinden wie das klassische Konto, mit dem verbunden wird|
 |**Video Analyzer für Medienkonto** (Optionsfeld)| Wählen Sie *"Connecting an existing classic account" (Herstellen einer Verbindung mit einem vorhandenen klassischen Konto) aus.*|
 |**Vorhandene Konto-ID**| Geben Sie die ID des vorhandenen Klassischen Video Analyzer für Medienkonten ein.|
 |**Ressourcenname**|Geben Sie den Namen des neuen Video Analyzer für Medienkonten ein. Der Standardwert ist derselbe Name, den das Konto als klassisches hatte.|
 |**Location**|Die geografische Region kann während des Verbindungsvorgangs geändert werden. Das verbundene Konto muss sich in derselben Region befinden. |
 |**Name des Media Services-Kontos**|Der ursprüngliche Medienservices-Kontoname, der dem klassischen Konto zugeordnet war.|
 |**Benutzerseitig zugewiesene verwaltete Identität**|Wählen Sie eine dem Benutzer zugewiesene verwaltete Identität aus, oder erstellen Sie eine neue. Das Video Analyzer für Medienkonten verwendet sie für den Zugriff auf die Medienservices. Der verwalteten Identität für die Benutzerzuweisung wird die Rolle als Mitwirkender für das Medienservice-Konto zugewiesen.|

1. Klicken Sie unten im Formular auf **Überprüfen + erstellen**.

### <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [ein Video mit C# hochladen](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/ApiUsage/ArmBased).
  
<!-- links -->
[docs-arm-overview]: ../../azure-resource-manager/management/overview.md
[docs-rbac-overview]: ../../role-based-access-control/overview.md
[docs-rbac-assignment]: ../../role-based-access-control/role-assignments-portal.md
