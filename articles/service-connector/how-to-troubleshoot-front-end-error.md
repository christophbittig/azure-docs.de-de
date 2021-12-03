---
title: Leitfaden zur Problembehandlung für den Service Connector
description: Fehlerliste und vorgeschlagene Aktionen des Service Connectors
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8a1379495dacba7d2c0cf21af3a1e5ec2f45ed41
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283181"
---
# <a name="how-to-troubleshoot-with-service-connector"></a>Problembehandlung mit dem Service Connector

Wenn ein Problem auftritt, können Sie in der Fehlermeldung nach vorgeschlagenen Aktionen oder Fehlerbehebungen suchen. In dieser Anleitung werden die Optionen zur Problembehandlung für den Service Connector veranschaulicht.

## <a name="error-message-and-suggested-actions-from-portal"></a>Fehlermeldung und vorgeschlagene Aktionen aus dem Portal

| Fehlermeldung | Vorgeschlagene Maßnahme |
| --- | --- |
| Unbekannter Ressourcentyp | <ul><li>Checken Sie die Quell- und Zielressource, um zu überprüfen, ob die Diensttypen vom Service Connector unterstützt werden.</li><li>Überprüfen Sie, ob die angegebene Quell-Ziel-Verbindungskombination vom Service Connector unterstützt wird.</li></ul> |
| Unbekannter Ressourcentyp | <ul><li>Überprüfen Sie, ob die Zielressource vorhanden ist.</li><li>Überprüfen Sie die Richtigkeit der Zielressourcen-ID.</li></ul> |
| Nicht unterstützter Ressourcentyp | <ul><li>Überprüfen Sie, ob die angegebene Quell-Ziel-Verbindungskombination vom Service Connector unterstützt wird.</li></ul> |

### <a name="error-typeerror-message-and-suggested-actions-using-azure-cli"></a>Fehlertyp, Fehlermeldung und vorgeschlagene Aktionen mit Azure CLI

### <a name="invalidargumentvalueerror"></a>InvalidArgumentValueError


| Fehlermeldung | Vorgeschlagene Maßnahme |
| --- | --- |
| Die Ressourcen-ID ist ungültig `{SourceId}` | <ul><li>Überprüfen Sie, ob die Quellressourcen-ID vom Service Connector unterstützt wird.</li><li>Überprüfen Sie die Richtigkeit der Quellressourcen-ID.</li></ul> |
| Die Ressourcen-ID ist ungültig: `{TargetId}` | <ul><li>Überprüfen Sie, ob der Zieldiensttyp vom Service Connector unterstützt wird.</li><li>Überprüfen Sie die Richtigkeit der Zielressourcen-ID.</li></ul> |
| Die Verbindungs-ID ist ungültig: `{ConnectionId}` | <ul><li>Überprüfen Sie die Richtigkeit der Verbindungs-ID.</li></ul> |


### <a name="requiredargumentmissingerror"></a>RequiredArgumentMissingError

| Fehlermeldung | Vorgeschlagene Maßnahme |
| --- | --- |
| `{Argument}` darf nicht leer sein | Benutzer sollte Argumentwert für interaktive Eingabe angeben |
| Erforderliche Schlüssel fehlen für den Parameter `{Parameter}`. Alle möglichen Schlüssel sind: `{Keys}` | Geben Sie einen Wert für den Authentifizierungs-Informationsparameter an, in der Regel in Form von `--param key1=val1 key2=val2`. |
| Erforderliches Argument fehlt. Geben Sie die Argumente an: `{Arguments}` | Geben Sie das erforderliche Argument an. | 

### <a name="validationerror"></a>ValidationError

| Fehlermeldung | Vorgeschlagene Maßnahme |
| --- | --- |
| Es ist nur eine Authentifizierungs-Information erforderlich | Der Benutzer kann nur einen Authentifizierungs-Informationsparameter angeben, überprüfen, ob keine Authentifizierungs-Informationen bereitgestellt werden oder mehrere Authentifizierungs-Informationsparameter bereitgestellt werden. |
| Das Authentifizierungs-Informationsargument sollte beim Aktualisieren der Verbindung angegeben werden: `{ConnectionName}` | Beim Aktualisieren einer Geheimnistypverbindung sollte der Parameter für die Authentifizierungs-Informationen angegeben werden. (Dies liegt daran, dass auf das Geheimnis des Benutzers nicht über die ARM-API zugegriffen werden kann.) |
| Für die Aktualisierung sollten entweder Clienttyp- oder Authentifizierungs-Informationen angegeben werden | Beim Aktualisieren einer Verbindung sollten entweder Clienttyp- oder Authentifizierungs-Informationen angegeben werden. |
| Verwendungsfehler: {} [KEY=VALUE ...] | Überprüfen Sie die verfügbaren Schlüssel und geben Sie die Werte für den Parameter für die Authentifizierungs-Informationen an, in der Regel in Form von `--param key1=val1 key2=val2`. |
| Nicht unterstützter Schlüssel `{Key}` wird für den Parameter `{Parameter}` bereitgestellt. Alle möglichen Schlüssel sind: `{Keys}` | Überprüfen Sie die verfügbaren Schlüssel und geben Sie die Werte für den Parameter für die Authentifizierungs-Informationen an, in der Regel in Form von `--param key1=val1 key2=val2`. |
| Fehler bei der Bereitstellung. Erstellen Sie die Zielressource manuell, und erstellen Sie dann die Verbindung. Fehlerdetails: `{ErrorTrace}` | <ul><li>Wiederholen.</li><li>Fehler bei der Bereitstellung. Erstellen Sie die Zielressource manuell, und erstellen Sie dann die Verbindung.</li></ul> |

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
