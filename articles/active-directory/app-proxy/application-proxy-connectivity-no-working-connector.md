---
title: Keine funktionierende Connectorgruppe für einen Azure Active Directory-Anwendungsproxy gefunden
description: Beheben von Problemen, die auftreten können, wenn kein funktionierender Connector in einer Connectorgruppe für die Anwendung mit dem Azure Active Directory-Anwendungsproxy vorhanden ist
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 1c099b9af1314a62f5fe26703525dfbad3b79ab2
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989720"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Keine funktionierende Connectorgruppe für eine Anwendungsproxyanwendung gefunden

Dieser Artikel unterstützt beim Beheben häufiger Probleme, die auftreten können, wenn für eine Anwendungsproxyanwendung, die in Azure Active Directory integriert wird, kein Connector erkannt wird.

## <a name="overview-of-steps"></a>Übersicht über die Schritte
Wenn für Ihre Anwendung kein funktionierender Connector in einer Connectorgruppe vorhanden ist, gibt es verschiedene Methoden, um das Problem zu beheben:

-   Wenn sich kein Connector in der Gruppe befindet, haben Sie die folgenden Möglichkeiten:

    -   Laden Sie einen neuen Connector auf den rechten lokalen Server herunter, und weisen Sie ihn dieser Gruppe zu.

    -   Verschieben Sie einen aktiven Connector in die Gruppe.

-   Wenn sich kein aktiver Connector in der Gruppe befindet, haben Sie die folgenden Möglichkeiten:

    -   Identifizieren Sie die Ursache für die Inaktivität des Connectors, und beheben Sie das Problem.

    -   Verschieben Sie einen aktiven Connector in die Gruppe.

Öffnen Sie das Menü „Anwendungsproxy“ in Ihrer Anwendung, und betrachten Sie die Warnmeldung der Connectorgruppe, um die Ursache für das Problem zu erfahren. Wenn keine Connectors in der Gruppe vorhanden sind, gibt die Warnmeldung an, dass die Gruppe mindestens einen Connector benötigt. Sind keine Connectors aktiviert, wird dies in der Warnmeldung erläutert. Das Vorhandensein inaktiver Connectors ist normal. 

   ![Connectorgruppenauswahl im Azure-Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Ausführliche Informationen zu den jeweiligen Optionen finden Sie unten im entsprechenden Abschnitt. Bei den Anweisungen wird angenommen, dass Sie von der Connectorverwaltungsseite aus starten. Wenn Sie die oben aufgeführte Fehlermeldung betrachten, können Sie zu dieser Seite wechseln, indem Sie auf die Warnung klicken. Sie können auch zu dieser Seite zu wechseln, indem Sie zu **Azure Active Directory** navigieren und auf **Unternehmens-Apps** sowie auf **Anwendungsproxy** klicken.

   ![Connectorgruppenverwaltung im Azure-Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Herunterladen eines neuen Connectors

Verwenden Sie die Schaltfläche „Connector herunterladen“ am Seitenanfang, um einen neuen Connector herunterzuladen.

Installieren Sie den Connector auf einem Computer mit direktem Zugriff auf die Back-End-Anwendung. In der Regel ist der Connector auf demselben Server wie die Anwendung installiert. Nach dem Herunterladen sollte der Connector in diesem Menü angezeigt werden. Klicken Sie auf den Connector und verwenden die Dropdownliste „Connectorgruppe“, um sicherzustellen, dass er der richtigen Gruppe angehört. Speichern Sie die Änderungen.

   ![Herunterladen des Connectors aus dem Azure-Portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Verschieben eines aktiven Connectors

Wenn Sie über einen aktiven Connector verfügen, der dieser Gruppe angehören sollte und uneingeschränkten Zugriff auf die Back-End-Anwendung hat, können Sie den Connector in die zugewiesene Gruppe verschieben. Klicken Sie hierzu auf den Connector. Verwenden Sie im Feld „Connectorgruppe“ die Dropdownliste, um die richtige Gruppe auszuwählen, und klicken Sie dann auf „Speichern“.

## <a name="resolve-an-inactive-connector"></a>Beheben eines inaktiven Connectors

Wenn die einzigen Connectors in der Gruppe inaktiv sind, befinden sie sich voraussichtlich auf einem Computer, bei dem die Blockierung nicht für alle erforderlichen Ports aufgehoben wurde.

Weitere Informationen zur Untersuchung dieses Problems finden Sie im Dokument zur Problembehandlung bei Ports.

## <a name="next-steps"></a>Nächste Schritte
[Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)


