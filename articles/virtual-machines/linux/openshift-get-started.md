---
title: OpenShift in Azure – Übersicht
description: Eine Übersicht zu OpenShift in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 940973aa0465c94e8df4882af9b2a1a89c65ff87
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687862"
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

OpenShift ist eine offene und erweiterbare Containeranwendungsplattform, die Docker und Kubernetes in das Unternehmen einführt.  

OpenShift enthält Kubernetes für die Containerorchestrierung und -verwaltung. Es fügt entwickler- und vorgangsorientierte Tools hinzu, die Folgendes ermöglichen:

- Schnelle Anwendungsentwicklung
- Einfache Bereitstellung und Skalierung
- Langfristige Lebenszykluswartung für Teams und Anwendungen

Von OpenShift sind mehrere Versionen verfügbar.  Von diesen Versionen können zurzeit nur zwei von Kunden in Azure bereitgestellt werden: OpenShift Container Platform und OKD (vormals OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift ist ein vollständig verwaltetes OpenShift-Angebot, das in Azure ausgeführt wird. Dieser Dienst wird gemeinsam von Microsoft und Red Hat verwaltet und unterstützt. Weitere Informationen finden Sie in der Dokumentation zu [Azure Red Hat OpenShift Service](../../openshift/index.yml).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform ist eine [kommerzielle Version](https://www.openshift.com) für unternehmen von und unterstützt von Red Hat. Mit dieser Version erwirbt der Kunde die erforderlichen Berechtigungen für OpenShift Container Platform und ist für die Installation und Verwaltung der gesamten Infrastruktur zuständig.

Da der Kunde der Besitzer der gesamten Plattform ist, kann die Installation in seinem lokalen Rechenzentrum oder in einer öffentlichen Cloud (z.B. Azure) erfolgen.

## <a name="okd"></a>OKD

OKD ist ein [Open Source](https://www.okd.io/) Upstream-Projekt von OpenShift, das von der Community unterstützt wird. OKD kann auf CentOS oder Red Hat Enterprise Linux (RHEL) installiert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren allgemeiner Voraussetzungen für OpenShift in Azure](./openshift-container-platform-3x-prerequisites.md)
- [Bereitstellen von OpenShift Container Platform in Azure](./openshift-container-platform-3x.md)
- [Bereitstellen eines selbstverwalteten OpenShift Container Platform-Marketplace-Angebots](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Bereitstellen von OpenShift in Azure Stack](./openshift-azure-stack.md)
- [Aufgaben nach der Bereitstellung](./openshift-container-platform-3x-post-deployment.md)
- [Beheben von Problemen bei der Bereitstellung von OpenShift](./openshift-container-platform-3x-troubleshooting.md)
