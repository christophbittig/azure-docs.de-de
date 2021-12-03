---
title: Konfigurieren der externen Identitätsquelle für vCenter
description: Erfahren Sie, wie Sie Active Directory über LDAP oder LDAPS für vCenter als externe Identitätsquelle konfigurieren.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 37478b65572fba2815121b0712d1a0f2e43376dc
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506294"
---
# <a name="configure-external-identity-source-for-vcenter"></a>Konfigurieren der externen Identitätsquelle für vCenter



[!INCLUDE [vcenter-access-identity-description](includes/vcenter-access-identity-description.md)]

>[!NOTE]
>Skriptausführungen erfolgen nacheinander in der übermittelten Reihenfolge.

In dieser Schrittanleitung erfahren Sie Folgendes:

> [!div class="checklist"]
> * Auflisten aller vorhandenen externen Identitätsquellen, die in vCenter SSO integriert sind
> * Hinzufügen von Active Directory über LDAP, mit oder ohne SSL 
> * Hinzufügen einer vorhandenen AD-Gruppe zur CloudAdmin-Gruppe
> * Entfernen der AD-Gruppe aus der CloudAdmin-Rolle
> * Entfernen vorhandener externer Identitätsquellen



## <a name="prerequisites"></a>Voraussetzungen

- Richten Sie Verbindungen zwischen Ihrem lokalen Netzwerk und Ihrer privaten Cloud ein.

- Wenn Sie über AD mit SSL verfügen, laden Sie das Zertifikat für die AD-Authentifizierung herunter, und laden Sie es als Blobspeicher in ein Azure Storage-Konto hoch. Anschließend müssen Sie den [Zugriff auf Azure Storage-Ressourcen mithilfe einer Shared Access Signature (SAS) gewähren](../storage/common/storage-sas-overview.md).  

- Wenn Sie den FQDN verwenden, aktivieren Sie die DNS-Auflösung in Ihrem lokalen AD.

 

## <a name="list-external-identity"></a>Auflisten der externen Identität



Sie führen das Cmdlet `Get-ExternalIdentitySources` aus, um alle externen Identitätsquellen aufzulisten, die bereits mit vCenter SSO integriert sind.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Skriptausführung** > **Pakete** > **Get-ExternalIdentitySources** aus.

   :::image type="content" source="media/run-command/run-command-overview.png" alt-text="Screenshot: Zugriff auf die verfügbaren Skriptausführungen" lightbox="media/run-command/run-command-overview.png":::

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   :::image type="content" source="media/run-command/run-command-get-external-identity-sources.png" alt-text="Screenshot: Auflisten der externen Identitätsquelle":::
   
   | **Feld** | **Wert** |
   | --- | --- |
   | **Beibehalten bis**  |Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert ist 60 Tage.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **getExternalIdentity**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie die **Benachrichtigungen** oder den Bereich **Run Execution Status** (Status der Ausführung), um den Fortschritt anzuzeigen.


## <a name="add-active-directory-over-ldap-with-ssl"></a>Hinzufügen von Active Directory über LDAP mit SSL

Sie führen das Cmdlet `New-AvsLDAPSIdentitySource` aus, um AD über LDAP mit SSL als externe Identitätsquelle für die Verwendung mit SSO in vCenter hinzuzufügen. 

1. Laden Sie das Zertifikat für die AD-Authentifizierung herunter, und laden Sie es als Blobspeicher in ein Azure Storage-Konto hoch. Wenn mehrere Zertifikate erforderlich sind, laden Sie jedes Zertifikat einzeln hoch.  

1. [Gewähren Sie für jedes Zertifikat den Zugriff auf Azure Storage-Ressourcen mit einer Shared Access Signature (SAS)](../storage/common/storage-sas-overview.md). Diese SAS-Zeichenfolgen werden dem Cmdlet als Parameter bereitgestellt. 

   >[!IMPORTANT]
   >Stellen Sie sicher, dass Sie jede SAS-Zeichenfolge kopieren, da sie nicht mehr verfügbar sind, sobald Sie diese Seite verlassen.  
   
1. Wählen Sie **Skriptausführung** > **Pakete** > **New-AvsLDAPSIdentitySource** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **Name**  | Benutzerfreundlicher Name der externen Identitätsquelle, z. B. **avslap.local**.  |
   | **DomainName**  | Der FQDN der Domäne.   |
   | **DomainAlias**  | Für Active Directory-Identitätsquellen ist dies der NetBIOS-Name der Domäne. Fügen Sie den NetBIOS-Namen der AD-Domäne als Alias der Identitätsquelle hinzu, wenn Sie die SSPI-Authentifizierung verwenden.     |
   | **PrimaryUrl**  | Primäre URL der externen Identitätsquelle, z. B. **ldap://yourserver:636**.  |
   | **SecondaryURL**  | Sekundäre Fallback-URL, falls bei der primären URL ein Fehler auftritt.  |
   | **BaseDNUsers**  |  Für die Suche nach gültigen Benutzern, z. B. **CN=users,DC=Yourserver,DC=internal**.  Der Basis-DN ist für die Verwendung der LDAP-Authentifizierung erforderlich.  |
   | **BaseDNGroups**  | Für die Suche nach Gruppen, z. B. **CN=group1, DC=yourserver,DC= internal**. Der Basis-DN ist für die Verwendung der LDAP-Authentifizierung erforderlich.  |
   | **Credential**  | Benutzername und Kennwort für die Authentifizierung bei der AD-Quelle (nicht CloudAdmin).  |
   | **CertificateSAS** | Pfad zu SAS-Zeichenfolgen mit den Zertifikaten für die Authentifizierung bei der AD-Quelle. Wenn Sie mehrere Zertifikate verwenden, trennen Sie die einzelnen SAS-Zeichenfolgen durch ein Komma. Beispiel: **pathtocert1,pathtocert2**.  |
   | **GroupName**  | Gruppe in der externen Identitätsquelle, die dem Cloudadministrator Zugriff gewährt. Beispiel: **avs-admins**.  |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert ist 60 Tage.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **addexternalIdentity**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie die **Benachrichtigungen** oder den Bereich **Run Execution Status** (Status der Ausführung), um den Fortschritt anzuzeigen.



## <a name="add-active-directory-over-ldap"></a>Hinzufügen von Active Directory über LDAP

>[!NOTE]
>Diese Methode wird nicht empfohlen. Verwenden Sie stattdessen die Methode [Active Directory über LDAP mit SSL hinzufügen](#add-active-directory-over-ldap-with-ssl).

Sie führen das Cmdlet `New-AvsLDAPIdentitySource` aus, um AD über LDAP als externe Identitätsquelle für die Verwendung mit SSO in vCenter hinzuzufügen. 

1. Wählen Sie **Skriptausführung** > **Pakete** > **New-AvsLDAPIdentitySource** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.
   
   | **Feld** | **Wert** |
   | --- | --- |
   | **Name**  | Benutzerfreundlicher Name der externen Identitätsquelle, z. B. **avslap.local**.  |
   | **DomainName**  | Der FQDN der Domäne.    |
   | **DomainAlias**  | Für Active Directory-Identitätsquellen ist dies der NetBIOS-Name der Domäne. Fügen Sie den NetBIOS-Namen der AD-Domäne als Alias der Identitätsquelle hinzu, wenn Sie die SSPI-Authentifizierung verwenden.      |
   | **PrimaryUrl**  | Primäre URL der externen Identitätsquelle, z. B. **ldap://yourserver:389**.  |
   | **SecondaryURL**  | Sekundäre Fallback-URL, falls bei der primären URL ein Fehler auftritt.  |
   | **BaseDNUsers**  |  Für die Suche nach gültigen Benutzern, z. B. **CN=users,DC=Yourserver,DC=internal**.  Der Basis-DN ist für die Verwendung der LDAP-Authentifizierung erforderlich.  |
   | **BaseDNGroups**  | Für die Suche nach Gruppen, z. B. **CN=group1, DC=yourserver,DC= internal**. Der Basis-DN ist für die Verwendung der LDAP-Authentifizierung erforderlich.  |
   | **Credential**  | Benutzername und Kennwort für die Authentifizierung bei der AD-Quelle (nicht CloudAdmin).  |
   | **GroupName**  | Gruppe, um Cloudadministratorzugriff in Ihrer externen Identitätsquelle zu erhalten, z. B. **avs-admins**.  |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert ist 60 Tage.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **addexternalIdentity**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie die **Benachrichtigungen** oder den Bereich **Run Execution Status** (Status der Ausführung), um den Fortschritt anzuzeigen.


## <a name="add-existing-ad-group-to-cloudadmin-group"></a>Hinzufügen einer vorhandenen AD-Gruppe zur CloudAdmin-Gruppe

Sie führen das Cmdlet `Add-GroupToCloudAdmins` aus, um der CloudAdmin-Gruppe eine vorhandene AD-Gruppe hinzuzufügen. Die Benutzer in dieser Gruppe haben dieselben Berechtigungen wie die in vCenter SSO definierte CloudAdmin-Rolle (cloudadmin@vsphere.local).

1. Wählen Sie **Skriptausführung** > **Pakete** > **Add-GroupToCloudAdmins** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **GroupName**  | Name der hinzuzufügenden Gruppe, z. B. **VcAdminGroup**.  |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert ist 60 Tage.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **addADgroup**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie die **Benachrichtigungen** oder den Bereich **Run Execution Status** (Status der Ausführung), um den Fortschritt anzuzeigen.



## <a name="remove-ad-group-from-the-cloudadmin-role"></a>Entfernen der AD-Gruppe aus der CloudAdmin-Rolle

Sie führen das Cmdlet `Remove-GroupFromCloudAdmins` aus, um eine angegebene AD-Gruppe aus der CloudAdmin-Rolle zu entfernen. 

1. Wählen Sie **Skriptausführung** > **Pakete** > **Remove-GroupFromCloudAdmins** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **GroupName**  | Name der zu entfernenden Gruppe, z. B. **VcAdminGroup**.  |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert ist 60 Tage.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **removeADgroup**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie die **Benachrichtigungen** oder den Bereich **Run Execution Status** (Status der Ausführung), um den Fortschritt anzuzeigen.






## <a name="remove-existing-external-identity-sources"></a>Entfernen vorhandener externer Identitätsquellen

Führen Sie das Cmdlet `Remove-ExternalIdentitySources` aus, um alle vorhandenen externen Identitätsquellen im Massenvorgang zu entfernen. 

1. Wählen Sie **Skriptausführung** > **Pakete** > **Remove-ExternalIdentitySources** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert ist 60 Tage.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **remove_externalIdentity**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie die **Benachrichtigungen** oder den Bereich **Run Execution Status** (Status der Ausführung), um den Fortschritt anzuzeigen.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie LDAP und LDAPS konfiguriert werden, können Sie mehr über Folgendes erfahren:

- [Konfigurieren der Speicherrichtlinie](configure-storage-policy.md) – Jeder in einem vSAN-Datenspeicher bereitgestellten VM wird mindestens eine VM-Speicherrichtlinie zugewiesen. Sie können eine VM-Speicherrichtlinie zuweisen, wenn Sie eine VM erstmals bereitstellen oder wenn Sie andere VM-Vorgänge ausführen (z. B. Klonen oder Migrieren).

- [Azure VMware Solution-Identitätskonzepte](concepts-identity.md) – Verwenden Sie vCenter, um VM-Workloads und NSX-T Manager zur Verwaltung und Erweiterung der privaten Cloud zu verwalten. Zugriffs- und Identitätsverwaltung übernehmen die Rolle CloudAdmin für vCenter und eingeschränkte Administratorrechte für NSX-T Manager. 

 
