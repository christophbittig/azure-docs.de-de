---
title: 'Fehler bei Azure Digital Twins-Anforderung mit Status: 403 (Unzulässig)'
description: 'Ursachen und Behebungen für „Fehler bei Service Request. Status: 403 (Unzulässig)“ bei Azure Digital Twins.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 8/20/2021
ms.openlocfilehash: b3ad9c84e35483cf81bde83703b01ef0ff3d8a9d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772330"
---
# <a name="service-request-failed-status-403-forbidden"></a>Fehler bei Service Request. Status: 403 (Unzulässig)

In diesem Artikel werden die Ursachen und Schritte zur Behebung beim Empfang eines 403-Fehlers bei Service Requests an Azure Digital Twins beschrieben. 

## <a name="symptoms"></a>Symptome

Dieser Fehler kann bei vielen Service Request-Typen auftreten, für die eine Authentifizierung erforderlich ist. Die Auswirkung ist ein Fehler bei der API-Anforderung mit Rückgabe des Fehlerstatus `403 (Forbidden)`.

## <a name="causes"></a>Ursachen

### <a name="cause-1"></a>Ursache 1

In den meisten Fällen zeigt dieser Fehler an, dass die Azure RBAC-Berechtigungen (Role-Based Access Control, rollenbasierte Zugriffssteuerung in Azure) für den Dienst nicht ordnungsgemäß eingerichtet wurden. Viele Aktionen für eine Azure Digital Twins-Instanz erfordern die Rolle Azure Digital Twins Data Owner (Azure Digital Twins-Datenbesitzer) **für die Instanz, die Sie verwalten möchten**. 

### <a name="cause-2"></a>Ursache 2

Wenn Sie für die Kommunikation mit Azure Digital Twins eine Client-App verwenden, die sich mit einer [App-Registrierung](./how-to-create-app-registration-portal.md) authentifiziert, kann dieser Fehler auftreten, da für Ihre App-Registrierung keine Berechtigungen für den Azure Digital Twins-Dienst eingerichtet wurden.

Die App-Registrierung muss über konfigurierte Zugriffsberechtigungen für die Azure Digital Twins-APIs verfügen. Wenn Ihre Client-App dann bei der App-Registrierung authentifiziert wird, erhält sie die für die App-Registrierung konfigurierten Berechtigungen.

## <a name="solutions"></a>Lösungen

### <a name="solution-1"></a>Lösung 1

Die erste Lösung besteht darin sicherzustellen, dass Ihr Azure-Benutzer über die Rolle „Azure Digital Twins-Datenbesitzer“ für die zu verwaltende Instanz verfügt. Wenn diese Rolle noch nicht vorhanden ist, richten Sie sie ein.

Diese Rolle unterscheidet sich von Folgendem:
* Früherer Name dieser Rolle während der Vorschau: Azure Digital Twins-Besitzer (Vorschau). In diesem Fall ist die Rolle identisch, aber der Name wurde geändert.
* Rolle Besitzer für das gesamte Azure-Abonnement. Azure Digital Twins Data Owner (Azure Digital Twins-Datenbesitzer) ist eine Rolle in Azure Digital Twins und bezieht sich auf diese einzelne Azure Digital Twins-Instanz.
* Rolle Besitzer in Azure Digital Twins. Es handelt sich um zwei verschiedene Verwaltungsrollen für Azure Digital Twins, und Azure Digital Twins Data Owner ist die Rolle, die für die Verwaltung verwendet werden sollte.

#### <a name="check-current-setup"></a>Überprüfen des aktuellen Setups

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Beheben von Problemen 

Wenn Sie nicht über diese Rollenzuweisung verfügen, muss eine Person mit der Rolle „Besitzer“ in Ihrem **Azure-Abonnement** den folgenden Befehl ausführen, um Ihrem Azure-Benutzer die Rolle „Azure Digital Twins-Datenbesitzer“ für die **Azure Digital Twins-Instanz** zuzuweisen. 

Wenn Sie ein Besitzer des Abonnements sind, können Sie diesen Befehl selbst ausführen. Bitten Sie andernfalls einen Besitzer, diesen Befehl für Sie auszuführen.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

Weitere Informationen zu dieser Rollenanforderung und zum Zuweisungsprozess finden Sie im Abschnitt [Einrichten von Benutzerzugriffsberechtigungen](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) im Artikel *Einrichten einer Azure Digital Twins-Instanz und der Authentifizierung (CLI)* .

Wenn diese Rollenzuweisung bereits vorhanden ist **und** Sie eine Azure AD-App-Registrierung zur Authentifizierung einer Client-App verwenden, können Sie mit der nächsten Lösung fortfahren, falls die vorliegende Lösung das 403-Problem nicht behoben hat.

### <a name="solution-2"></a>Lösung 2

Wenn Sie eine Azure AD-App-Registrierung verwenden, um eine Client-App zu authentifizieren, besteht die zweite mögliche Lösung darin, zu überprüfen, ob die App-Registrierung über Berechtigungen verfügt, die für den Azure Digital Twins-Dienst konfiguriert sind. Wenn diese nicht konfiguriert sind, richten Sie sie ein.

#### <a name="check-current-setup"></a>Überprüfen des aktuellen Setups

Wenn Sie überprüfen möchten, ob die Berechtigungen ordnungsgemäß konfiguriert wurden, navigieren Sie im Azure-Portal zur [Übersichtsseite der Azure AD-App-Registrierung](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Sie können selbst auf diese Seite gelangen, indem Sie über die Portalsuchleiste nach **App-Registrierungen** suchen.

Wechseln Sie zur Registerkarte **Alle Anwendungen**, um alle App-Registrierungen anzuzeigen, die in Ihrem Abonnement erstellt wurden.

Die von Ihnen erstellte App-Registrierung sollte in der Liste angezeigt werden. Wählen Sie sie aus, um die Details zu öffnen.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Screenshot der Seite „App-Registrierungen“ im Azure-Portal.":::

Vergewissern Sie sich zunächst, dass die Einstellungen für die Azure Digital Twins-Berechtigungen ordnungsgemäß in der Registrierung festgelegt wurden: Wählen Sie in der Menüleiste die Option **Manifest** aus, um den Manifestcode der App-Registrierung anzuzeigen. Scrollen Sie zum unteren Bereich des Codefensters, und suchen Sie nach diesen Feldern unter `requiredResourceAccess`. Die Werte sollten den Werten im folgenden Screenshot entsprechen:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Screenshot des Manifests für die Azure AD-App-Registrierung im Azure-Portal.":::

Wählen Sie als Nächstes auf der Menüleiste **API-Berechtigungen** aus, um sich zu vergewissern, dass diese App-Registrierung Lese-/Schreibberechtigungen für Azure Digital Twins enthält. Es sollte ein Eintrag wie der folgende angezeigt werden:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Screenshot der API-Berechtigungen für die Azure AD-App-Registrierung im Azure-Portal mit „Lese-/Schreibzugriff“ für Azure Digital Twins.":::

#### <a name="fix-issues"></a>Beheben von Problemen

Sollte etwas nicht wie beschrieben aussehen, befolgen Sie die Anweisungen zum Einrichten einer App-Registrierung unter [Erstellen einer App-Registrierung für die Verwendung mit Azure Digital Twins (Portal)](./how-to-create-app-registration-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Einrichtungsschritte zum Erstellen und Authentifizieren einer neuen Azure Digital Twins-Instanz:
* [Einrichten einer Instanz und der Authentifizierung (CLI)](how-to-set-up-instance-cli.md)

Informieren Sie sich weiter über die Sicherheit und Berechtigungen in Azure Digital Twins:
* [Sicherheit für Azure Digital Twins-Lösungen](concepts-security.md)