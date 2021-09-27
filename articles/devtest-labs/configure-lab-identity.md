---
title: Konfigurieren einer Lab-Identität
description: Erfahren Sie, wie Sie eine Lab-Identität in Azure DevTest konfigurieren.
ms.topic: how-to
ms.date: 08/20/2020
ms.openlocfilehash: 3cc14d87cfea331d8a11e09071005c23be942405
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638727"
---
# <a name="configure-a-lab-identity"></a>Konfigurieren einer Lab-Identität

Eine gängige Herausforderung beim Erstellen von Cloudanwendungen ist die Verwaltung der Anmeldeinformationen im Code, die für die Authentifizierung bei Clouddiensten erforderlich sind. Der Schutz dieser Anmeldeinformationen ist eine wichtige Aufgabe. Im Idealfall werden die Anmeldeinformationen nie auf Entwicklerarbeitsstationen angezeigt und auch nicht in die Quellcodeverwaltung eingecheckt. Azure Key Vault bietet eine Möglichkeit zum sicheren Speichern von Anmeldeinformationen, Geheimnissen und anderen Schlüsseln. Um diese abrufen zu können, muss sich Ihr Code jedoch bei Key Vault authentifizieren. 

Die Funktion für verwaltete Identitäten für Azure-Ressourcen in Azure Active Directory (Azure AD) löst dieses Problem. Dieses Feature stellt für Azure-Dienste eine automatisch verwaltete Identität in Azure AD bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Weitere Informationen zu [verwalteten Identitäten in Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Es gibt zwei Arten von verwalteten Identitäten: 

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität  

Eine  **vom System zugewiesene verwaltete Identität**  wird direkt für eine Azure-Dienstinstanz aktiviert. Wenn die Identität aktiviert ist, erstellt Azure eine Identität für die Instanz in dem Azure AD-Mandanten, der vom Abonnement der Instanz als vertrauenswürdig eingestuft wird. Nach dem Erstellen der Identität werden die Anmeldeinformationen in der Instanz bereitgestellt. Der Lebenszyklus einer vom System zugewiesenen Identität ist direkt an die Azure-Dienstinstanz gebunden, für die sie aktiviert wurde. Wenn die Instanz gelöscht wird, bereinigt Azure automatisch die Anmeldeinformationen und die Identität in Azure AD. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>Szenarien für die Verwendung der vom System zugewiesenen Identität des Labs  

Alle DevTest-Labs werden mit einer vom System zugewiesenen Identität erstellt, die für die Lebensdauer des Labs gültig bleibt. Die vom System zugewiesene Identität wird für folgende Zwecke verwendet:  

- Alle auf [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) basierenden Bereitstellungen, die zum Einrichten von Multi-VM- und/oder Paas-Umgebungen (Platform-as-a-Service) verwendet werden, werden mithilfe der vom System zugewiesenen Identität des Labs ausgeführt.  
- Datenträgerverschlüsselung für Lab-Datenträger mit einem kundenseitig verwalteten Schlüssel wird durch die vom System zugewiesene Identität des Labs unterstützt. Durch Bereitstellen von explizitem Zugriff auf die Identität des Labs, um auf Ihren Datenträgerverschlüsselungssatz zuzugreifen, kann das Lab alle VM-Datenträger in Ihrem Namen verschlüsseln. Erfahren Sie mehr über die [Aktivierung von Datenträgerverschlüsselung](encrypt-disks-customer-managed-keys.md) für Ihre Lab-Datenträger mit einem kundenseitig verwalteten Schlüssel.  

### <a name="configure-identity"></a>Konfigurieren der Identität

In diesem Abschnitt wird veranschaulicht, wie Sie die Identitätsrichtlinie des Labs konfigurieren.

> [!NOTE]
> Bei Labs, die vor dem 10.08.2020 erstellt wurden, wird die vom System zugewiesene Identität auf „Aus“ festgelegt. Als Lab-Besitzer können Sie sie aktivieren, wenn Sie Labs für die im vorherigen Abschnitt aufgeführten Zwecke verwenden möchten.  
>
> Für neue Labs, die nach dem 10.08.2020 erstellt wurden, ist die vom System zugewiesene Identität des Labs standardmäßig auf „Ein“ festgelegt, und der Lab-Besitzer kann diese Einstellung während des Lebenszyklus des Labs nicht deaktivieren.  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **DevTest-Labs**.
1. Wählen Sie in der Liste der Labs das gewünschte Lab aus.
1. Wählen Sie **Konfiguration und Richtlinien** -> **Identität (Vorschau)** aus. 

> [!div class="mx-imgBorder"]
> ![Konfigurieren der Identität](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität  

Eine vom Benutzer zugewiesene verwaltete Identität wird als eigenständige Azure-Ressource erstellt. Azure erstellt eine Identität in dem Azure AD-Mandanten, der vom verwendeten Abonnement als vertrauenswürdig eingestuft wird. Nachdem die Identität erstellt wurde, kann sie einer oder mehreren Azure-Dienstinstanzen zugewiesen werden. Der Lebenszyklus einer vom Benutzer zugewiesenen Identität wird getrennt vom Lebenszyklus der Azure-Dienstinstanzen verwaltet, denen sie zugewiesen ist. 

DevTest Labs unterstützt vom Benutzer zugewiesene Identitäten für virtuelle Computer und für auf Azure Resource Manager basierende Umgebungen.  Weitere Informationen finden Sie unter den folgenden Themen:

- [Hinzufügen einer vom Benutzer zugewiesenen Identität zum Bereitstellen von Azure Resource Manager-Lab-Umgebungen](use-managed-identities-environments.md)
- [Hinzufügen von vom Benutzer zugewiesenen Identitäten zum Bereitstellen von virtuellen Lab-Computern](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [Konfigurieren des Kostenmanagements](devtest-lab-configure-cost-management.md).