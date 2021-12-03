---
title: Erstellen einer Logz.io-Ressource – Azure-Partnerlösungen
description: Schnellstartartikel, in dem beschrieben wird, wie eine Logz.io-Ressource in Azure erstellt wird.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: d97339251888b522fd31d30b3ce77d1bb4241f3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070069"
---
# <a name="quickstart-create-a-logzio-resource-in-azure-portal"></a>Schnellstart: Erstellen einer Logz.io-Ressource im Azure-Portal

Dieser Artikel beschreibt, wie Sie die Azure-Integration der Logz.io-SaaS-Lösung (Software-as-a-Service) aktivieren. Sie verwenden Logz.io zum Überwachen der Integrität und Leistung Ihrer Azure-Umgebung.

## <a name="prerequisites"></a>Voraussetzungen

- **Abonnementbesitzer**: Zum Einrichten von Logz.io muss Ihnen im Azure-Abonnement die [Rolle „Besitzer“](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zugewiesen sein. Bevor Sie mit dieser Integration beginnen, [überprüfen Sie Ihren Zugriff](../../role-based-access-control/check-access.md).
- **Registrierter Ressourcenanbieter**: Wenn `Microsoft.Insights` noch nicht für Ihr Abonnement registriert ist, führen Sie diese Registrierung jetzt durch. Weitere Informationen finden Sie unter [Registrieren des Ressourcenanbieters](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

## <a name="find-offer"></a>Suchen nach dem Angebot

Verwenden Sie das Azure-Portal, um Logz.io im Azure Marketplace zu finden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Falls Sie während einer der letzten Sitzungen den **Marketplace** besucht haben, können Sie das Symbol aus den verfügbaren Optionen auswählen. Suchen Sie andernfalls nach _Marketplace_.

    :::image type="content" source="./media/create/marketplace.png" alt-text="Azure Marketplace":::

1. Suchen Sie im Azure Marketplace nach _Logz.io_.
1. Wählen Sie **Logz.io – Observability based on ELK & Prometheus** (Logz.io – Einblicke basierend auf ELK & Prometheus) aus.
1. Wählen Sie die Schaltfläche **Einrichten + abonnieren** aus. Das Fenster **Neues Logz.io-Konto erstellen** wird geöffnet.

    :::image type="content" source="./media/create/logzio-app.png" alt-text="Logz.io-Anwendung für Einblicke":::

## <a name="create-new-logzio-account"></a>Erstellen eines neuen Logz.io-Kontos

Geben Sie auf der Registerkarte **Grundlagen** des Bildschirms **Logz.io-Konto erstellen** die folgenden Werte ein:

| Eigenschaft | BESCHREIBUNG |
| ---- | ---- |
| **Abonnement** | Wählen Sie im Dropdownmenü das Azure-Abonnement aus, für das Sie über Zugriff als Besitzer verfügen. |
| **Ressourcengruppe** | Geben Sie an, ob Sie eine neue Ressourcengruppe erstellen oder eine vorhandene verwenden möchten. Eine [Ressourcengruppe](../../azure-resource-manager/management/overview.md#resource-groups) ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. |
| **Name des Logz-Kontos** | Geben Sie den Namen des Logz.io-Kontos ein, das Sie erstellen möchten. |
| **Location** | Wählen Sie **USA, Westen 2** oder **Europa, Westen** aus. Logz.io unterstützt nur diese Azure-Regionen. |
| **Pricing Plan** (Tarif) | Wählen Sie einen Plan aus der Liste der verfügbaren Logz.io-Pläne aus. |
| **Abrechnungszeitraum** | **Monatlich** ist der Standardwert. |
| **Preis** | Wird basierend auf dem ausgewählten Logz.io-Plan angegeben. |

:::image type="content" source="./media/create/basics.png" alt-text="Logz.io-Konto erstellen":::

Wählen Sie nach der Eingabe der Werte die Schaltfläche **Weiter: Protokolle und Metriken** aus.

## <a name="configure-logs"></a>Konfigurieren von Protokollen

Geben Sie auf der Registerkarte **Protokolle und Metriken** an, welche Azure-Ressourcen Protokolle und Metriken an Logz.io senden werden.

Es gibt zwei Arten von Protokollen, die von Azure an Logz.io gesendet werden können:

- **Protokolle auf Abonnementebene**: Diese bieten Einblicke in die Vorgänge für jede Azure-Ressource im Abonnement von außen (die Verwaltungsebene) sowie Updates zu **Service Health**-Ereignissen. Über das **Aktivitätsprotokoll** können Sie für alle Schreibvorgänge (PUT, POST, DELETE) für die Ressourcen Ihres Abonnements ermitteln, welcher Benutzer welche Vorgang zu welchem Zeitpunkt ausgeführt hat. Es gibt jeweils ein **Aktivitätsprotokoll** für jedes Azure-Abonnement.
- **Azure-Ressourcenprotokolle**: Diese bieten Einblicke in Vorgänge, die für eine Azure-Ressource ausgeführt wurden (die Datenebene). Beispiele hierfür sind das Abrufen eines Geheimnisses aus einem Schlüsseltresor oder das Senden einer Anforderung an eine Datenbank. Der Inhalt dieser Protokolle variiert je nach Azure-Dienst und -Ressourcentyp.

Protokolle auf Abonnementebene können durch Aktivieren des Kontrollkästchens **Protokolle auf Abonnementebene senden** an Logz.io gesendet werden. Wenn das Kontrollkästchen nicht aktiviert ist, wird keins der Protokoll auf Abonnementebene an Logz.io gesendet.

Azure-Ressourcenprotokolle können durch Aktivieren des Kontrollkästchens **Azure-Ressourcenprotokolle für alle definierten Ressourcen senden** an Logz.io gesendet werden. Die Typen von Azure-Ressourcenprotokollen sind unter [Azure Monitor-Ressourcenprotokollkategorien](../../azure-monitor/essentials/resource-logs-categories.md)aufgeführt. Um bestimmte Azure-Ressourcen zu filtern, die Protokolle an Logz.io senden, können Sie Azure-Ressourcentags verwenden.

Die Tagregeln zum Senden von Protokollen lauten wie folgt:

- Standardmäßig werden Protokolle für alle Ressourcen gesammelt.
- Azure-Ressourcen mit _Include_-Tags senden Protokolle an Logz.io.
- Azure-Ressourcen mit _Exclude_-Tags senden **keine** Protokolle an Logz.io.
- Wenn ein Konflikt zwischen Einschluss- und Ausschlussregeln vorliegt, hat der Ausschluss Priorität.

:::image type="content" source="./media/create/logs.png" alt-text="Einrichten von Protokollen und Metriken":::

Nach dem Konfigurieren der Protokolle und Metriken wählen Sie die Schaltfläche **Weiter: Tags** aus.

## <a name="add-custom-tags"></a>Benutzerdefinierte Tags hinzufügen

Sie können benutzerdefinierte Tags für die neue Logz.io-Ressource angeben, indem Sie Schlüssel-Wert-Paare hinzufügen.

Jedes Schlüssel-Wert-Paar enthält einen **Namen** und einen **Wert**:

| Eigenschaft | BESCHREIBUNG |
| ---- | ---- |
| **Name** | Name des Tags, entsprechend der Azure Logz.io-Ressource. |
| **Wert** | Wert des Tags, entsprechend der Azure Logz.io-Ressource. |

:::image type="content" source="./media/create/tags.png" alt-text="Hinzufügen von benutzerdefinierten Tags":::

Nach dem Hinzufügen von Tags wählen Sie die Schaltfläche **Weiter: Einmaliges Anmelden** aus.

## <a name="configure-single-sign-on"></a>Einmaliges Anmelden konfigurieren

Das einmalige Anmelden (Single Sign-On, SSO) ist ein optionales Feature:

- Wenn Sie SSO nicht verwenden möchten, überspringen Sie diesen Schritt.
- Informationen zum Aktivieren von SSO finden Sie unter [Einrichten des einmaligen Anmeldens mit Logz.io](setup-sso.md).

Nach dem Konfigurieren von AAD wählen Sie Ihre Logz.io-SSO-Anwendung auf der Registerkarte **Einmaliges Anmelden** aus.

:::image type="content" source="./media/create/sso.png" alt-text="Konfigurieren des einmaligen Anmeldens":::

Wählen Sie die Schaltfläche **Weiter: Überprüfen + erstellen** aus, um zum letzten Schritt der Ressourcenerstellung zu gelangen.

## <a name="create-resource"></a>Erstellen von Ressourcen

Auf der Seite **Überprüfen + erstellen** wurden alle Überprüfungen ausgeführt. Sie können alle ausgewählten Optionen auf den Registerkarten **Grundlagen**, **Protokolle und Metriken**, **Tags** und **Einmaliges Anmelden** überprüfen. Sie können auch die Nutzungsbedingungen für Logz.io und den Azure Marketplace lesen.

Überprüfen Sie alle Informationen, um sich davon zu überzeugen, dass alles richtig ist. Um die Bereitstellung zu starten, wählen Sie die Schaltfläche **Erstellen** aus.

:::image type="content" source="./media/create/create-resource.png" alt-text="Überprüfen und Erstellen des Kontos":::

Nach der erfolgreichen Bereitstellung können Sie die bereitgestellte Logz.io-Ressourcen anzeigen, indem Sie die Schaltfläche **Zu Ressource wechseln** auswählen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre Logz.io-Integration [verwalten](manage.md).
- Informationen zum Beheben von Problemen mit der Integration finden Sie unter [Problembehandlung](troubleshoot.md).
