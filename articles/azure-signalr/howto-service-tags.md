---
title: Verwenden von Diensttags
titleSuffix: Azure SignalR Service
description: Verwenden von Diensttags für das Zulassen von ausgehendem Datenverkehr zu Ihrer Azure SignalR Service-Instanz
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: fdb87dab5f2dc105da28ca097f81ff83c0dce3b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339495"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Verwenden von Diensttags für Azure SignalR Service

Sie können [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) für Azure SignalR Service verwenden, wenn Sie [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#network-security-groups) konfigurieren. So können Sie Sicherheitsregeln für ein-/ausgehenden Datenverkehr für Azure SignalR Service-Endpunkte definieren, ohne dass dafür IP-Adressen hartcodiert werden müssen.

Azure SignalR Service verwaltet diese Diensttags. Sie können kein eigenes Diensttag erstellen oder ein vorhandenes Tag ändern. Microsoft verwaltet diese Adresspräfixe, die dem Diensttag entsprechen, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

> [!Note]
> Ab dem 15. August 2021 unterstützt Azure SignalR Service bidirektionale Diensttags für ein- und ausgehenden Datenverkehr.

## <a name="use-service-tag-on-portal"></a>Verwenden von Diensttags im Portal

### <a name="configure-outbound-traffic"></a>Konfigurieren von ausgehendem Datenverkehr

Sie können ausgehenden Datenverkehr zu Azure SignalR Service zulassen, indem Sie eine neue Sicherheitsregel für ausgehenden Datenverkehr im Netzwerk festlegen:

1. Rufen Sie die Netzwerksicherheitsgruppe auf.

1. Klicken Sie auf das Einstellungsmenü namens **Ausgangssicherheitsregeln**.

1. Klicken Sie oben auf die Schaltfläche **+ Hinzufügen**.

1. Klicken Sie unter **Ziel** auf die Option **Diensttag**.

1. Klicken Sie unter **Zieldiensttag** auf **AzureSignalR**.

1. Geben Sie für **Zielportbereiche** den Port **443** ein.

    ![Erstellen einer Ausgangssicherheitsregel](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Nehmen Sie an den anderen Feldern entsprechend Ihrer Anforderungen Anpassungen vor.

1. Klicken Sie auf **Hinzufügen**.

### <a name="configure-inbound-traffic"></a>Konfigurieren von eingehendem Datenverkehr

Wenn Sie Upstreams besitzen, können Sie auch eingehenden Datenverkehr von Azure SignalR Service zulassen, indem Sie eine neue Netzwerksicherheitsregel für eingehenden Datenverkehr hinzufügen:

1. Rufen Sie die Netzwerksicherheitsgruppe auf.

1. Klicken Sie auf das Menü mit den Einstellungen namens **Eingangssicherheitsregeln**.

1. Klicken Sie oben auf die Schaltfläche **+ Hinzufügen**.

1. Wählen Sie unter **Quelle** die Option **Diensttag** aus.

1. Wählen Sie unter **Quelldiensttag** die Option **AzureSignalR** aus.

1. Tragen Sie **\* *_ in _* Quellportbereiche** ein.

   :::image type="content" alt-text="Erstellen einer Eingangssicherheitsregel" source="media/howto-service-tags/portal-add-inbound-security-rule.png" :::


1. Nehmen Sie an den anderen Feldern entsprechend Ihrer Anforderungen Anpassungen vor.

1. Klicken Sie auf **Hinzufügen**.

## <a name="next-steps"></a>Nächste Schritte

- [Netzwerksicherheitsgruppen: Diensttags](../virtual-network/network-security-groups-overview.md#security-rules)