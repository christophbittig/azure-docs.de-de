---
title: Sammeln von Daten in benutzerdefinierten Protokollformaten für Microsoft Sentinel | Microsoft Docs
description: Sammeln Sie Daten aus benutzerdefinierten Datenquellen und nehmen Sie sie mit dem Log Analytics-Agent in Microsoft Sentinel auf.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1a64f6e11abf8531c90968c2f24824f700ac73d9
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524586"
---
# <a name="collect-data-in-custom-log-formats-to-microsoft-sentinel-with-the-log-analytics-agent"></a>Sammeln von Daten in benutzerdefinierten Protokollformaten für Microsoft Sentinel mit dem Log Analytics-Agent

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Viele Anwendungen protokollieren Daten nicht in standardmäßigen Protokollierungsdiensten wie Windows-Ereignisprotokoll oder Syslog, sondern in Textdateien. Sie können den Log Analytics-Agent verwenden, um Daten in Textdateien mit nicht standardmäßigen Formaten von Windows- und Linux-Computern zu sammeln. Die erfassten Daten können entweder in Ihren Abfragen zu einzelnen Feldern aufgeschlüsselt oder während der Erfassung in einzelne Felder extrahiert werden.

Dieser Artikel beschreibt, wie Sie Ihre Datenquellen mit Microsoft Sentinel verbinden können, indem Sie benutzerdefinierte Protokollformate verwenden. Weitere Informationen zu unterstützten Datenconnectors für diese Methode finden Sie in der [Referenz zu Datenconnectors](data-connectors-reference.md).

Informationen zu [benutzerdefinierten Protokollen finden Sie in der Azure Monitor-Dokumentation](../azure-monitor/agents/data-sources-custom-logs.md).

Ähnlich wie bei Syslog gibt es zwei Schritte zum Konfigurieren der benutzerdefinierten Protokollsammlung:

- Installieren Sie den Log Analytics-Agent auf dem Linux- oder Windows Computer, der die Protokolle generiert.

- Konfigurieren Sie die Protokollierungseinstellungen Ihrer Anwendung.

- Konfigurieren Sie den Log Analytics-Agent in Microsoft Sentinel.

## <a name="install-the-log-analytics-agent"></a>Installieren des Log Analytics-Agents

Installieren Sie den Log Analytics-Agent auf dem Linux- oder Windows Computer, der die Protokolle generiert.

> [!NOTE]
> Einige Anbieter empfehlen, den Log Analytics-Agent auf einem separaten Protokollserver, statt direkt auf dem Gerät zu installieren. Lesen Sie den Abschnitt Ihres Produkts auf der [Referenzseite für Datenconnectors](data-connectors-reference.md) oder die eigene Dokumentation Ihres Produkts.

Wählen Sie die entsprechende Registerkarte unten, je nachdem, ob Ihr Konnektor eine Datenkonnektor-Seite in Microsoft Sentinel hat.

# <a name="from-a-specific-data-connector-page"></a>[Von einer bestimmten Datenconnectorseite](#tab/DCG)

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Datenkonnektoren**.

1. Wählen Sie Ihren Gerätetyp und dann **Connectorseite öffnen** aus.

1. Installieren und registrieren Sie den Agent auf dem Gerät, das die Protokolle generiert. Wählen Sie je nach Bedarf Linux oder Windows aus.

    | Computertyp | Anweisungen |
    | --------- | --------- |
    | **Für eine Azure-Linux-VM** | <ol><li>Erweitern Sie unter **Wählen Sie aus, wo der Linux-Agent installiert werden soll** die Option **Agent auf virtuellem Linux-Computer in Azure installieren**.<br><br><li>Klicken Sie auf den Link **Agent für virtuelle Linux-Computer (Azure) herunterladen und installieren >** .<br><br><li>Wählen Sie auf dem Blatt **Virtuelle Computer** eine VM aus, auf der der Agent installiert werden soll, und wählen Sie dann **Verbinden** aus. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten. |
    | **Für jeden weiteren Linux-Computer** | <ol><li>Erweitern Sie unter **Wählen Sie aus, wo der Linux-Agent installiert werden soll** die Option **Agent auf virtuellem Linux-Computer (kein Azure) installieren**.<br><br><li>Klicken Sie auf den Link **Agent für Linux-Computer (kein Azure) herunterladen und installieren >** .<br><br><li>Klicken Sie auf dem Blatt **Agent-Verwaltung** auf die Registerkarte **Linux-Server**, kopieren Sie dann den Befehl für **Agent für Linux herunterladen und Onboarding durchführen**, und führen Sie ihn auf Ihrem Linux-Computer aus.<br><br> Wenn Sie eine lokale Kopie der Linux-Agent-Installationsdatei beibehalten möchten, klicken Sie auf den Link **Linux-Agent herunterladen** über dem Befehl „Agent herunterladen und integrieren“ aus.|
    | **Für einen virtuellen Windows-Computer in Azure** | <ol><li>Erweitern Sie unter **Wählen Sie aus, wo der Windows-Agent installiert werden soll** die Option **Agent auf virtuellem Windows-Computer in Azure installieren**.<br><br><li>Wählen Sie den Link **Agent für virtuellen Windows-Computer in Azure herunterladen und installieren >** .<br><br><li>Wählen Sie auf dem Blatt **Virtuelle Computer** eine VM aus, auf der der Agent installiert werden soll, und wählen Sie dann **Verbinden** aus. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten. |
    | **Für alle anderen Windows-Computer** | <ol><li>Erweitern Sie unter **Wählen Sie aus, wo der Windows-Agent installiert werden soll** die Option **Agent auf Windows-Computer (kein Azure) installieren**.<br><br><li>Klicken Sie auf den Link **Agent für Windows-Computer (kein Azure) herunterladen und installieren**.<br><br><li>Wählen Sie auf dem Blatt **Agent-Verwaltung** auf der Registerkarte **Windows-Server** je nach Eignung den Link **Windows-Agent herunterladen** für 32-Bit- oder 64-Bit-Systeme aus. |

# <a name="other-data-sources"></a>[Andere Datenquellen](#tab/CUS)

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Einstellungen** und dann die Registerkarte **Arbeitsbereichseinstellungen**.

1. Installieren und registrieren Sie den Agent auf dem Gerät, das die Protokolle generiert. Wählen Sie je nach Bedarf Linux oder Windows aus.

    | Computertyp | Anweisungen |
    | --------- | --------- |
    | **Azure-VM (Windows oder Linux)** | <ol><li>Wählen Sie im Navigationsmenü des Log Analytics-Arbeitsbereichs die Option **Virtuelle Computer** aus.<br><br><li>Wählen Sie auf dem Blatt **Virtuelle Computer** eine VM aus, auf der der Agent installiert werden soll, und wählen Sie dann **Verbinden** aus.<br>Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten. |
    | **Alle anderen Windows oder Linux-Computer** | <ol><li>Wählen Sie im Navigationsmenü des Log Analytics-Arbeitsbereichs die Option **Agent-Verwaltung** aus.<br><br><li>Wählen Sie nach Bedarf die Registerkarte **Windows Server** oder **Linux-Server** aus.<br><br><li>Wählen Sie für Windows den Link **Windows-Agent herunterladen** für 32-Bit- oder 64-Bit-Systeme aus. Kopieren Sie für Linux den Befehl zum **Herunterladen und Integrieren des Agents für Linux**, und führen Sie ihn über die Befehlszeile aus, oder wählen Sie den Link **Linux-Agent herunterladen** aus, um eine lokale Kopie der Installationsdatei herunterzuladen. |

---

## <a name="configure-the-logs-to-be-collected"></a>Konfigurieren der zu erfassenden Protokolle

Viele Gerätetypen haben ihre eigenen Datenkonnektoren, die auf der Seite **Datenkonnektoren** in Microsoft Sentinel erscheinen. Einige dieser Konnektoren erfordern spezielle zusätzliche Anweisungen, um die Protokollsammlung in Microsoft Sentinel ordnungsgemäß einzurichten. Diese Anweisungen können die Implementierung eines Parsers basierend auf einer Kusto-Funktion beinhalten. 

Alle in Microsoft Sentinel aufgeführten Konnektoren zeigen auf ihren jeweiligen Konnektorenseiten im Portal sowie in ihren Abschnitten auf der Seite [Microsoft Sentinel-Datenkonnektoren-Referenz](data-connectors-reference.md) spezifische Anweisungen an.

Wenn Ihr Produkt nicht auf der Seite **Datenconnectors** aufgeführt ist, finden Sie in der Dokumentation Ihres Anbieters Anweisungen zum Konfigurieren der Protokollierung für Ihr Gerät.

## <a name="configure-the-log-analytics-agent"></a>Konfigurieren des Log Analytics-Agent

1. Wählen Sie auf der Connectorseite den Link **Öffnen der Konfiguration der erweiterten Einstellungen Ihres Arbeitsbereichs** aus.

    Oder wählen Sie im Navigationsmenü des Log Analytics-Arbeitsbereichs die Option **Benutzerdefinierte Protokolle** aus.

1. Wählen Sie auf der Seite **Benutzerdefinierte Tabellen** die Option **Benutzerdefiniertes Protokoll hinzufügen** aus.

1. Laden Sie auf der Registerkarte **Beispiel** ein Beispiel einer Protokolldatei von Ihrem Gerät hoch (z. B. access.log oder error.log). Klicken Sie anschließend auf **Weiter**.

1. Wählen Sie in der Registerkarte **Datensatztrennzeichen** ein Datensatztrennzeichen aus – entweder **Neue Zeile** oder **Zeitstempel** (siehe Anleitung in dieser Registerkarte), und wählen Sie **Weiter** aus.

1. Wählen Sie auf der Registerkarte **Sammlungspfade** den Pfadtyp Windows oder Linux aus, und geben Sie den Pfad zu den Protokollen Ihres Geräts basierend auf Ihrer Konfiguration ein. Klicken Sie anschließend auf **Weiter**.

1. Geben Sie Ihrem benutzerdefinierten Protokoll einen Namen und optional eine Beschreibung, und wählen Sie **Weiter** aus.  
    Beenden Sie Ihren Namen nicht mit „_CL“, da diese Kennung automatisch angefügt wird.


## <a name="find-your-data"></a>Suchen von Daten

Geben Sie im Abfragefenster den Namen ein, den Sie Ihrem benutzerdefinierten Protokoll gegeben haben (der auf „_CL“ endet), um die benutzerdefinierten Protokolldaten in **Protokolle** abzufragen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie Daten aus benutzerdefinierten Protokolltypen sammeln, um sie in Microsoft Sentinel zu importieren. Weitere Informationen über Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Erste Schritte [Erkennung von Bedrohungen mit Microsoft Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
