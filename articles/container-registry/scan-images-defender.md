---
title: Überprüfen von Registrierungsimages mit Azure Defender
description: Hier erfahren Sie, wie Sie Azure Defender für Containerregistrierungen verwenden, um Images in Ihren Azure-Containerregistrierungen zu überprüfen.
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: d00e23ffa7e3bd2fc1084ba6253274d14031d624
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553066"
---
# <a name="scan-registry-images-with-azure-defender"></a>Überprüfen von Registrierungsimages mit Azure Defender

Um Images in Ihren Azure-Containerregistrierungen auf Sicherheitsrisiken zu überprüfen, können Sie eine der verfügbaren Azure Marketplace-Lösungen integrieren oder optional **Azure Defender für Containerregistrierungen** auf der Abonnementebene aktivieren, wenn Sie Azure Security Center verwenden möchten. 

* Weitere Informationen zu [Azure Defender für Containerregistrierungen](../security-center/defender-for-container-registries-introduction.md)
* Weitere Informationen zur [Containersicherheit in Azure Security Center](../security-center/container-security.md)

## <a name="registry-operations-by-azure-defender"></a>Registrierungsvorgänge von Azure Defender

Azure Defender überprüft an eine Registrierung gepushte, in eine Registrierung importierte oder alle innerhalb der letzten 30 Tage gepullten Images. Wenn Sicherheitsrisiken erkannt werden, werden [empfohlene Wartungsmaßnahmen](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings) in Azure Security Center angezeigt.

 Nachdem Sie die empfohlenen Schritte zum Beheben des Sicherheitsproblems ausgeführt haben, ersetzen Sie das Image in Ihrer Registrierung. Azure Defender überprüft das Image erneut, um zu bestätigen, dass die Sicherheitsrisiken behoben wurden. 

Ausführliche Informationen finden Sie unter [Verwenden von Azure Defender für Containerregistrierungen](../security-center/defender-for-container-registries-usage.md).

> [!TIP]
> Azure Defender authentifiziert sich bei der Registrierung, um Images zur Überprüfung auf Sicherheitsrisiken zu pullen. Wenn [Ressourcenprotokolle](monitor-service-reference.md#resource-logs) für Ihre Registrierung gesammelt werden, enthalten diese von Azure Defender generierte Anmeldeereignisse und Ereignisse für Imagepullvorgänge für die Registrierung. Diesen Ereignissen ist eine alphanumerische ID zugeordnet, z. B. `b21cb118-5a59-4628-bab0-3c3f0e434cg6`.

## <a name="scanning-a-network-restricted-registry"></a>Überprüfen einer Registrierung mit Netzwerkeinschränkungen

Azure Defender kann Images in einer öffentlich zugänglichen Containerregistrierung oder einer mit Netzwerkzugriffsregeln geschützten Containerregistrierung überprüfen. Wenn Netzwerkregeln konfiguriert sind (d. h. Sie deaktivieren den öffentlichen Zugriff auf die Registrierung, konfigurieren IP-Zugriffsregeln oder erstellen private Endpunkte), müssen Sie die Netzwerkeinstellung zum [**Zulassen des Zugriffs vertrauenswürdiger Microsoft-Dienste**](allow-access-trusted-services.md) auf die Registrierung aktivieren. Diese Einstellung ist in neuen Containerregistrierungen standardmäßig aktiviert.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über den Registrierungszugriff durch [vertrauenswürdige Dienste](allow-access-trusted-services.md).
* Informationen zum Einschränken des Zugriffs auf eine Registrierung mithilfe eines privaten Endpunkts in einem virtuellen Netzwerk finden Sie unter [Konfigurieren von Azure Private Link für eine Azure-Container Containerregistrierung](container-registry-private-link.md).
* Informationen zum Einrichten von Firewallregeln für die Registrierung finden Sie unter [Konfigurieren von Netzwerkregeln für öffentliche IP-Adressen](container-registry-access-selected-networks.md).
