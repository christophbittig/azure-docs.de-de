---
title: Problembehandlung für Logz.io – Azure-Partnerlösungen
description: Erfahren Sie mehr über die Problembehandlung bei der Azure-Integration mit Logz.io.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 4322cdfead345aec836760199ce7d6f1f9654c59
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057512"
---
# <a name="troubleshoot-logzio-integration-with-azure"></a>Problembehandlung bei der Logz.io-Integration in Azure

In diesem Artikel wird beschrieben, wie Sie Probleme bei der Azure-Integration mit Logz.io behandeln.

## <a name="owner-role-needed-to-create-resource"></a>Rolle „Besitzer“ zum Erstellen einer Ressource erforderlich

Zum Einrichten von Logz.io muss Ihnen im Azure-Abonnement die [Rolle „Besitzer“](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zugewiesen sein. Bevor Sie mit dieser Integration beginnen, [überprüfen Sie Ihren Zugriff](../../role-based-access-control/check-access.md).

## <a name="single-sign-on-errors"></a>Fehler beim einmaligen Anmelden

Während der Einrichtung des einmaligen Anmeldens können Fehler auftreten. Weitere Informationen zum Einrichten des einmaligen Anmeldens (Single Sign-On, SSO) finden Sie unter [Einrichten des einmaligen Anmeldens mit Logz.io](setup-sso.md).

### <a name="unable-to-save-single-sign-on-settings"></a>Einstellungen für einmaliges Anmelden können nicht gespeichert werden

Dieser Fehler bedeutet, dass es eine andere Unternehmensanwendung gibt, die den SAML-Bezeichner (Security Assertion Markup Language) von Logz.io verwendet. Um herauszufinden, um welche Anwendung es sich dabei handelt, wählen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Option **Bearbeiten** aus.

Zur Behebung dieses Problems deaktivieren Sie entweder die Unternehmensanwendung, die SAML verwendet, oder verwenden Sie eine andere Unternehmensanwendung, um SAML-SSO mit Logz.io einzurichten. Stellen Sie sicher, dass die Anwendung die erforderlichen Einstellungen aufweist.

### <a name="application-not-shown-in-single-sign-on"></a>Anwendung wird beim einmaligen Anmelden nicht angezeigt

Suchen Sie mithilfe der _Anwendungs-ID_. Wenn kein Ergebnis angezeigt wird, überprüfen Sie die SAML-Einstellungen der Anwendung. Im Raster werden nur vorhandene Anwendungen mit den richtigen SAML-Einstellungen angezeigt. Der **Bezeichner** und die **Antwort-URL** müssen genau wie in der folgenden Abbildung dargestellt sein.

In den leeren Textfeldern können Sie neue Werte für **Bezeichner** und **Antwort-URL** eingeben.

Verwenden Sie die folgenden Muster, um neue Werte hinzuzufügen:

- **Bezeichner**: `urn:auth0:logzio:<Application ID>`
- **Antwort-URL**: `https://logzio.auth0.com/login/callback?connection=<Application ID>`

:::image type="content" source="./media/troubleshoot/basic-saml-config.png" alt-text="Einstellungen für die grundlegende SAML-Konfiguration":::

## <a name="logs-not-being-sent-to-logzio"></a>Protokolle werden nicht an Logz.io gesendet

Nur Ressourcen, die unter den [Azure Monitor-Ressourcenprotokollkategorien](../../azure-monitor/essentials/resource-logs-categories.md) aufgelistet sind, senden Protokolle an Logz.io.

Um zu überprüfen, ob eine Ressource Protokolle an Logz.io sendet, gehen Sie folgendermaßen vor:

1. Navigieren Sie zu [Azure-Diagnoseeinstellungen](../../azure-monitor/essentials/diagnostic-settings.md) für die jeweilige Ressource.
1. Überprüfen Sie, ob eine Diagnoseeinstellung für Logz.io vorhanden ist.

:::image type="content" source="./media/troubleshoot/diagnostics.png" alt-text="Diagnoseeinstellungen":::

## <a name="limit-reached-in-monitored-resources"></a>Grenzwert erreicht unter überwachten Ressourcen

Die Azure Monitor-Diagnose unterstützt maximal fünf Diagnoseeinstellungen für eine einzelne Ressource oder ein einzelnes Abonnement. Wenn Sie diesen Grenzwert erreichen, wird unter **Überwachte Ressourcen** der Zustand **Grenzwert erreicht** für die Ressource angezeigt. Sie können keine Überwachung mit Logz.io hinzufügen.

:::image type="content" source="./media/troubleshoot/limit-monitored-resources.png" alt-text="Grenzwert erreicht für überwachte Ressourcen":::

## <a name="vm-extension-installation-failed"></a>Fehler bei der Installation der VM-Erweiterung

Ein virtueller Computer (VM) kann nur von einem einzigen Logz.io-Konto (Haupt- oder Unterkonto) überwacht werden. Wenn Sie versuchen, den Agent auf einer VM zu installieren, die bereits von einem anderen Konto überwacht wird, wird der folgende Fehler angezeigt:

:::image type="content" source="./media/troubleshoot/vm-agent-fail.png" alt-text="Fehler bei der Installation des Agents für virtuelle Computer":::

## <a name="purchase-errors"></a>Fehler beim Kauf

Beim Kauf tritt ein Fehler auf, weil keine gültige Kreditkarte mit dem Azure-Abonnement verknüpft ist. Es kann auch sein, dass dem Abonnement keine Zahlungsmethode zugeordnet ist.

Zum Beheben eines Fehlers beim Kauf gehen Sie folgendermaßen vor:

- Verwenden Sie ein anderes Azure-Abonnement.
- Fügen Sie die Kreditkarte oder Zahlungsmethode für das Abonnement hinzufügen, oder aktualisieren Sie die entsprechenden Angaben. Weitere Informationen finden Sie unter [Hinzufügen oder Aktualisieren einer Kreditkarte für Azure](../../cost-management-billing/manage/change-credit-card.md).

Sie können die Ausgabe des Fehlers auf der Bereitstellungsseite der Ressource anzeigen, indem Sie **Vorgangsdetails** auswählen.

```json
{
  "status": "Failed",
  "error": {
    "code": "BadRequest",
    "message": "{\"message\":\"Purchase has failed because we couldn't find a valid credit card nor
               a payment method associated with your Azure subscription. Please use a different
               Azure subscription or add\\\\update current credit card or payment method for this
               subscription and retry.\",\"code\":\"BadRequest\"}"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre Logz.io-Integration [verwalten](manage.md).
- Weitere Informationen zum einmaligen Anmelden finden Sie unter [Einrichten des einmaligen Anmeldens mit Logz.io](setup-sso.md).
