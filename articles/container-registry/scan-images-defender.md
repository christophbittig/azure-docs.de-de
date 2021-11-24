---
title: Überprüfen von Registrierungsprofilen mit Microsoft Defender für Cloud
description: Hier erfahren Sie, wie Sie Mirosoft Defender für Containerregistrierungen verwenden, um Profile in Ihren Azure-Containerregistrierungen zu überprüfen
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: 0170895ede2be40cc57d9ecfd82806e1ee3a617c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323484"
---
# <a name="scan-registry-images-with-microsoft-defender-for-cloud"></a>Überprüfen von Registrierungsprofilen mit Microsoft Defender für Cloud

Um Profile in Ihren Azure-Containerregistrierungen auf Sicherheitsrisiken zu überprüfen, können Sie eine der verfügbaren Azure Marketplace-Lösungen integrieren oder optional **Microsoft Defender für Cloud** auf der Abonnementebene für Containerregistrierungen aktivieren. 

* Weitere Informationen zu [Microsoft Defender für Containerregistrierungen](../security-center/defender-for-container-registries-introduction.md)
* [Containersicherheit in Microsoft Defender für Cloud](../security-center/container-security.md)

## <a name="registry-operations-by-microsoft-defender-for-cloud"></a>Registrierungsvorgänge von Microsoft Defender für Cloud

Microsoft Defender überprüft an eine Registrierung übertragene, in eine Registrierung importierte oder alle innerhalb der letzten 30 Tage abgerufenen Profile. Wenn Sicherheitsrisiken erkannt werden, werden [empfohlene Wartungsmaßnahmen](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings) in Microsoft Defender für Cloud angezeigt.

 Nachdem Sie die empfohlenen Schritte zum Beheben des Sicherheitsproblems ausgeführt haben, ersetzen Sie das Image in Ihrer Registrierung. Microsoft Defender überprüft das Profil erneut, um zu bestätigen, dass die Sicherheitsrisiken behoben wurden. 

Ausführliche Informationen finden Sie unter [Verwenden von Mirosoft Defender für Containerregistrierungen](../security-center/defender-for-container-registries-usage.md).

> [!TIP]
> Microsoft Defender für Cloud authentifiziert sich bei der Registrierung, um Profile für Schwachstellen-Scans abzurufen. Wenn [Ressourcenprotokolle](monitor-service-reference.md#resource-logs) für Ihre Registrierung gesammelt werden, sehen Sie Registrierungsanmeldeereignisse und Profilabrufereignisse, die von Microsoft Defender für Cloud generiert werden. Diesen Ereignissen ist eine alphanumerische ID zugeordnet, z. B. `b21cb118-5a59-4628-bab0-3c3f0e434cg6`.

## <a name="scanning-a-network-restricted-registry"></a>Überprüfen einer Registrierung mit Netzwerkeinschränkungen

Microsoft Defender kann Profile innerhalb einer öffentlich zugänglichen Containerregistrierung oder einer mit Netzwerkzugriffsregeln geschützten Containerregistrierung überprüfen. Wenn Netzwerkregeln konfiguriert sind (d. h. Sie deaktivieren den öffentlichen Zugriff auf die Registrierung, konfigurieren IP-Zugriffsregeln oder erstellen private Endpunkte), müssen Sie die Netzwerkeinstellung zum [**Zulassen des Zugriffs vertrauenswürdiger Microsoft-Dienste**](allow-access-trusted-services.md) auf die Registrierung aktivieren. Diese Einstellung ist in neuen Containerregistrierungen standardmäßig aktiviert.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über den Registrierungszugriff durch [vertrauenswürdige Dienste](allow-access-trusted-services.md).
* Informationen zum Einschränken des Zugriffs auf eine Registrierung mithilfe eines privaten Endpunkts in einem virtuellen Netzwerk finden Sie unter [Konfigurieren von Azure Private Link für eine Azure-Container Containerregistrierung](container-registry-private-link.md).
* Informationen zum Einrichten von Firewallregeln für die Registrierung finden Sie unter [Konfigurieren von Netzwerkregeln für öffentliche IP-Adressen](container-registry-access-selected-networks.md).
