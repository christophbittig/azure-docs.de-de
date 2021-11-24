---
title: Verbinden Ihrer Threat Intelligence-Plattform mit Microsoft Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Verbindung Ihrer Threat Intelligence-Plattform (TIP) oder Ihres benutzerdefinierten Feeds mit Microsoft Sentinel herstellen und Bedrohungsindikatoren senden.
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
ms.openlocfilehash: 1490e7b1b0364918ec63bf39ad5e1c70120549ec
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523066"
---
# <a name="connect-your-threat-intelligence-platform-to-microsoft-sentinel"></a>Verbinden Ihrer Threat Intelligence-Plattform mit Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Siehe auch**: [Herstellen einer Verbindung zwischen Microsoft Sentinel und STIX-/TAXII-Threat Intelligence-Feeds](connect-threat-intelligence-taxii.md)

Viele Organisationen nutzen TIP-Lösungen (Threat Intelligence Platform), um Feeds mit Bedrohungsindikatoren aus mehreren Quellen zu aggregieren, die Daten innerhalb der Plattform zusammenzustellen und dann auszuwählen, welche Bedrohungsindikatoren auf die verschiedene Sicherheitslösungen wie Netzwerkgeräte, EDR/XDR-Lösungen oder SIEM-Lösungen wie Microsoft Sentinel angewendet werden sollen. Mit dem **Datenconnector für Threat Intelligence-Plattformen** können Sie diese Lösungen zum Importieren von Bedrohungsindikatoren in Microsoft Sentinel verwenden. 

Da der TIP-Datenconnector zu diesem Zweck mit der [tiIndicators-API von Microsoft Intelligent Security Graph](/graph/api/resources/tiindicator) arbeitet, können Sie den Connector verwenden, um von jeder benutzerdefinierten Threat Intelligence Plattform, die mit dieser API kommunizieren kann, Indikatoren an Microsoft Sentinel (und andere Microsoft-Sicherheitslösungen wie Microsoft 365 Defender) zu übermitteln.

:::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-import-path.png" alt-text="Importpfad für Threat Intelligence":::

Erfahren Sie mehr über [Threat Intelligence](understand-threat-intelligence.md) in Microsoft Sentinel und insbesondere über die [Threat Intelligence-Plattformprodukte ](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products), die in Microsoft Sentinel integriert werden können.

## <a name="prerequisites"></a>Voraussetzungen  

- Sie benötigen entweder die Azure AD-Rolle **Globaler Administrator** oder **Sicherheitsadministrator**, um Berechtigungen für Ihr TIP-Produkt oder eine andere benutzerdefinierte Anwendung zu erteilen, die die direkte Integration mit der tiIndicators-API von Microsoft Graph-Sicherheit verwendet.

- Sie benötigen Lese- und Schreibberechtigungen für den Microsoft Sentinel-Arbeitsbereich, um Ihre Bedrohungsindikatoren zu speichern.

## <a name="instructions"></a>Anweisungen

Führen Sie die folgenden Schritte aus, um Bedrohungsindikatoren aus Ihrer integrierten TIP- oder einer benutzerdefinierten Threat Intelligence-Lösung in Microsoft Sentinel zu importieren:
1.  Abrufen einer Anwendungs-ID und eines geheimen Clientschlüssels aus Azure Active Directory
2.  Einfügen dieser Informationen in Ihre TIP-Lösung oder benutzerdefinierte Anwendung
3.  Aktivieren des Datenconnectors „Threat Intelligence-Plattformen“ in Microsoft Sentinel

### <a name="sign-up-for-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Anmelden für eine Anwendungs-ID und einen geheimen Clientschlüssel aus Ihrer Azure Active Directory-Instanz

Unabhängig davon, ob Sie eine TIP- oder eine benutzerdefinierte Lösung verwenden, benötigt die tiIndicators-API einige grundlegende Informationen, um Ihnen zu erlauben, eine Verbindung Ihres Feeds mit ihr herzustellen und ihr Bedrohungsindikatoren zu senden. Sie benötigen die folgenden drei Informationen:

- Anwendungs-ID (Client)
- Verzeichnis-ID (Mandant)
- Geheimer Clientschlüssel

Sie erhalten diese Informationen von Ihrer Azure Active Directory-Instanz über einen Prozess namens **App-Registrierung**, der die folgenden drei Schritte umfasst:

- Registrieren einer App bei Azure Active Directory
- Gewähren der von der App benötigten Berechtigungen zum Verbinden mit der Microsoft Graph-API tiIndicators und zum Senden von Bedrohungsindikatoren
- Abrufen der Einwilligung von Ihrer Organisation für das Gewähren dieser Berechtigungen für diese Anwendung.

#### <a name="register-an-application-with-azure-active-directory"></a>Registrieren einer Anwendung mit Azure Active Directory

1. Navigieren Sie im Azure-Portal zum Dienst **Azure Active Directory**.
1. Wählen Sie die Menüoption **App-Registrierungen** und dann **Neue Registrierung** aus.
1. Wählen Sie einen Namen für die Anwendungsregistrierung aus, aktivieren Sie das Optionsfeld **Einzelner Mandant**, und wählen Sie dann **Registrieren** aus. 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-register-application.png" alt-text="Registrieren einer Anwendung":::

1. Kopieren Sie auf dem angezeigten Bildschirm die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** . Dies sind die ersten beiden Informationen, die Sie später benötigen, um Ihre TIP- oder benutzerdefinierte Lösung zum Senden von Bedrohungsindikatoren an Microsoft Sentinel zu konfigurieren. Die dritte, der **Geheime Clientschlüssel**, kommt später.

#### <a name="specify-the-permissions-required-by-the-application"></a>Gewähren der von der Anwendung benötigten Berechtigungen

1. Gehen Sie zurück zur Hauptseite des **Azure Active Directory**-Diensts.

1. Wählen Sie die Menüoption **App-Registrierungen** und dann Ihre neu registrierte App aus.

1. Wählen Sie die Menüoption **API-Berechtigungen** aus, und klicken Sie auf die Schaltfläche **Berechtigung hinzufügen**.

1. Wählen Sie auf der Seite **API auswählen** die **Microsoft Graph**-API aus, um dann in einer Liste mit Microsoft Graph-Berechtigungen Ihre Auswahl zu treffen.

1. Wählen Sie an der Eingabeaufforderung „Welche Art von Berechtigungen sind für Ihre Anwendung erforderlich?“ **Anwendungsberechtigungen** aus. Dies ist der Berechtigungstyp, der von Anwendungen verwendet wird, die sich mit der App-ID und App-Geheimnissen (API-Schlüssel) authentifizieren.

1. Wählen Sie **ThreatIndicators.ReadWrite.OwnedBy** und dann **Berechtigung hinzufügen** aus, um diese Berechtigung der Liste der Berechtigungen Ihrer App hinzuzufügen.

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-1.png" alt-text="Angeben von Berechtigungen":::

#### <a name="get-consent-from-your-organization-to-grant-these-permissions"></a>Abrufen der Einwilligung von Ihrer Organisation für das Gewähren dieser Berechtigungen

1. Um eine Einwilligung zu erhalten, muss ein globaler Administrator in Azure Active Directory die Schaltfläche **Administratoreinwilligung für Ihren Mandanten** auf der **Berechtigungsseite der API** Ihrer App auswählen. Wenn Sie nicht über die Rolle „Globaler Administrator“ für Ihr Konto verfügen, ist diese Schaltfläche nicht verfügbar, und Sie müssen einen globalen Administrator Ihrer Organisation bitten, diesen Schritt auszuführen. 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-2.png" alt-text="Zustimmung erteilen":::

1. Nachdem die Einwilligung für Ihre App erteilt wurde, wird unter **Status** ein grünes Häkchen angezeigt.

Nachdem Ihre App registriert wurde und die Berechtigungen erteilt wurden, können Sie die letzte Anforderung auf Ihrer Liste erfüllen: einen geheimen Clientschlüssel für Ihre App abzurufen.

1. Gehen Sie zurück zur Hauptseite des **Azure Active Directory**-Diensts.

1. Wählen Sie die Menüoption **App-Registrierungen** und dann Ihre neu registrierte App aus.

1. Wählen Sie im Menü **Zertifikate & Geheimnisse** aus, und klicken Sie auf die Schaltfläche **Neuer geheimer Clientschlüssel**, um ein Geheimnis (API-Schlüssel) für Ihre App zu erhalten.

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-client-secret.png" alt-text="Abrufen des geheimen Clientschlüssels":::

1. Klicken Sie auf die Schaltfläche **Hinzufügen**, und **kopieren Sie den geheimen Clientschlüssel**.

    > [!IMPORTANT]
    > Sie müssen den **geheimen Clientschlüssel** kopieren, bevor Sie diesen Bildschirm verlassen. Sie können dieses Geheimnis nicht erneut abrufen, wenn Sie diese Seite verlassen. Sie benötigen diesen Wert, wenn Sie Ihre TIP- oder benutzerdefinierte Lösung konfigurieren.

### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Einfügen dieser Informationen in Ihre TIP-Lösung oder benutzerdefinierte Anwendung

Sie verfügen nun über die drei erforderlichen Informationen, die Sie benötigen, um Ihre TIP- oder Ihre benutzerdefinierte Lösung zum Senden von Bedrohungsindikatoren an Microsoft Sentinel zu konfigurieren.

- Anwendungs-ID (Client)
- Verzeichnis-ID (Mandant)
- Geheimer Clientschlüssel

1. Geben Sie diese Werte bei Bedarf in die Konfiguration Ihres integrierten TIP oder Ihrer benutzerdefinierten Lösung ein.

1. Geben Sie für das Zielprodukt **Microsoft Sentinel** an.

1. Geben Sie für die Aktion **Warnung** an.

Sobald diese Konfiguration abgeschlossen ist, werden Bedrohungsindikatoren von Ihrer TIP- oder benutzerdefinierten Lösung über die **Microsoft Graph tiIndicators-API** an Microsoft Sentinel gesendet.

### <a name="enable-the-threat-intelligence-platforms-data-connector-in-microsoft-sentinel"></a>Aktivieren des Datenconnectors „Threat Intelligence-Plattformen“ in Microsoft Sentinel

Der letzte Schritt im Integrationsprozess besteht darin, den **Threat Intelligence-Plattformen-Datenconnector** in Microsoft Sentinel zu aktivieren. Die Aktivierung des Connectors ermöglicht Microsoft Sentinel, die von Ihrer TIP- oder benutzerdefinierten Lösung gesendeten Bedrohungsindikatoren zu empfangen. Diese Indikatoren stehen dann in allen Microsoft Sentinel-Arbeitsbereichen für Ihre Organisation zur Verfügung. Führen Sie die folgenden Schritte aus, um den Datenconnector „Threat Intelligence-Plattformen“ für alle Arbeitsbereiche zu aktivieren:

1. Navigieren Sie im Azure-Portal zum Dienst **Microsoft Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren importieren möchten, die von Ihrer TIP- oder benutzerdefinierten Lösung gesendet werden.

1. Wählen Sie im Menü **Datenconnectors** im Connectorkatalog **Threat Intelligence-Plattformen** aus, und klicken Sie auf die Schaltfläche **Connectorseite öffnen**.

1. Da Sie die App-Registrierung bereits abgeschlossen und Ihre TIP- oder benutzerdefinierte Lösung bereits für das Senden von Bedrohungsindikatoren konfiguriert haben, müssen Sie nur noch auf die Schaltfläche **Verbinden** klicken.

Nach einigen Minuten sollten die Bedrohungsindikatoren in den Microsoft Sentinel-Arbeitsbereich fließen. Sie finden die neuen Indikatoren auf dem Blatt **Threat Intelligence**, auf das Sie über das Hauptmenü von Microsoft Sentinel Zugriff haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Ihre Threat Intelligence-Plattform mit Microsoft Sentinel verbinden. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln.

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](./detect-threats-built-in.md).
