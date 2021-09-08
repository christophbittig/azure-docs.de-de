---
title: Fehler bei der Azure Digital Twins-Explorer-Authentifizierung
description: Ursachen und Lösungen für die Meldung „Fehler bei der Authentifizierung“ im Azure Digital Twins-Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: ef78f1dd6089201744de19900738ebcc6e5f70db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468670"
---
# <a name="authentication-failed"></a>Fehler bei der Authentifizierung.

In diesem Artikel werden die Ursachen und Lösungsschritte für die Anzeige der Meldung „Fehler bei der Authentifizierung“ beim Ausführen des [Azure Digital Twins-Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)-Beispiels auf Ihrem lokalen Computer beschrieben. 

## <a name="symptoms"></a>Symptome

Beim Einrichten und Ausführen des Azure Digital Twins-Explorers führen Versuche, sich bei der App zu authentifizieren, zur Anzeige der folgenden Fehlermeldung:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Screenshot einer Fehlermeldung für einen Authentifizierungsfehler im Azure Digital Twins-Explorer.":::

## <a name="causes"></a>Ursachen

### <a name="cause-1"></a>Ursache 1

Zu diesem Fehler kann es kommen, wenn Ihr Azure-Konto nicht über die benötigten Azure-Berechtigungen für die rollenbasierte Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC) für Ihre Azure Digital Twins-Instanz verfügt. Um auf Daten in Ihrer Instanz zugreifen zu können, müssen Sie über die Rolle **Leseberechtigter für Azure Digital Twins-Daten** oder **Azure Digital Twins-Datenbesitzer** für die Instanz verfügen, für die Sie Lesevorgänge ausführen bzw. die Sie verwalten möchten. 

Weitere Informationen zu Sicherheit und Rollen in Azure Digital Twins finden Sie unter [Sicherheit für Azure Digital Twins-Lösungen](concepts-security.md).

## <a name="solutions"></a>Lösungen

### <a name="solution-1"></a>Lösung 1

Stellen Sie sicher, dass Ihr Azure-Benutzer für die Azure Digital Twins-Instanz über die Rolle **Leseberechtigter für Azure Digital Twins-Daten** (wenn nur die zugehörigen Daten gelesen werden sollen) oder über die Rolle **Azure Digital Twins-Datenbesitzer** verfügt (wenn die zugehörigen Daten verwaltet werden sollen).

Beachten Sie, dass sich diese Rolle von folgenden Rollen unterscheidet:
* Vom früheren Namen für diese Rolle während der Vorschauphase: *Azure Digital Twins-Besitzer (Vorschau)* (Die Rolle ist dieselbe, der Name hat sich jedoch geändert.)
* Rolle *Besitzer* für das gesamte Azure-Abonnement. *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) ist eine Rolle in Azure Digital Twins und bezieht sich auf diese einzelne Azure Digital Twins-Instanz.
* Rolle *Besitzer* in Azure Digital Twins. Es handelt sich um zwei verschiedene Verwaltungsrollen für Azure Digital Twins, und *Azure Digital Twins Data Owner* ist die Rolle, die für die Verwaltung verwendet werden sollte.

 Wenn keine dieser Rollen zugewiesen ist, richten Sie sie ein, um das Problem zu lösen.

#### <a name="check-current-setup"></a>Überprüfen des aktuellen Setups

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Beheben von Problemen 

Wenn Sie nicht über diese Rollenzuweisung verfügen, muss eine Person mit der Rolle „Besitzer“ in Ihrem **Azure-Abonnement** den folgenden Befehl ausführen, um Ihrem Azure-Benutzer die geeignete Rolle für die **Azure Digital Twins-Instanz** zuzuweisen. 

Wenn Sie ein Besitzer des Abonnements sind, können Sie diesen Befehl selbst ausführen. Bitten Sie andernfalls einen Besitzer, diesen Befehl für Sie auszuführen. Der Rollenname lautet für den Bearbeitungszugriff **Azure Digital Twins-Datenbesitzer** oder für den Lesezugriff **Leseberechtigter für Azure Digital Twins-Daten**.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Weitere Informationen zu dieser Rollenanforderung und zum Zuweisungsvorgang finden Sie im Abschnitt [Einrichten der Zugriffsberechtigungen für Ihren Benutzer](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) unter *Gewusst wie: Einrichten einer Instanz und der Authentifizierung (CLI oder Portal)* .

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Einrichtungsschritte zum Erstellen und Authentifizieren einer neuen Azure Digital Twins-Instanz:
* [Einrichten einer Instanz und der Authentifizierung (CLI)](how-to-set-up-instance-cli.md)

Informieren Sie sich weiter über die Sicherheit und Berechtigungen in Azure Digital Twins:
* [Sicherheit für Azure Digital Twins-Lösungen](concepts-security.md)
