---
title: Sammeln von Daten in benutzerdefinierten Protokollformaten für Azure Sentinel | Microsoft-Dokumentation
description: Sammeln Sie Daten aus benutzerdefinierten Datenquellen, und erfassen Sie sie mithilfe des Log Analytics-Agents in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d99785da00f277480505b417947781821df1caa2
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123261050"
---
# <a name="collect-data-in-custom-log-formats-to-azure-sentinel-with-the-log-analytics-agent"></a>Sammeln von Daten in benutzerdefinierten Protokollformaten für Azure Sentinel mit dem Log Analytics-Agent

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Viele Anwendungen protokollieren Daten nicht in standardmäßigen Protokollierungsdiensten wie Windows-Ereignisprotokoll oder Syslog, sondern in Textdateien. Sie können den Log Analytics-Agent verwenden, um Daten in Textdateien mit nicht standardmäßigen Formaten von Windows- und Linux-Computern zu sammeln. Die erfassten Daten können entweder in Ihren Abfragen zu einzelnen Feldern aufgeschlüsselt oder während der Erfassung in einzelne Felder extrahiert werden.

In diesem Artikel wird beschrieben, wie Sie Ihre Datenquellen über benutzerdefinierte Protokollformate mit Azure Sentinel verbinden. Weitere Informationen zu unterstützten Datenconnectors für diese Methode finden Sie in der [Referenz zu Datenconnectors](data-connectors-reference.md).

Informationen zu [benutzerdefinierten Protokollen finden Sie in der Azure Monitor-Dokumentation](../azure-monitor/agents/data-sources-custom-logs.md).

Ähnlich wie bei Syslog gibt es zwei Schritte zum Konfigurieren der benutzerdefinierten Protokollsammlung:

- Installieren Sie den Log Analytics-Agent auf dem Linux- oder Windows Computer, der die Protokolle generiert.

- Konfigurieren Sie die Protokollierungseinstellungen Ihrer Anwendung.

- Konfigurieren Sie den Log Analytics-Agent aus Azure Sentinel heraus.

## <a name="install-the-log-analytics-agent"></a>Installieren des Log Analytics-Agents

Installieren Sie den Log Analytics-Agent auf dem Linux- oder Windows Computer, der die Protokolle generiert.

> [!NOTE]
> Einige Anbieter empfehlen, den Log Analytics-Agent auf einem separaten Protokollserver, statt direkt auf dem Gerät zu installieren. Lesen Sie den Abschnitt Ihres Produkts auf der [Referenzseite für Datenconnectors](data-connectors-reference.md) oder die eigene Dokumentation Ihres Produkts.

Wählen Sie die entsprechende Registerkarte unten aus, je nachdem, ob Ihr Connector über eine Datenconnectorseite in Azure Sentinel verfügt.

# <a name="from-a-specific-data-connector-page"></a>[Von einer bestimmten Datenconnectorseite](#tab/DCG)

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie Ihren Gerätetyp und dann **Connectorseite öffnen** aus.

1. Installieren und registrieren Sie den Agent auf dem Gerät, das die Protokolle generiert. Wählen Sie je nach Bedarf Linux oder Windows aus.

    | Computertyp | Anweisungen |
    | --------- | --------- |
    | **Für eine Azure-Linux-VM** | <ol><li>Erweitern Sie unter **Wählen Sie aus, wo der Linux-Agent installiert werden soll** die Option **Agent auf virtuellem Linux-Computer in Azure installieren**.<br><br><li>Klicken Sie auf den Link **Agent für virtuelle Linux-Computer (Azure) herunterladen und installieren >** .<br><br><li>Wählen Sie auf dem Blatt **Virtuelle Computer** eine VM aus, auf der der Agent installiert werden soll, und wählen Sie dann **Verbinden** aus. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten. |
    | **Für jeden weiteren Linux-Computer** | <ol><li>Erweitern Sie unter **Wählen Sie aus, wo der Linux-Agent installiert werden soll** die Option **Agent auf virtuellem Linux-Computer (kein Azure) installieren**.<br><br><li>Klicken Sie auf den Link **Agent für Linux-Computer (kein Azure) herunterladen und installieren >** .<br><br><li>Klicken Sie auf dem Blatt **Agent-Verwaltung** auf die Registerkarte **Linux-Server**, kopieren Sie dann den Befehl für **Agent für Linux herunterladen und Onboarding durchführen**, und führen Sie ihn auf Ihrem Linux-Computer aus.<br><br> Wenn Sie eine lokale Kopie der Linux-Agent-Installationsdatei beibehalten möchten, klicken Sie auf den Link **Linux-Agent herunterladen** über dem Befehl „Agent herunterladen und integrieren“ aus.|
    | **Für einen virtuellen Windows-Computer in Azure** | <ol><li>Erweitern Sie unter **Wählen Sie aus, wo der Windows-Agent installiert werden soll** die Option **Agent auf virtuellem Windows-Computer in Azure installieren**.<br><br><li>Wählen Sie den Link **Agent für virtuellen Windows-Computer in Azure herunterladen und installieren >** .<br><br><li>Wählen Sie auf dem Blatt **Virtuelle Computer** eine VM aus, auf der der Agent installiert werden soll, und wählen Sie dann **Verbinden** aus. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten. |
    | **Für alle anderen Windows-Computer** | <ol><li>Erweitern Sie unter **Wählen Sie aus, wo der Windows-Agent installiert werden soll** die Option **Agent auf Windows-Computer (kein Azure) installieren**.<br><br><li>Klicken Sie auf den Link **Agent für Windows-Computer (kein Azure) herunterladen und installieren**.<br><br><li>Wählen Sie auf dem Blatt **Agent-Verwaltung** auf der Registerkarte **Windows-Server** je nach Eignung den Link **Windows-Agent herunterladen** für 32-Bit- oder 64-Bit-Systeme aus. |


# <a name="other-data-sources"></a>[Andere Datenquellen](#tab/CUS)

1. Wählen Sie im Navigationsmenü von Azure Sentinel auf der linken Seite **Einstellungen** und dann die Registerkarte **Arbeitsbereichseinstellungen** aus.

1. Installieren und registrieren Sie den Agent auf dem Gerät, das die Protokolle generiert. Wählen Sie je nach Bedarf Linux oder Windows aus.

    | Computertyp | Anweisungen |
    | --------- | --------- |
    | **Azure-VM (Windows oder Linux)** | <ol><li>Wählen Sie im Navigationsmenü des Log Analytics-Arbeitsbereichs die Option **Virtuelle Computer** aus.<br><br><li>Wählen Sie auf dem Blatt **Virtuelle Computer** eine VM aus, auf der der Agent installiert werden soll, und wählen Sie dann **Verbinden** aus.<br>Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten. |
    | **Alle anderen Windows oder Linux-Computer** | <ol><li>Wählen Sie im Navigationsmenü des Log Analytics-Arbeitsbereichs die Option **Agent-Verwaltung** aus.<br><br><li>Wählen Sie nach Bedarf die Registerkarte **Windows Server** oder **Linux-Server** aus.<br><br><li>Wählen Sie für Windows den Link **Windows-Agent herunterladen** für 32-Bit- oder 64-Bit-Systeme aus. Kopieren Sie für Linux den Befehl zum **Herunterladen und Integrieren des Agents für Linux**, und führen Sie ihn über die Befehlszeile aus, oder wählen Sie den Link **Linux-Agent herunterladen** aus, um eine lokale Kopie der Installationsdatei herunterzuladen. |

---

## <a name="configure-the-logs-to-be-collected"></a>Konfigurieren der zu erfassenden Protokolle

Für viele Gerätetypen werden eigene Datenconnectors auf der Seite **Datenconnectors** in Azure Sentinel angezeigt. Für einige dieser Connectors gelten spezielle zusätzliche Anweisungen zum ordnungsgemäßen Einrichten der Protokollsammlung in Azure Sentinel. Diese Anweisungen können die Implementierung eines Parsers basierend auf einer Kusto-Funktion beinhalten. 

Für alle in Azure Sentinel aufgeführten Connectors werden spezifische Anweisungen ggf. auf den jeweiligen Connectorseiten im Portal sowie in den entsprechenden Abschnitten der [Referenzseite zu Azure Sentinel-Datenconnectors](data-connectors-reference.md) angezeigt.

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

In diesem Dokument haben Sie erfahren, wie Sie Daten von benutzerdefinierten Protokolltypen sammeln, um sie in Azure Sentinel zu erfassen. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
