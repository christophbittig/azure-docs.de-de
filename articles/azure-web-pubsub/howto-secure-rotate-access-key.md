---
title: Rotieren von Zugriffsschlüsseln für den Azure Web PubSub-Dienst
description: Enthält eine Übersicht dazu, warum Kunden routinemäßig die Zugriffsschlüssel rotieren müssen und wie dies durchgeführt wird.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: e89366e924ae7f079816980e6fcbefd6366eb3b0
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997759"
---
# <a name="how-to-rotate-access-key-for-azure-web-pubsub-service"></a>Rotieren von Zugriffsschlüsseln für den Azure Web PubSub-Dienst

Jede Instanz des Azure Web PubSub-Diensts verfügt über ein Zugriffsschlüsselpaar, das aus einem Primär- und einem Sekundärschlüssel besteht. Sie werden verwendet, um Clients zu authentifizieren, wenn Anforderungen an den Dienst gesendet werden. Die Schlüssel werden der Endpunkt-URL der Instanz zugeordnet. Bewahren Sie Ihre Schlüssel sicher auf, und rotieren Sie sie regelmäßig. Es werden zwei Zugriffsschlüssel bereitgestellt, damit Sie die Verbindungen mit einem Schlüssel aufrechterhalten können, während Sie den anderen erneut generieren.

## <a name="why-rotate-access-keys"></a>Warum müssen Zugriffsschlüssel rotiert werden?

Aus Sicherheits- und Konformitätsgründen sollten Sie die Zugriffsschlüssel regelmäßig rotieren.

## <a name="regenerate-access-keys"></a>Erneutes Generieren von Zugriffsschlüsseln

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich mit Ihren Anmeldeinformationen an.

1. Suchen Sie in der Instanz des Azure Web PubSub-Diensts, die die erneut zu generierenden Schlüssel enthält, nach dem Abschnitt **Schlüssel**.

1. Wählen Sie im Navigationsmenü die Option **Schlüssel**.

1. Wählen Sie die Option **Primären Schlüssel erneut generieren** bzw. **Sekundären Schlüssel erneut generieren**.

   Ein neuer Schlüssel und die entsprechende Verbindungszeichenfolge werden erstellt und angezeigt.

Sie können Schlüssel auch erneut generieren, indem Sie die Azure CLI verwenden, nachdem der Azure Web PubSub-Dienst die allgemeine Verfügbarkeit erreicht hat.

## <a name="update-configurations-with-new-connection-strings"></a>Aktualisieren von Konfigurationen mit neuen Verbindungszeichenfolgen

1. Kopieren Sie die neu generierte Verbindungszeichenfolge.

1. Aktualisieren Sie alle Konfigurationen, damit die neue Verbindungszeichenfolge verwendet wird.

1. Starten Sie die Anwendung neu, falls dies erforderlich ist.

## <a name="forced-access-key-regeneration"></a>Erzwungene erneute Generierung des Zugriffsschlüssels

In bestimmten Situationen kann es vorkommen, dass vom Azure Web PubSub-Dienst eine obligatorische erneute Generierung des Zugriffsschlüssels erzwungen wird. Der Dienst benachrichtigt Kunden per E-Mail und Nachricht im Portal. Wenn Sie diese Benachrichtigungen erhalten oder für den Dienst ein Fehler aufgrund des Zugriffsschlüssels auftritt, sollten Sie die Schlüssel rotieren, indem Sie sich an die Anleitung in diesem Leitfaden halten.