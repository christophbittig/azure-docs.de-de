---
title: Datei einfügen
description: Datei einfügen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 282fee2bdb9fa6cd063d2c041b57bb1ee5513926
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353957"
---
Diese Beispiele veranschaulichen verschiedene Aufgaben, die in mandantenübergreifenden Verwaltungsszenarien ausgeführt werden können.

| **Vorlage** | **Beschreibung** |
|---------|---------|
| [`create-keyvault-secret`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | Erstellt eine Key Vault-Instanz im Mandanten des Kunden und erstellt Zugriffsrichtlinien.
| [`cross-rg-deployment`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Stellt Speicherkonten in zwei verschiedenen Ressourcengruppen bereit.|
| [`deploy-azure-mgmt-services`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Erstellt Azure-Verwaltungsdienste, verknüpft sie miteinander und stellt zusätzliche Lösungen bereit. Verwenden Sie für eine End-to-End-Bereitstellung die Vorlage **rgWithAzureMgmt.json**. |
| [`deploy-azure-security-center`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Aktiviert und konfiguriert Microsoft Defender für Cloud innerhalb des Azure-Zielabonnements. |
| [`deploy-azure-sentinel`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Stellt Microsoft Sentinel für einen vorhandenen Log Analytics-Arbeitsbereich in einem delegierten Abonnement bereit und aktiviert die Lösung. |
| [`deploy-log-analytics-vm-extensions`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Mithilfe dieser Vorlagen können Sie Log Analytics-Erweiterung für virtuelle Computer für Ihre Windows- und Linux-VMs bereitstellen und sie mit dem vorgesehenen Log Analytics-Arbeitsbereich verbinden. |
