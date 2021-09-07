---
title: Schätzen der App-Streamingkosten auf Benutzerebene für Azure Virtual Desktop – Azure
description: Hier erfahren Sie, wie Sie die Abrechnungskosten auf Benutzerebene für Azure Virtual Desktop schätzen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 4d0218031a79e5271dcdff7531f856094175c477
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798926"
---
# <a name="estimate-per-user-app-streaming-costs-for-azure-virtual-desktop"></a>Schätzen der App-Streamingkosten auf Benutzerebene für Azure Virtual Desktop

Mit den Preisen auf Benutzerebene für Azure Virtual Desktop können Sie Benutzern außerhalb Ihres Unternehmens Zugriff auf mit Azure Virtual Desktop gehostete Apps und Desktops gewähren. Weitere Informationen zu den Preisen für den Zugriff auf Benutzerebene für Azure Virtual Desktop finden Sie unter [Grundlegendes zu Lizenzen und zu den Preisen für benutzerspezifischen Zugriff](licensing.md). In diesem Artikel erfahren Sie, wie Sie die Kosten für den Zugriff auf Benutzerebene für Ihre Bereitstellung schätzen können, wobei alle Preisinformationen vorausgesetzt werden.

>[!NOTE]
>Die Preise in diesem Artikel beziehen sich auf Abonnements zum vollen Preis pro Benutzer ohne Werbeangebote oder Rabatte. Beachten Sie außerdem, dass mit der Azure Virtual Desktop-Bereitstellung zusätzliche Kosten verbunden sind, z. B. für die Nutzung der Infrastruktur. Weitere Informationen zu diesen anderen Kosten finden Sie unter [Grundlegendes zu den Gesamtkosten für die Bereitstellung von Azure Virtual Desktop](total-costs.md).

## <a name="requirements"></a>Requirements (Anforderungen)

Um die Zugriffskosten pro Benutzer für eine bestehende Bereitstellung schätzen zu können, benötigen Sie die folgenden Voraussetzungen:

- Eine Azure Virtual Desktop-Bereitstellung, die im letzten Monat aktive Benutzer aufgewiesen hat.
- [Azure Monitor für Ihre Azure Virtual Desktop-Bereitstellung](../azure-monitor.md)

## <a name="measure-monthly-user-activity-in-a-host-pool"></a>Messung der monatlichen Benutzeraktivität in einem Hostpool.

Um die Gesamtkosten für den Betrieb eines Hostpools schätzen zu können, müssen Sie zunächst die Anzahl der aktiven Benutzer im vergangenen Monat kennen. Sie können Azure Monitor für Azure Virtual Desktop verwenden, um diesen Wert zu finden.

So überprüfen Sie monatlich aktive Benutzer für Azure Monitor

1. Öffnen Sie das Azure-Portal, suchen Sie dann nach **Azure Virtual Desktop** und wählen Sie es aus. Wählen Sie anschließend **Erkenntnisse** aus, um Azure Monitor für Azure Virtual Desktop zu öffnen.

2. Wählen Sie den Namen des Abonnements oder Hostpools aus, das bzw. den Sie messen möchten.

3. Auf der Registerkarte **Übersicht** finden Sie im Abschnitt für die **Auslastung** das Diagramm für **Monatliche Benutzer (MAU)** .

4. Prüfen Sie den Wert der monatlich aktiven Benutzer (MAU) für das aktuellste Datum. Die MAU zeigt an, wie viele Benutzer innerhalb der letzten 28 Tage vor diesem Datum eine Verbindung mit diesem Hostpool hergestellt haben.

## <a name="estimate-per-user-access-costs-for-an-azure-virtual-desktop-host-pool"></a>Schätzen der Zugriffskosten auf Benutzerebene für einen Azure Virtual Desktop-Hostpool

Als nächstes prüfen wir den Betrag, der pro Abrechnungszyklus in Rechnung gestellt wird. Dieser Wert wird durch die Anzahl der Benutzer bestimmt, die mit mindestens einem Sitzungshost in Ihrem registrierten Abonnement verbunden sind.

Außerdem gibt es zwei Tarife für Benutzer:

- Benutzer, die nur mit RemoteApp-Anwendungsgruppen Verbindungen herstellen.
- Benutzer, die mit mindestens einer Desktopanwendungsgruppe Verbindungen herstellen.

Sie können die Gesamtkosten schätzen, indem Sie überprüfen, wie viele Benutzer in jedem Tarif mit den Sitzungshosts in Ihrem Abonnement verbunden sind.

So prüfen Sie die Anzahl der Benutzer in jedem Tarif

1. Wechseln Sie auf die [Seite mit der Preisübersicht für Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/), und suchen Sie nach den Preisen für „Apps“ und „Apps + Desktops“ für Ihre Region.
2. Verwenden Sie den Wert des Verbindungsaufkommens, die Sie in Schritt 4 von [Messen der monatlichen Aktivität in einem Hostpool](#measure-monthly-user-activity-in-a-host-pool) ermittelt haben, um die Gesamtkosten für den Benutzerzugriff zu berechnen.
   
   Wenn Ihr Hostpool eine RemoteApp-Anwendungsgruppe verwendet, müssen Sie das Verbindungsaufkommen mit dem Preiswert multiplizieren, der unter „Apps“ angezeigt wird. Mit anderen Worten, Sie müssen diese Gleichung verwenden:

   Verbindungsaufkommen x „Apps“-Preis pro Benutzer = Gesamtkosten

   Wenn Ihr Hostpool eine Desktopanwendungsgruppe verwendet, multiplizieren Sie ihn stattdessen wie folgt mit dem Preis für „Apps + Desktops“ pro Benutzer:

   Verbindungsaufkommen x „Apps + Desktops“-Preis pro Benutzer = Gesamtkosten

>[!IMPORTANT]
>Je nach Ihrer Umgebung kann der tatsächliche Preis von der Schätzung abweichen, die Sie anhand dieser Anweisungen erhalten. So kann die Kostenschätzung z. B. höher sein als die tatsächlichen Kosten, da Ihre Benutzer auf Ressourcen aus mehreren Hostpools zugreifen, Ihnen aber nur einmal pro Benutzer und Abrechnungszyklus in Rechnung gestellt werden. Die Schätzung könnte auch die Kosten unterschätzen, wenn die Benutzeraktivität während des 28-Tage-Zeitfensters, auf das Sie Ihre Daten stützen, nicht der typischen monatlichen Benutzeraktivität entspricht. Ein Monat mit einem einwöchigen Urlaub oder einem größeren Ausfall des Diensts weist z. B. eine unterdurchschnittliche Benutzeraktivität auf und liefert keine genaue Schätzung.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mehr über die Schätzung der Gesamtkosten für eine gesamte Azure Virtual Desktop-Bereitstellung erfahren möchten, lesen Sie den Abschnitt [Grundlegendes zu den Gesamtkosten für die Bereitstellung von Azure Virtual Desktop](total-costs.md). Informationen über Lizenzanforderungen und -kosten finden Sie unter [Grundlegendes zu Lizenzen und zu den Preisen für benutzerspezifischen Zugriff](licensing.md).
