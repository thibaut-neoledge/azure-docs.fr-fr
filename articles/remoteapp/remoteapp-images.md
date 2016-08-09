<properties
    pageTitle="Que contiennent les images de modèle Azure RemoteApp ? | Microsoft Azure"
    description="Découvrez les images de modèle incluses dans Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/02/2016"
    ms.author="elizapo" />

# Que contiennent les images de modèle Azure RemoteApp ?

Votre abonnement Azure RemoteApp comprend trois images de modèle :


- Windows Server 2012
- Microsoft Office 365 ProPlus (abonnement Office 365 requis)
- Microsoft Office Professionnel Plus 2013 (version d'évaluation uniquement)

> [AZURE.IMPORTANT]Votre abonnement à Azure RemoteApp vous permet d'accéder au logiciel dans les images, à l'exception d'Office 365 ProPlus, qui requiert un abonnement distinct et Office 2013, qui ne peut pas être utilisé en production. Cela signifie que vous pouvez partager les programmes ou les applications sur les images de modèle avec vos utilisateurs. Par exemple, si vous créez une collection qui utilise l'image de Windows Server 2012 R2, vous pouvez publier System Center Endpoint Protection pour que vos utilisateurs y accèdent avec RemoteApp.
>
> Consultez les [Informations sur les licences RemoteApp](remoteapp-licensing.md) pour plus d'informations. Et [Utilisation d'Office avec Azure RemoteApp](remoteapp-o365.md) pour les informations de licence concernant Office.

Continuez votre lecture pour en savoir plus sur ce que contient chaque image.

## Windows Server 2012 R2 (« l'image vanille »)
Cette image est basée sur le système d'exploitation Microsoft Windows Server 2012 R2 Datacenter et inclut les rôles et fonctionnalités suivants conformément à la configuration requise pour les images de modèle Azure RemoteApp :


- .NET Framework 4.5, 3.5.1, 3.5
- Expérience utilisateur
- Services de prise en charge de l'écriture manuscrite
- Media Foundation
- Hôte de session Bureau à distance
- Windows PowerShell 4.0
- Windows PowerShell ISE
- Prise en charge WoW64

Cette image inclut également les applications suivantes :

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (abonnement requis)
Office 365 étant l'application la plus demandée, nous avons créé une image « personnalisée » que vous pouvez utiliser.

Cette image est une extension de l'image vanille et inclut les composants suivants de Microsoft Office 365 ProPlus en plus des composants décrits dans l'image Windows Server 2012 R2 :


- Access
- Excel
- Lync
- OneNote
- OneDrive Entreprise (notez que l’agent de synchronisation n’est pas pris en charge avec Azure RemoteApp)
- Outlook
- PowerPoint
- Word
- Outils de vérification linguistique Microsoft Office

L'image inclut également Visio Pro et Project Pro.

Et les applications suivantes, également :

- SQL Native Client
- Pilote ODBC
- Client d'exploration de données SQL Server
- Client MasterDataServices
- Microsoft Publisher
- PowerQuery
- PowerMap


Toutes les fonctionnalités des applications Office 365 ProPlus sont disponibles uniquement pour les utilisateurs qui disposent d'une offre Office 365 ProPlus. Pour plus d'informations sur les offres d'abonnement Office 365, consultez [Plans de service Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Des questions supplémentaires ? Consultez les informations sur [Office 365 + RemoteApp](remoteapp-o365.md). Consultez également le nouvel article, [Utilisation de votre abonnement Office 365 avec Azure RemoteApp](remoteapp-officesubscription.md).

Notez que vous devez obtenir une licence Office 365 ProPlus, Visio Pro et Project Pro séparément : ils ont chacun leur propre licence.

## Microsoft Office Professionnel Plus 2013 (version d'évaluation uniquement)
Pendant la durée de la version d'évaluation gratuite, vous pouvez tester le service avec l'image Office 2013.

Cette image est une extension de l'image vanille et inclut les composants suivants de Microsoft Office Professionnel Plus 2013 en plus des composants décrits dans l'image Windows Server 2012 R2 :


- Access
- Excel
- Lync
- OneNote
- OneDrive Entreprise (notez que l’agent de synchronisation n’est pas pris en charge avec Azure RemoteApp)
- Outlook
- PowerPoint
- projet
- Visio
- Word
- Outils de vérification linguistique Microsoft Office

> [AZURE.IMPORTANT]**Informations légales :** cette image n’inclut pas de licence Microsoft Office et *ne peut pas être utilisée en production*. L'image Office 2013 Professionnel Plus peut uniquement être utilisée à des fins d'évaluation. Si vous voulez utiliser des applications Office dans Azure RemoteApp pour la production, vous devez utiliser l'image Office 365 ProPlus. Pour plus d'informations sur la licence Office, consultez [Utilisation d'Office 365 avec Azure RemoteApp](remoteapp-o365.md)

<!---HONumber=AcomDC_0803_2016-->