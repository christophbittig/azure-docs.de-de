---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 0198bcdd34cd92822c779329affeb1a405264bae
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830514"
---
## <a name="configure-your-local-environment"></a>Konfigurieren Ihrer lokalen Umgebung

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

::: zone pivot="programming-language-csharp"
[!INCLUDE [functions-cli-dotnet-prerequisites](functions-cli-dotnet-prerequisites.md)]
::: zone-end  
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other" 
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2).

+ [Azure CLI](/cli/azure/install-azure-cli), Version 2.4 oder höher. 
:::zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS- und Maintenance LTS-Versionen (8.11.1 und 10.14.1 empfohlen)
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 Bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 Bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 Bit)](https://www.python.org/downloads/release/python-368/). Diese Versionen werden von Azure Functions unterstützt. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ Das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), Version 8 oder 11. 

+ [Apache Maven](https://maven.apache.org), Version 3.0 oder höher

::: zone-end
::: zone pivot="programming-language-other"
+ Entwicklungstools für die von Ihnen verwendete Sprache. In diesem Tutorial wird als Beispiel die [Programmiersprache R](https://www.r-project.org/) verwendet.
::: zone-end

Sie benötigen außerdem ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).