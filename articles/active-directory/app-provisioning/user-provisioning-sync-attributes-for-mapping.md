---
title: Synchronisieren von Attributen mit Azure AD zu Zuordnungszwecken
description: Verwenden Sie beim Konfigurieren der Benutzerbereitstellungen in Azure AD und SaaS-Apps (Software-as-a-Service) die Verzeichniserweiterungsfunktion, um Quellattribute hinzuzufügen, die nicht standardmäßig synchronisiert sind.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/24/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 207936bd4b8fe29cfa15cd284b8ebba1e6a12164
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057531"
---
# <a name="syncing-extension-attributes-for-azure-active-directory-application-provisioning"></a>Synchronisieren von Erweiterungsattributen für die Azure Active Directory-Anwendungsbereitstellung

Azure Active Directory (Azure AD) muss bei der Bereitstellung eines Benutzerkontos aus Azure AD in eine SaaS-App alle erforderlichen Daten (Attribute) zum[ Erstellen eines Benutzerprofils enthalten](../saas-apps/tutorial-list.md). Beim Anpassen von Attributzuordnungen für die Benutzerbereitstellung wird das Attribut, das Sie zuordnen möchten, möglicherweise nicht in der **Quellattribut**-Liste angezeigt. In diesem Artikel wird gezeigt, wie Sie das fehlende Attribut hinzufügen.

Nur für Benutzer: In Azure AD können Sie [mithilfe von PowerShell oder Microsoft Graph Erweiterungen erstellen](#create-an-extension-attribute-on-a-cloud-only-user).

Für Benutzer im Active Directory vor Ort: Sie müssen die Benutzer mit Azure AD synchronisieren. Sie können Benutzer und Attribute mithilfe von [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)synchronisieren. Azure AD Connect synchronisiert zwar automatisch bestimmte Attribute mit Azure AD, allerdings nicht alle. Darüber hinaus werden einige standardmäßig synchronisierte Attribute (z. B. SAMAccountName) nicht mit Azure AD Graph-API verfügbar gemacht. In diesen Fällen können Sie die Verzeichniserweiterungsfunktion von Azure AD Connect [verwenden, um das Attribut mit Azure AD zu synchronisieren](#create-an-extension-attribute-using-azure-ad-connect). Auf diese Weise wird das Attribut über die Azure AD Graph-API und den Azure AD-Bereitstellungsdienst angezeigt.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Erstellen eines Erweiterungsattributs von einem reinen Cloud-Benutzer
Sie können Microsoft Graph und PowerShell verwenden, um das Benutzerschema für Benutzer in Azure AD zu erweitern. Diese Erweiterungsattribute werden in den meisten Fällen automatisch erkannt.

Wenn Sie über mehr als 1000 Dienstprinzipale verfügen, stellen Sie möglicherweise fest, dass in der Liste Quellattribut Erweiterungen fehlen. Wenn ein von Ihnen erstelltes Attribut nicht automatisch angezeigt wird, vergewissern Sie sich, dass das Attribut erstellt wurde, und fügen Sie es dem Schema manuell hinzu. Bestätigen Sie mit Microsoft Graph und [Graph-Explorer](/graph/graph-explorer/graph-explorer-overview), dass es erstellt wurde. Informationen zum manuellen Hinzufügen des Attributs zu Ihrem Schema finden Sie unter [Bearbeiten der Liste unterstützter Attribute](customize-application-attributes.md#editing-the-list-of-supported-attributes).

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Erstellen eines Erweiterungsattributs von einem reinen Cloud-Benutzer mithilfe von Microsoft Graph
Sie können das Schema der Azure AD-Benutzer mithilfe von [Microsoft Graph](/graph/overview)erweitern. 

Listen Sie zuerst die Apps in Ihrem Mandanten auf, um die ID der APP zu erhalten, an der Sie arbeiten. Weitere Informationen finden Sie unter [Auflisten von extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Erstellen Sie als nächstes das Erweiterungsattribut. Ersetzen Sie die **ID**-Eigenschaft unten durch die **ID**, die Sie im vorherigen Schritt abgerufen haben. Sie müssen das Attribut **„ID“** und nicht die „AppID“ verwenden. Weitere Informationen finden Sie unter [Create extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

Die vorherige Anforderung hat ein Erweiterungsattribut mit dem Format erstellt `extension_appID_extensionName` . Sie können jetzt einen Benutzer mit diesem Erweiterungsattribut aktualisieren. Weitere Informationen finden Sie unter [Aktualisieren des Benutzers](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Überprüfen Sie abschließend das Attribut für den Benutzer. Weitere Informationen finden Sie unter [Benutzer erhalten](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Erstellen eines Erweiterungsattributs von einem reinen Cloud-Benutzer mithilfe von PowerShell
Erstellen Sie eine benutzerdefinierte Erweiterung mit PowerShell, und weisen Sie einem Benutzer einen Wert zu. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Erstellen von Erweiterungsattributen mit Azure AD Connect

1. Öffnen Sie den Azure AD Connect-Assistenten, wählen Sie „Tasks“ aus, und klicken Sie auf **Synchronisierungsoptionen anpassen**.

   ![Seite „Weitere Aufgaben“ des Azure Active Directory Connect-Assistenten](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Anmelden als globaler Azure AD-Administrator 

3. Wählen Sie auf der Seite **Optionale Features** **Verzeichniserweiterungen-Attributsynchronisierung** aus.
 
   ![Seite „Optionale Features“ des Azure Active Directory Connect-Assistenten](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Wählen Sie das Attribut bzw. die Attribute aus, die Sie mit Azure AD erweitern möchten.
   > [!NOTE]
   > Bei der Suche im Feld **Verfügbare Attribute** wird die Groß-/Kleinschreibung berücksichtigt.

   ![Screenshot der Auswahlseite „Verzeichniserweiterungen“](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Beenden Sie den Azure AD Connect-Assistenten, und führen Sie einen vollständigen Synchronisierungszyklus aus. Nach Abschluss des Synchronisierungszyklus wird das Schema erweitert, und die neuen Werte werden zwischen Ihrem lokalen AD und Azure AD synchronisiert.
 
6. Während Sie im Azure-Portal [Benutzerattributzuordnungen bearbeiten](customize-application-attributes.md), enthält die Liste der **Quellattribute** jetzt die hinzugefügten Attribute im `<attributename> (extension_<appID>_<attributename>)`-Format. Wählen Sie das Attribut aus, und ordnen Sie es der Zielanwendung für die Bereitstellung zu.

   ![Seite „Directory extensions selection“ (Verzeichniserweiterungsauswahl) des Azure Active Directory Connect-Assistenten](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Die Möglichkeit des Bereitstellens von Verweisattributen aus lokalen AD-Instanzen (z. B. **managedBy** oder **DN/DistinguishedName**) wird derzeit nicht unterstützt. Sie können dieses Feature über [User Voice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) anfordern. 


## <a name="next-steps"></a>Nächste Schritte

* [Attribute-based application provisioning with scoping filters (Definieren des Geltungsbereichs für die Bereitstellung)](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
