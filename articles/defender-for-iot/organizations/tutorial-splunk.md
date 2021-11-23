---
title: Integrieren von Splunk in Microsoft Defender für loT
description: In diesem Tutorial erfahren Sie, wie Sie Splunk in Azure Defender für loT integrieren.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: e4b0f321741d6f07f2ac636d57de31bf02503877
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283579"
---
# <a name="tutorial-integrate-splunk-with-microsoft-defender-for-iot"></a>Tutorial: Integrieren von Splunk in Microsoft Defender für loT

In diesem Tutorial lernen Sie, wie Sie Splunk mit Microsoft Defender for loT integrieren und nutzen.

Defender für IoT mindert IIoT-, ICS- und SCADA-Risiken mit patentierten, ICS-fähigen, selbstlernenden Engines, die in weniger als einer Imagestunde und ohne Agents, Regeln oder Signaturen und spezielle Skills oder Vorwissen der Umgebung unmittelbare Einblicke in ICS-Geräte, Sicherheitsrisiken und Bedrohungen liefern.

Um eine fehlende Sichtbarkeit in Bezug auf die Sicherheit und Resilienz von OT-Netzwerken zu beheben, wurde für Defender für IoT die Defender für IoT-, IIoT- und ICS-Bedrohungsüberwachungsanwendung für Splunk entwickelt: eine native Integration von Defender für IoT und Splunk, die einen einheitlichen Ansatz für IT- und OT-Sicherheit ermöglicht.

Die Anwendung bietet SOC-Analysten einen mehrdimensionalen Einblick in die speziellen OT-Protokolle und IIoT-Geräte, die in Industrieumgebungen bereitgestellt werden, sowie ICS-fähige Verhaltensanalysen zur schnellen Erkennung von verdächtigem oder anomalem Verhalten. Außerdem ermöglicht die Anwendung die Reaktion auf IT- und OT-Vorfälle über ein zentrales Unternehmens-SOC. Im Hinblick auf die fortschreitende Verschmelzung von IT und OT zur Unterstützung neuer IIoT-Initiativen, z. B. intelligente Computer und Echtzeitintelligenz, stellt dies eine wichtige Weiterentwicklung dar.

Die Splunk-Anwendung kann lokal installiert oder in einer Cloud ausgeführt werden. Die Splunk-Integration und Defender für IoT unterstützen beide Bereitstellungen.

> [!Note]
> Verweise auf CyberX beziehen sich auf Microsoft Defender für IoT.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Herunterladen der Defender für IoT-Anwendung in Splunk
> * Senden von Defender für IoT-Warnungen an Splunk

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="version-requirements"></a>Versionsanforderungen

Die folgenden Versionen sind erforderlich, damit die Anwendung ausgeführt werden kann:

- Defender für IoT ab Version 2.4

- Splunkbase ab Version 11

- Splunk Enterprise ab Version 7.2

### <a name="splunk-permission-requirements"></a>Erforderliche Splunk-Berechtigungen

Die folgenden Splunk-Berechtigungen sind erforderlich:

- Benutzer mit einer Benutzerrolle auf *Administratorebene*.

## <a name="download-the-defender-for-iot-application-in-splunk"></a>Herunterladen der Defender für IoT-Anwendung in Splunk

Für den Zugriff auf die Defender für IoT-Anwendung in Splunk müssen Sie die Anwendung aus dem Splunkbase-App-Store herunterladen.

**So greifen Sie in Splunk auf die Defender für IoT-Anwendung zu**

1. Navigieren Sie zum [Splunkbase](https://splunkbase.splunk.com/)-App-Store.

1. Suchen Sie nach `CyberX ICS Threat Monitoring for Splunk`.

1. Wählen Sie die Anwendung „CyberX ICS Threat Monitoring for Splunk“ aus.

1. Wählen Sie die Schaltfläche **LOGIN TO DOWNLOAD** (ANMELDEN ZUM HERUNTERLADEN) aus.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Senden von Defender für IoT-Warnungen an Splunk

Die Defender für IoT-Warnungen enthalten Informationen zu einem umfangreichen Spektrum an Sicherheitsereignissen. Dazu gehören u. a. folgende Ereignisse:

- Abweichungen von der erlernten Baseline für Netzwerkaktivitäten

- Schadsoftwareerkennungen

- Erkennungen auf der Grundlage verdächtiger Ablaufänderungen

- Netzwerkanomalien

- Abweichungen von Protokollspezifikationen

    :::image type="content" source="media/tutorial-splunk/address-scan.png" alt-text="Fenster für Erkennungen":::

Sie können Defender für IoT auch so konfigurieren, dass Warnungen an den Splunk-Server gesendet werden. Dort werden die Warnungsinformationen auf dem Splunk Enterprise-Dashboard angezeigt.

:::image type="content" source="media/tutorial-splunk/alerts-and-details.png" alt-text="Anzeigen aller Warnungen und zugehörigen Details" lightbox="media/tutorial-splunk/alerts-and-details-expanded.png":::

Um Warnungsinformationen von Defender für IoT an die Splunk-Server zu senden, müssen Sie eine Weiterleitungsregel erstellen.

**So erstellen Sie eine Weiterleitungsregel**

1. Melden Sie sich beim Sensor an, und wählen Sie im linken Bereich **Weiterleitung** aus.

    :::image type="content" source="media/tutorial-splunk/forwarding.png" alt-text="Auswählen der blauen Schaltfläche „Create Forwarding Rule“ (Weiterleitungsregel erstellen)":::

1. Wählen Sie **Create Forwarding Rule** (Weiterleitungsregel erstellen) aus.

1. Definieren Sie im Fenster **Create Forwarding Rule** (Weiterleitungsregel erstellen) die Parameter für die Regel.

    :::image type="content" source="media/tutorial-splunk/forwarding-rule.png" alt-text="Erstellen der Parameter für die Weiterleitungsregel" lightbox="media/tutorial-splunk/forwarding-rule-expanded.png":::

    | Parameter | Beschreibung |
    |--|--|
    | **Name** | Der Name der Weiterleitungsregel |
    | **Schweregrad** | Der Mindestschweregrad für die Weiterleitung von Vorfällen. Wenn z. B. „Gering“ ausgewählt ist, werden Warnungen mit einem geringen Schweregrad und alle Warnungen mit höheren Schweregraden weitergeleitet. |
    | **Protokolle** | Standardmäßig sind alle Protokolle ausgewählt. Wenn Sie ein bestimmtes Protokoll angeben möchten, wählen Sie **Spezifisch** und dann das Protokoll aus, für das die Regel angewandt wird. |
    | **Engines** | Standardmäßig werden alle Sicherheits-Engines verwendet. Wenn Sie eine bestimmte Sicherheits-Engine auswählen möchten, für die die Regel angewandt wird, wählen Sie **Spezifisch** und dann die Engine aus. |
    | **Systembenachrichtigungen** | Weiterleiten des Online- oder Offlinestatus des Sensors. Diese Option ist nur verfügbar, wenn Sie bei Central Manager angemeldet sind. |

1. Wählen Sie **Aktion** und dann **Send to Splunk Server** (An Splunk-Server senden) aus.

1. Geben Sie die folgenden Splunk-Parameter ein.

    :::image type="content" source="media/tutorial-splunk/parameters.png" alt-text="In diesem Fenster einzugebende Splunk-Parameter" lightbox="media/tutorial-splunk/parameters-expanded.png":::

    | Parameter | BESCHREIBUNG |
    |--|--|
    | **Host** | Adresse des Splunk-Servers |
    | **Port** | 8089 |
    | **Benutzername** | Benutzername für den Splunk-Server |
    | **Kennwort** | Kennwort für den Splunk-Server |

1. Klicken Sie auf **Submit** (Senden).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die ersten Schritte bei der Integration von Splunk kennengelernt. Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie [ServiceNow in Microsoft Defender für IoT integrieren](tutorial-servicenow.md):

> [!div class="nextstepaction"]
> [Integrieren von ServiceNow in Microsoft Defender für IoT](tutorial-servicenow.md)
