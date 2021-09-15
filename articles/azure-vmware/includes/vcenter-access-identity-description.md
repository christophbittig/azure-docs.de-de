---
title: Beschreibung von vCenter-Zugriff und -Identität
description: vCenter verfügt über einen integrierten lokalen Benutzer namens „cloudadmin“, der der Rolle „CloudAdmin“ zugewiesen ist.
ms.topic: include
ms.date: 08/31/2021
ms.openlocfilehash: 1fdc81c11b833b1239d37f613732eec42b463e1a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252548"
---
<!-- used in concepts-run-commands.md and tutorial-configure-identity-source.md -->

In Azure VMware Solution verfügt vCenter über einen integrierten lokalen Benutzer namens *cloudadmin*, der der Rolle „CloudAdmin“ zugewiesen ist. Sie können Benutzer und Gruppen in Active Directory (AD) mit der Rolle „CloudAdmin“ für Ihre private Cloud konfigurieren. Im Allgemeinen erstellt und verwaltet die Rolle CloudAdmin Workloads in Ihrer privaten Cloud. In Azure VMware Solution verfügt die Rolle „CloudAdmin“ jedoch über vCenter-Berechtigungen, die sich von anderen VMware-Cloudlösungen und lokalen Bereitstellungen unterscheiden.

>[!IMPORTANT]
>Der lokale Benutzer „cloudadmin“ sollte als Notfallzugriffskonto für Notfallszenarien in Ihrer privaten Cloud behandelt werden. Er ist nicht für alltägliche Verwaltungsaktivitäten oder die Integration in andere Dienste vorgesehen. 

- Bei einer lokalen Bereitstellung von vCenter und ESXi hat der Administrator Zugriff auf das vCenter-Konto „administrator\@vsphere.local“. Er kann auch weitere AD-Benutzer und -Gruppen zuweisen lassen. 

- Bei einer Azure VMware Solution-Bereitstellung hat der Administrator keinen Zugriff auf das Benutzerkonto des Administrators. Er kann der Rolle „CloudAdmin“ in vCenter jedoch AD-Benutzer und -Gruppen zuweisen.  Die Rolle „CloudAdmin“ verfügt nicht über die Berechtigungen zum Hinzufügen einer Identitätsquelle wie einem lokalen LDAP- oder LDAPS-Server zu vCenter. Sie können jedoch Ausführungsbefehle verwenden, um eine Identitätsquelle hinzuzufügen und Benutzern und Gruppen die Rolle „CloudAdmin“ zuzuweisen.
 
Der Benutzer der privaten Cloud hat keinen Zugriff auf bestimmte Verwaltungskomponenten, die von Microsoft unterstützt und verwaltet werden, und kann diese auch nicht konfigurieren. Dazu gehören z. B. Cluster, Hosts, Datenspeicher und verteilte virtuelle Switches.

>[!NOTE]
>In Azure VMware Solution wird die SSO-Domäne *vsphere.local* als verwaltete Ressource bereitgestellt, um Plattformvorgänge zu unterstützen. Sie unterstützt nicht die Erstellung und Verwaltung lokaler Gruppen und Benutzer, die nicht standardmäßig in Ihrer privaten Cloud bereitgestellt werden.
