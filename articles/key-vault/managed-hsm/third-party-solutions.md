---
title: Verwaltetes HSM von Azure Key Vault – Lösungen von Drittanbietern | Microsoft-Dokumentation
description: Erfahren Sie mehr über Lösungen von Drittanbietern, die in verwaltetes HSM integriert sind.
services: key-vault
author: mbaldwin
editor: ''
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: mbaldwin
ms.openlocfilehash: e70afc4a89fc0c9ce5c6ec59cd795b83b5e078b0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443467"
---
# <a name="third-party-solutions"></a>Lösungen von Drittanbietern

Verwaltetes HSM von Azure Key Vault ist ein vollständig verwalteter, hochverfügbarer und standardkonformer Einzelmandanten-Clouddienst, der es Ihnen ermöglicht, kryptografische Schlüssel für Ihre Cloudanwendungen über HSMs zu schützen, die mit **FIPS 140-2 Level 3** validiert sind. [Weitere Informationen](overview.md)

Mehrere Anbieter haben eng mit Microsoft zusammengearbeitet, um ihre Lösungen in verwaltetes HSM zu integrieren. In der folgenden Tabelle sind diese Lösungen mit einer kurzen Beschreibung (vom Anbieter bereitgestellt) aufgeführt. Links zum jeweiligen Azure Marketplace und zur Dokumentation werden ebenfalls bereitgestellt.


## <a name="third-party-solutions-integrated-with-managed-hsm"></a>In verwaltetes HSM integrierte Drittanbieterlösungen

| Herstellername | Beschreibung der Lösung |
|-------------|-------------------------------------------------|
|[Cloudflare](https://cloudflare.com)|Das schlüssellose SSL von Cloudflare ermöglicht es Ihren Websites, den SSL-Dienst von Cloudflare zu verwenden, während ihre privaten Schlüssel im verwalteten HSM verwahrt werden. Dieser Dienst sorgt in Verbindung mit verwaltetem HSM für ein hohes Maß an Schutz, indem er Ihre privaten Schlüssel schützt, Signier- und Verschlüsselungsvorgänge intern durchführt, Zugriffssteuerungen bereitstellt und Schlüssel in einem manipulationssicheren HSM mit FIPS 140-2 Level 3 speichert. <br>[Dokumentation](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/azure-managed-hsm)
|[NewNet Communication Technologies](https://newnet.com/)|Secure Transaction Cloud (STC) von NewNet ist eine branchenweit erste cloudbasierte Lösung für sicheres Routing, Switching und Transport von Zahlungen, ergänzt durch ein cloudbasiertes, virtualisiertes HSM, das mobile, Web- und In-Store-Zahlungen verarbeitet. STC ermöglicht Cloudtransformation für Zahlungsentitäten und schnelle Bereitstellung für Greenfield-Zahlungsanbieter.<br/>[Angebot im Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/newnetcommunicationtechnologies1589991852134.secure_transaction_cloud?tab=overview)<br/>[Dokumentation](https://newnet.com/business-units/secure-transactions/products/secure-transaction-cloud-stc/)|
|[PrimeKey](https://www.primekey.com)|EJBCA Enterprise, die weltweit meistgenutzte PKI (Public Key-Infrastruktur), bietet die grundlegenden Sicherheitsdienste für vertrauenswürdige Identitäten und sichere Kommunikation für jeden Anwendungsfall. Eine einzige Instanz von EJBCA Enterprise unterstützt mehrere CAs und Ebenen, so dass Sie vollständige Infrastruktur(en) für mehrere Anwendungsfälle erstellen können.<br>[Angebot im Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/primekey.ejbca_enterprise_cloud_2)<br/>[Dokumentation](https://doc.primekey.com/x/a4z_/)|



## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über verwaltetes HSM](overview.md)
* [Bewährte Methoden für verwaltetes HSM](best-practices.md)

