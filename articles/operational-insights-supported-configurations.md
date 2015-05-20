<properties 
   pageTitle="Configurations prises en charge pour Operational Insights"
   description="En savoir plus sur les configurations requises pour Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" /> <tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/30/2015"
   ms.author="banders" />

# Configurations prises en charge pour Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../includes/operational-insights-note-moms.md)]

De quoi avez-vous besoin pour utiliser Operational Insights ? Consultez les informations suivantes pour vous préparer à Operational Insights.


## Configuration pour System Center 2012 Operations Manager

Vous pouvez utiliser Operational Insights comme service associé dans Operations Manager dans System Center 2012 R2 ou System Center 2012 R2 SP1. Cela vous permet d’utiliser la console Opérateur dans Operations Manager pour afficher les alertes et les informations de configuration d’Operational Insights. Lorsque vous utilisez Operational Insights comme service associé dans Operations Manager, l’agent communique directement avec le serveur d’administration, qui à son tour communique avec le service Operational Insights.

L’utilisation d’Operational Insights comme service associé nécessite de réunir les conditions préalables suivantes :


- L’intégration entre System Center 2012 SP1 Operations Manager et Operational Insights requiert des packs d’administration mis à jour, inclus dans le [connecteur Operational Insights pour Operations Manager](https://www.microsoft.com/fr-fr/download/details.aspx?id=38199). Vous pouvez télécharger et installer les packs d’administration depuis [Connecteur Operational Insights pour Operations Manager](https://www.microsoft.com/fr-fr/download/details.aspx?id=38199).

- System Center 2012 SP1 : correctif cumulatif 6 d’Operations Manager, bien que le correctif cumulatif 7 soit préférable. Cette mise à jour doit être appliquée au serveur d’administration, aux agents et à la console Opérateur pour Operational Insights en tant que service associé.

- System Center 2012 R2 : correctif cumulatif 2 d’Operations Manager, même si le correctif cumulatif 3 est préférable. Cette mise à jour doit être appliquée au serveur d’administration, aux agents et à la console Opérateur pour Operational Insights en tant que service associé.

- Pour afficher les données de gestion de capacité, vous devez activer la connectivité d'Operations Manager avec Virtual Machine Manager \(VMM\). Pour plus d’informations sur la connexion des systèmes, consultez [Connexion de VMM avec Operations Manager](https://technet.microsoft.com/fr-fr/library/hh882396.aspx).

Consultez [Affichage des alertes Operational Insights](http://go.microsoft.com/fwlink/?LinkID=293793) pour obtenir des instructions d’installation et de configuration.

Si vous souhaitez afficher les alertes Operational Insights sur SharePoint Server 2010, Lync Server 2013, Lync Server 2010 ou System Center 2012 SP1 - Virtual Machine Manager, vous devez configurer un compte d’identification pour ces charges de travail. Consultez les informations suivantes :


- [Définition du compte d’identification pour SharePoint](operational-insights-run-as.md)

- [Définition du compte d’identification pour Lync Server](operational-insights-run-as.md)

- [Définition du compte d’identification pour Virtual Machine Manager \(VMM\)](operational-insights-run-as.md)

### Systèmes d’exploitation Operations Manager

Les agents Operations Manager sont pris en charge sur divers ordinateurs. Consultez [Configuration requise : System Center 2012 R2](https://technet.microsoft.com/library/dn249696.aspx) pour plus d’informations sur la prise en charge de l’agent.

### Logiciels requis pour Operations Manager

Pour afficher les données de gestion de capacité, vous devez activer la connectivité d’Operations Manager avec VMM. Pour plus d’informations sur la connexion des systèmes, consultez [Connexion de VMM avec Operations Manager](https://technet.microsoft.com/fr-fr/library/hh882396.aspx).

## Agents directement connectés à Operational Insights

L’agent utilisé pour se connecter directement au service est l’agent Microsoft Monitoring Agent. Sa configuration requise est indiquée dans la page [Centre de téléchargement Microsoft ](https://www.microsoft.com/fr-fr/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True).

## Navigateurs

Les navigateurs suivants sont pris en charge pour Operational Insights :

- Windows Internet Explorer 11, Internet Explorer 10, Internet Explorer 9, Internet Explorer 8 ou Internet Explorer 7

- Mozilla Firefox 3.5 ou version ultérieure

Quel que soit le navigateur que vous utilisez, vous devez également installer Microsoft Silverlight 4.

## Technologies prises en charge pour l’analyse

L’évaluation de configuration Operational Insights analyse les charges de travail suivantes :

- Windows Server 2012 et Microsoft Hyper-V Server 2012

- Windows Server 2008 et Windows Server 2008 R2, y compris :
    - Active Directory
	- Hôte Hyper-V
	- Système d’exploitation général

- SQL Server 2012, SQL Server 2008 R2, SQL Server 2008
    - Moteur de base de données SQL Server

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 et Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 et Lync Server 2010

- System Center 2012 SP1 – Virtual Machine Manager

Pour SQL Server, les éditions 32 bits et 64 bits suivantes sont prises en charge pour l’analyse :

- SQL Server 2008 et 2008 R2 Enterprise

- SQL Server 2008 et 2008 R2 Standard

- SQL Server 2008 et 2008 R2 Workgroup

- SQL Server 2008 et 2008 R2 Web

- SQL Server 2008 et 2008 R2 Express

En outre, l’édition 32 bits de SQL Server est prise en charge lors de l’exécution dans l’implémentation WOW64.


<!--HONumber=54-->