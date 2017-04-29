---
title: "Passerelle de données locale | Microsoft Docs"
description: "Une passerelle locale est nécessaire si votre serveur Analysis Services dans Azure se connecte à des sources de données locales."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c7127f4bd89bf00a4ef72e54dc7694a1766e5762
ms.lasthandoff: 04/18/2017


---
# <a name="on-premises-data-gateway"></a>Passerelle de données locale
La passerelle de données locale agit comme un pont, en fournissant un transfert de données sécurisé entre des sources de données locales et votre serveur Azure Analysis Services dans le cloud.

Une passerelle est installée sur un ordinateur de votre réseau. Une passerelle doit être installée pour chaque serveur Azure Analysis Services inclus dans votre abonnement Azure. Par exemple, si vous disposez de deux serveurs dans votre abonnement Azure qui se connectent à des sources de données locales, une passerelle doit être installée sur deux ordinateurs distincts de votre réseau.

## <a name="requirements"></a>Configuration requise
**Configuration minimale requise :**

* .NET Framework 4.5
* Version 64 bits de Windows 7 / Windows Server 2008 R2 (ou version ultérieure)

**Recommandé :**

* Processeur 8 cœurs
* 8 Go de mémoire
* Version 64 bits de Windows 2012 R2 (ou version ultérieure)

**Points importants à prendre en compte :**

* La passerelle ne peut pas être installée sur un contrôleur de domaine.
* Une seule passerelle peut être installée sur un ordinateur.
* Installez la passerelle sur un ordinateur qui reste activé et qui ne se met pas en veille. Si l’ordinateur n’est pas actif, votre serveur Azure Analysis Services ne peut pas se connecter à vos sources de données locales pour actualiser les données.
* N’installez pas la passerelle sur un ordinateur sans fil connecté à votre réseau. Les performances peuvent être réduites.
* Pour modifier le nom du serveur pour une passerelle qui a déjà été configurée, vous devez réinstaller et configurer une nouvelle passerelle.
* Dans certains cas, les modèles tabulaires de connexion aux sources de données à l’aide de fournisseurs natifs tels que SQL Server Native Client (SQLNCLI11) peuvent renvoyer une erreur. Pour plus d’informations, consultez les rubriques suivantes : [Connexions de source de données](analysis-services-datasource.md).

## <a name="supported-on-premises-data-sources"></a>Sources de données locales prises en charge
Pour la version préliminaire, la passerelle prend en charge les connexions entre votre serveur Azure Analysis Services et les sources de données locales suivantes :

* SQL Server
* SQL Data Warehouse
* APS
* Oracle
* Teradata

## <a name="download"></a>Télécharger
 [Télécharger la passerelle](https://aka.ms/azureasgateway)

## <a name="install-and-configure"></a>Installer et configurer
1. Exécutez le programme d’installation.
2. Choisissez un emplacement d’installation et acceptez les termes du contrat de licence.
3. Connectez-vous à Azure.
4. Spécifiez le nom de votre serveur Azure Analysis Server. Vous ne pouvez spécifier qu’un seul serveur par passerelle. Cliquez sur **Configurer**. Vous pouvez continuer.

    ![se connecter à azure](./media/analysis-services-gateway/aas-gateway-configure-server.png)

## <a name="how-it-works"></a>Fonctionnement
La passerelle s’exécute comme un service Windows, **Passerelle de données locale**, sur un ordinateur dans le réseau de votre entreprise. La passerelle que vous installez pour une utilisation avec Azure Analysis Services est basée sur la même passerelle que celle utilisée pour d’autres services comme Power BI, mais avec quelques différences dans la manière selon laquelle elle est configurée.

![Fonctionnement](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Les requêtes et les flux de données fonctionnent comme suit :

1. Une requête est créée par le service cloud avec les informations d’identification chiffrées de la source de données locale. Elle est ensuite envoyée dans une file d’attente pour être traitée par la passerelle.
2. Le service cloud de la passerelle analyse la requête et envoie la requête vers [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. La passerelle de données locale interroge Azure Service Bus pour connaître les requêtes en attente.
4. La passerelle reçoit la requête, déchiffre les informations d’identification et se connecte aux sources de données avec ces informations d’identification.
5. La passerelle envoie la requête à la source de données pour exécution.
6. Les résultats sont renvoyés de la source de données vers la passerelle, puis vers le service cloud.

## <a name="windows-service-account"></a>Compte de service Windows
La passerelle de données locale est configurée afin d’utiliser *NT SERVICE\PBIEgwService* pour les informations d’identification d’ouverture de session du service Windows. Par défaut, elle dispose du droit d’ouverture de session en tant que service ; dans le contexte de l’ordinateur sur lequel vous installez la passerelle. Ces informations d’identification ne correspondent pas au même compte que celui utilisé pour se connecter aux sources de données locales ou à votre compte Azure.  

Si vous rencontrez des problèmes avec votre serveur proxy en raison de l’authentification, vous souhaiterez peut-être modifier le compte de service Windows sur un utilisateur de domaine ou un compte de service géré.

## <a name="ports"></a>Ports
La passerelle crée une connexion sortante vers Azure Service Bus. Elle communique sur les ports sortants : TCP 443 (par défaut), 5671, 5672 et 9350 à 9354.  La passerelle ne nécessite pas de ports entrants.

Il est recommandé d’autoriser les adresses IP pour votre région de données dans votre pare-feu. Vous pouvez télécharger la [liste d’adresses IP de centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Cette liste est actualisée chaque semaine.

> [!NOTE]
> Les adresses IP répertoriées dans la liste d’adresses IP de centre de données Azure sont en notation CIDR. Par exemple, 10.0.0.0/24 ne signifie pas 10.0.0.0 à 10.0.0.24. En savoir plus sur la [notation CIDR](http://whatismyipaddress.com/cidr).
>
>

Voici les noms de domaine complets utilisés par la passerelle.

| Noms de domaine | Ports sortants | Description |
| --- | --- | --- |
| *. powerbi.com |80 |HTTP utilisé pour télécharger le programme d’installation. |
| *. powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Récepteurs du Service Bus Relay sur TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Permet de tester la connectivité internet si la passerelle est inaccessible par le service Power BI. |
| *.microsoftonline-p.com |443 |Utilisé pour l’authentification en fonction de la configuration. |

### <a name="forcing-https-communication-with-azure-service-bus"></a>Forcer les communications HTTPS avec Azure Service Bus
Vous pouvez forcer la passerelle à communiquer avec Azure Service Bus à l’aide de HTTPS au lieu de TCP direct ; toutefois, cela peut affecter considérablement les performances. Vous devez modifier le fichier *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config*. Modifiez la valeur de `AutoDetect` à `Https`. Ce fichier se trouve par défaut dans *C:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Résolution des problèmes
En coulisses, la passerelle de données locale utilisée pour connecter Azure Analysis Services à vos sources de données locales est la même passerelle que celle utilisée avec Power BI.

Si vous rencontrez des difficultés lors de l’installation et la configuration d’une passerelle, veillez à consulter [Dépannage de la passerelle Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Si vous pensez que vous rencontrez un problème avec votre pare-feu, consultez les sections de pare-feu ou du proxy.

Si vous pensez que vous rencontrez des problèmes de proxy avec la passerelle, consultez [Configuration des paramètres de proxy pour les passerelles Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

### <a name="telemetry"></a>Télémétrie
La télémétrie peut être utilisée pour la surveillance et la résolution des problèmes. 

**Pour activer la télémétrie**

1.    Vérifiez le répertoire client de la passerelle de données sur site sur l’ordinateur. En règle générale, il s’agit de la passerelle de données %systemdrive%\Program Files\On-premises. Vous pouvez également ouvrir une console de services et vérifiez le chemin d’accès au fichier exécutable : une propriété du service de passerelle de données sur site.
2.    Dans le fichier Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config du répertoire client. Modifiez le paramètre SendTelemetry sur true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.    Enregistrez vos modifications et redémarrez le service Windows : service de passerelle de données sur site.




## <a name="next-steps"></a>Étapes suivantes
* [Gérer Analysis Services](analysis-services-manage.md)
* [Obtenir les données d’Azure Analysis Services](analysis-services-connect.md)

