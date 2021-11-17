---
title: Behandeln von Problemen mit Azure Automation-Konten
description: In diesem Artikel erfahren Sie, wie Sie Probleme mit einem Azure-Konto behandeln und beheben können.
services: automation
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 33c4f3bab041356a1ad91e7ab9f12891f0d9c99d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488562"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Behandeln von Problemen mit Azure Automation-Konten

In diesem Artikel werden Lösungen für Probleme beschrieben, die bei Verwendung eines Azure Automation-Kontos auftreten können. Allgemeine Informationen zu Automation-Konten finden Sie unter [Übersicht über die Azure Automation-Kontoauthentifizierung](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Szenario: Der Automation-Ressourcenanbieter für Abonnements kann nicht registriert werden.

### <a name="issue"></a>Problem

Beim Arbeiten mit Verwaltungsfeatures, wie z. B. der Updateverwaltung, in Ihrem Automation-Konto tritt der folgende Fehler auf:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Ursache

Der Automation-Ressourcenanbieter ist nicht im Abonnement registriert.

### <a name="resolution"></a>Lösung

Führen Sie im Azure-Portal die folgenden Schritte aus, um den Automation-Ressourcenanbieter zu registrieren:

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

2. Wählen Sie unter **Abonnements** Ihr Abonnement aus.   

3. Wählen Sie unter **Einstellungen** die Option **Ressourcenanbieter** aus.

4. Überprüfen Sie in der Liste der Ressourcenanbieter, ob der Ressourcenanbieter **Microsoft.Automation** registriert ist.

5. Wenn der Anbieter nicht aufgeführt ist, registrieren Sie ihn, so wie unter [Beheben von Fehlern bei der Ressourcenanbieterregistrierung](../../azure-resource-manager/templates/error-register-resource-provider.md) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Ihr Problem nicht mit diesem Artikel lösen konnten, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Wenden Sie sich an [@AzureSupport](https://twitter.com/azuresupport) – Dies ist das offizielle Microsoft Azure-Konto, über das die Azure-Community Zugriff auf die richtigen Ressourcen erhält: Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.
