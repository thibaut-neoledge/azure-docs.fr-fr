<properties title="Batterie de serveurs SharePoint" pageTitle="Batterie de serveurs SharePoint" description="D&eacute;crit la nouvelle fonctionnalit&eacute; Batterie de serveurs&nbsp;SharePoint de la version pr&eacute;liminaire du portail&nbsp;Azure" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="josephd" />

# Batterie de serveurs SharePoint

Avec la batterie de serveurs SharePoint, la version préliminaire du portail Azure crée automatiquement une batterie SharePoint Server 2013 préconfigurée. Cela peut vous faire gagner un temps précieux lorsque vous avez besoin d'une batterie de serveurs SharePoint de base ou à haute disponibilité pour un environnement de test et de développement ou si vous envisagez l'adoption de SharePoint Server 2013 comme solution de collaboration pour votre entreprise.

La batterie de serveurs SharePoint de base se compose de trois machines virtuelles dans la configuration suivante :

![sharepointfarm][sharepointfarm]

Vous pouvez utiliser cette configuration de batterie comme configuration simplifiée pour le développement d'applications SharePoint ou pour votre évaluation initiale de SharePoint 2013.

La batterie de serveurs SharePoint à haute disponibilité se compose de neuf machines virtuelles dans la configuration suivante :

![sharepointfarm][1]

Vous pouvez utiliser cette configuration de batterie pour tester des charges de clients plus élevées, la haute disponibilité du site SharePoint externe et SQL Server AlwaysOn pour une batterie SharePoint. Vous pouvez également l'utiliser pour le développement d'applications SharePoint dans un environnement à haute disponibilité.

Pour obtenir des informations de configuration détaillées sur ces deux types de batteries de serveurs, consultez la rubrique [Détails de configuration de la batterie de serveurs SharePoint][Détails de configuration de la batterie de serveurs SharePoint].

Ces batteries disposent d'un point de terminaison préconfiguré afin d'autoriser le trafic web authentifié (port TCP 80) vers le serveur web SharePoint par le biais d'un ordinateur client connecté à Internet. Ce point de terminaison pointe vers un site d'équipe préconfiguré. Pour accéder au site de l'équipe, procédez comme suit :

1.  Dans la version préliminaire du portail Azure, cliquez sur **Parcourir**, puis sur **Groupes de ressources**.
2.  Dans la liste des groupes de ressources, cliquez sur le nom du groupe de ressources de votre batterie de serveurs SharePoint.
3.  Dans le volet du groupe de ressources de votre batterie de serveurs SharePoint, cliquez sur **Historique du déploiement**.
4.  Dans le volet **Historique du déploiement**, cliquez sur **Microsoft.SharePointFarm**.
5.  Dans le volet **Microsoft.SharePointFarm**, sélectionnez l'URL dans le champ SHAREPOINTSITEURL et copiez-la.
6.  Collez cette URL dans le champ d'adresse de votre navigateur Internet.
7.  Lorsque vous y êtes invité, entrez les informations d'identification du compte d'utilisateur que vous avez indiquées lors de la création de la batterie de serveurs.

Sur le site d'administration centrale de SharePoint, vous pouvez configurer Mes sites, des applications SharePoint, ainsi que d'autres fonctionnalités. Pour plus d'informations, consultez la page [Configurer SharePoint 2013][Configurer SharePoint 2013]. Pour accéder au site d'administration centrale de SharePoint, procédez comme suit :

1.  Dans la version préliminaire du portail Azure, cliquez sur **Parcourir**, puis sur **Groupes de ressources**.
2.  Dans la liste des groupes de ressources, cliquez sur le nom du groupe de ressources de votre batterie de serveurs SharePoint.
3.  Dans le volet du groupe de ressources de votre batterie de serveurs SharePoint, cliquez sur **Historique du déploiement**.
4.  Dans le volet **Historique du déploiement**, cliquez sur **Microsoft.SharePointFarm**.
5.  Dans le volet **Microsoft.SharePointFarm**, sélectionnez l'URL dans le champ SHAREPOINTCENTRALADMINURL et copiez-la.
6.  Collez cette URL dans le champ d'adresse de votre navigateur Internet.
7.  Lorsque vous y êtes invité, entrez les informations d'identification du compte d'utilisateur que vous avez indiquées lors de la création de la batterie de serveurs.

Remarques :

-   La version préliminaire du portail Azure crée ces machines virtuelles dans votre abonnement.
-   La version préliminaire du portail Azure crée ces deux batteries de serveurs dans un réseau virtuel sur le cloud uniquement avec une présence web accessible sur Internet. Il n'existe pas de connexion VPN de site à site en amont vers le réseau de votre entreprise.
-   Vous pouvez administrer ces serveurs par l'intermédiaire de connexions Bureau à distance.

## Parcours de la configuration

Pour créer votre batterie SharePoint avec Batterie de serveurs SharePoint, procédez comme suit :

1.  Dans la [version préliminaire du portail Microsoft Azure][version préliminaire du portail Microsoft Azure], cliquez sur **Machines virtuelles** \> **Batterie de serveurs SharePoint**.
2.  Dans le volet **Créer une batterie de serveurs SharePoint**, tapez le nom d'un groupe de ressources.
3.  Tapez un nom d'utilisateur et un mot de passe pour le compte administrateur local sur chaque machine virtuelle de votre batterie. Sélectionnez un nom et un mot de passe difficiles à deviner.
4.  Si vous souhaitez une batterie à haute disponibilité, cliquez sur **Activer la haute disponibilité**.
5.  Pour configurer vos contrôleurs de domaine, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d'hôte (par défaut, il s'agit du nom du groupe de ressources), le nom de domaine racine de forêt (contoso.com, par défaut) et la taille de vos contrôleurs de domaine (A1, par défaut).
6.  Pour configurer vos serveurs SQL, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d'hôte (par défaut, il s'agit du nom du groupe de ressources), la taille de vos serveurs SQL (A5, par défaut), un nom et un mot de passe de compte d'accès à la base de données (par défaut, il s'agit du compte administrateur), ainsi qu'un nom de compte de service de serveur SQL (sqlservice) et un mot de passe (par défaut, le même mot de passe que le compte administrateur est utilisé).
7.  Pour configurer vos serveurs SharePoint, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d'hôte (par défaut, il s'agit du nom du groupe de ressources), la taille de vos serveurs SharePoint (A3 par défaut), un compte d'utilisateur SharePoint (par défaut, il s'agit de sp\_setup) et un mot de passe, un nom de compte de batterie de serveurs SharePoint (par défaut, il s'agit de sp\_farm) et un mot de passe, ainsi qu'une phrase secrète de batterie de serveurs SharePoint. Par défaut, vous utiliserez le mot de passe administrateur du compte d'utilisateur SharePoint, le compte de la batterie de serveurs et une phrase secrète.
8.  Pour définir la configuration facultative (réseau virtuel, compte de stockage ou diagnostics), cliquez sur la flèche.
9.  Pour indiquer l'abonnement, cliquez sur la flèche.
10. Pour indiquer l'emplacement (région), cliquez sur la flèche.
11. Une fois ces opérations effectuées, cliquez sur **Créer**.

## Azure Resource Manager

La batterie de serveurs SharePoint utilise des scripts et le module Azure Resource Manager pour créer automatiquement l'infrastructure et les configurations serveur relatives à ces batteries de serveurs SharePoint. Pour plus d'informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager][Utilisation de Windows PowerShell avec Resource Manager].

  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png
  [Détails de configuration de la batterie de serveurs SharePoint]: ../virtual-machines-sharepoint-farm-config-azure-preview/
  [Configurer SharePoint 2013]: http://technet.microsoft.com/library/ee836142.aspx
  [version préliminaire du portail Microsoft Azure]: https://portal.azure.com/
  [Utilisation de Windows PowerShell avec Resource Manager]: http://azure.microsoft.com/fr-fr/documentation/articles/powershell-azure-resource-manager/
