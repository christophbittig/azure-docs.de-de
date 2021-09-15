---
title: Verschieben von Azure App Service-Ressourcen
description: Verwenden Sie Azure Resource Manager, um App Service-Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement zu verschieben.
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 34f86cfd3a3822b77992f5d1ae77afaf14fdc7ab
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251456"
---
# <a name="move-guidance-for-app-service-resources"></a>Anleitung zum Verschieben von App Service-Ressourcen

In diesem Artikel werden die Schritte zum Verschieben von App Service-Ressourcen beschrieben. Für das Verschieben von App Service-Ressourcen in ein neues Abonnement gelten bestimmte Anforderungen.

## <a name="move-across-subscriptions"></a>Verschieben zwischen Abonnements

Beim Verschieben einer Web-App zwischen Abonnements gelten die folgenden Richtlinien:

- Das Verschieben einer Ressource in eine neue Ressourcengruppe oder ein neues Abonnement ist eine Metadatenänderung, die sich nicht auf die Funktionsweise der Ressource auswirken sollte. Beispielsweise ändert sich durch das Verschieben eines App Service nicht die eingehende IP-Adresse für den App Service.
- Die Zielressourcengruppe darf keine App Service-Ressourcen besitzen. Zu App Service-Ressourcen zählen:
    - Web-Apps
    - App Service-Pläne
    - Hochgeladene oder importierte TLS/SSL-Zertifikate
    - App Service-Umgebungen
- Alle App Service-Ressourcen in der Ressourcengruppe müssen zusammen verschoben werden.
- App Service-Umgebungen können nicht in eine neue Ressourcengruppe oder ein neues Abonnement verschoben werden. Sie können jedoch eine Web-App und einen App Service-Plan in ein neues Abonnement verschieben, ohne die App Service-Umgebung zu verschieben. Nach dem Verschieben wird die Web-App nicht mehr in der App Service-Umgebung gehostet.
- Sie können ein Zertifikat, das an ein Web gebunden ist, ohne Löschen der TLS-Bindungen verschieben, solange das Zertifikat mit allen anderen Ressourcen in der Ressourcengruppe verschoben wird. Sie können jedoch kein kostenloses verwaltetes App Service-Zertifikat verschieben. Weitere Informationen zu diesem Szenario finden Sie unter [Verschieben mit kostenlosen verwalteten Zertifikaten](#move-with-free-managed-certificates).
- App Service-Ressourcen können nur aus der Ressourcengruppe verschoben werden, in der sie ursprünglich erstellt wurden. Wenn eine App Service-Ressource nicht mehr in ihrer ursprünglichen Ressourcengruppe enthalten ist, verschieben Sie sie zurück zu ihrer ursprünglichen Ressourcengruppe. Verschieben Sie dann die Ressource zwischen Abonnements. Hinweise zum Finden der ursprünglichen Ressourcengruppe finden Sie im nächsten Abschnitt.

## <a name="find-original-resource-group"></a>Finden der ursprünglichen Ressourcengruppe

Wenn Sie sich nicht an die ursprüngliche Ressourcengruppe erinnern, können Sie diese mithilfe der Diagnosefunktion finden. Wählen Sie für Ihre Web-App **Diagnose und Problembehandlung** aus. Wählen Sie dann **Konfiguration und Verwaltung** aus.

![„Diagnose“ auswählen](./media/app-service-move-limitations/select-diagnostics.png)

Wählen Sie **Migrationsoptionen** aus.

![Migrationsoptionen auswählen](./media/app-service-move-limitations/select-migration.png)

Wählen Sie die Option für empfohlene Schritte, um die Web-App zu verschieben.

![„Empfohlene Schritte“ auswählen](./media/app-service-move-limitations/recommended-steps.png)

Sie sehen die empfohlenen Maßnahmen, die Sie ergreifen sollten, bevor Sie die Ressourcen verschieben. Die Informationen enthalten die ursprüngliche Ressourcengruppe für die Web-App.

![Screenshot zeigt empfohlene Schritte zum Verschieben von Microsoft-Punkt-Webressourcen.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-hidden-resource-types-in-portal"></a>Verschieben ausgeblendeter Ressourcentypen im Portal

Wenn Sie das Portal zum Verschieben Ihrer App Service-Ressourcen verwenden, werden Sie möglicherweise in einem Fehler darauf hingewiesen, dass nicht alle Ressourcen verschoben wurden. Wenn dieser Fehler angezeigt wird, überprüfen Sie, ob bestimmte Ressourcentypen im Portal nicht angezeigt wurden. Wählen Sie **Ausgeblendete Typen anzeigen** aus. Wählen Sie dann alle zu verschiebenden Ressourcen aus.

:::image type="content" source="./media/app-service-move-limitations/show-hidden-types.png" alt-text="Ausgeblendete Typen anzeigen":::

## <a name="move-with-free-managed-certificates"></a>Verschieben mit kostenlosen verwalteten Zertifikaten

Ein kostenloses verwaltetes App Service-Zertifikat kann nicht verschoben werden. Stattdessen löschen Sie das verwaltete Zertifikat und erstellen es nach dem Verschieben der Web-App neu. Anweisungen zum Löschen des Zertifikats erhalten Sie über das Tool **Migrationsvorgänge**.

Wenn Ihr kostenloses App Service-Zertifikat in einer unerwarteten Ressourcengruppe erstellt wird, versuchen Sie, den App Service-Plan zurück in die ursprüngliche Ressourcengruppe zu verschieben. Erstellen Sie dann das kostenlose verwaltete Zertifikat neu. Dieses Problem wird behoben.

## <a name="move-support"></a>Unterstützung für das Verschieben

Um zu bestimmen, welche App Service-Ressourcen verschoben werden können, sehen Sie sich den Supportstatus für Folgendes an:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Nächste Schritte

Befehle zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../move-resource-group-and-subscription.md).
