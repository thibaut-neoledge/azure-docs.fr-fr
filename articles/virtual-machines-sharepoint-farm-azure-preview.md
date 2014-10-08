<properties title="SharePoint Server Farm" pageTitle="SharePoint Server Farm" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="josephd"></tags>

# Batterie de serveurs SharePoint

Avec la batterie de serveurs SharePoint, la version préliminaire du portail Azure crée automatiquement une batterie SharePoint Server 2013 préconfigurée. Cela peut vous faire gagner un temps précieux lorsque vous avez besoin d'une batterie de serveurs SharePoint de base ou à haute disponibilité pour un environnement de test et de développement ou si vous envisagez l'adoption de SharePoint Server 2013 comme solution de collaboration pour votre entreprise.

La batterie de serveurs SharePoint de base se compose de trois machines virtuelles dans la configuration suivante :

![sharepointfarm][]

Vous pouvez utiliser cette configuration de batterie comme configuration simplifiée pour le développement d'applications SharePoint ou pour votre évaluation initiale de SharePoint 2013.

La batterie de serveurs SharePoint à haute disponibilité se compose de neuf machines virtuelles dans la configuration suivante :

![sharepointfarm][1]

Vous pouvez utiliser cette configuration de batterie pour tester des charges de clients plus élevées, la haute disponibilité du site SharePoint externe et SQL Server AlwaysOn pour une batterie SharePoint. Vous pouvez également l'utiliser pour le développement d'applications SharePoint dans un environnement à haute disponibilité.

Pour obtenir des informations de configuration détaillées sur ces deux types de batteries de serveurs, consultez la rubrique [Détails de configuration de la batterie de serveurs SharePoint][].

Ces batteries disposent d'un point de terminaison préconfiguré afin d'autoriser le trafic web authentifié (port TCP 80) vers le serveur web SharePoint par le biais d'un ordinateur client connecté à Internet. Ce point de terminaison pointe vers un site d'équipe préconfiguré. Pour accéder à ce site d'équipe, vous devez d'abord obtenir le nom de domaine du serveur web de la batterie SharePoint :

1.  Dans la version préliminaire du portail Azure, cliquez sur **Parcourir**, puis sur **Machines virtuelles**.
2.  Cliquez sur le nom de votre serveur web (qui se termine par SP, WEB1 ou WEB2) dans la liste des machines virtuelles.
3.  Dans le volet de la machine virtuelle de votre serveur web, cliquez sur **Propriétés**.
4.  Prenez note du nom de domaine complet dans **Nom de domaine**.
5.  Accédez à l'URL **http://[FQDN]** à l'aide de votre navigateur Internet. Dans le cas du nom de domaine spfarm1-web1-contoso.cloudpapp.net, par exemple, l'URL est **http://spfarm1-web1-contoso.cloudpapp.net**.
6.  Lorsque vous y êtes invité, entrez les informations d'identification du compte d'utilisateur que vous avez indiquées lors de la création de la batterie de serveurs.

Sur le site d'administration centrale de SharePoint, vous pouvez configurer Mes sites, des applications SharePoint, ainsi que d'autres fonctionnalités. Pour plus d'informations, consultez la page [Configurer SharePoint 2013][]. Pour accéder au site d'administration centrale de SharePoint, vous devez d'abord obtenir son numéro de port externe.

1.  Dans la version préliminaire du portail Azure, cliquez sur **Parcourir**, puis sur **Machines virtuelles**.
2.  Dans la liste des machines virtuelles, cliquez sur le nom du serveur SharePoint pour la configuration de base (se termine par SP) ou le nom des serveurs d'applications pour la configuration à haute disponibilité (se termine par APP1 ou APP2).
3.  Dans le volet de la machine virtuelle, cliquez sur **Propriétés**.
4.  Prenez note du nom de domaine complet dans **Nom de domaine**.
5.  Fermez le volet **Propriétés**.
6.  Dans le volet de la machine virtuelle, accédez à la liste **Points de terminaison**.
7.  Prenez note du numéro de port du point de terminaison nommé SPCentralAdmin.
8.  Accédez à l'URL **http://[FQDN]:[numéro de port]** à l'aide de votre navigateur Internet. Dans le cas du nom de domaine spfarm1-web1-contoso.cloudpapp.net et du numéro de port externe 54398, par exemple, l'URL est **http://spfarm1-app1-contoso.cloudpapp.net:54398**.
9.  Lorsque vous y êtes invité, entrez les informations d'identification du compte d'utilisateur que vous avez indiquées lors de la création de la batterie de serveurs.

Remarques :

-   La version préliminaire du portail Azure crée ces machines virtuelles dans     votre abonnement.
-   La version préliminaire du portail Azure crée ces deux batteries de serveurs dans un réseau virtuel sur le cloud uniquement avec une présence web accessible sur Internet. Il n'existe pas de connexion VPN de site à site en amont vers le réseau de votre entreprise.
-   Vous pouvez administrer ces serveurs par l'intermédiaire de connexions Bureau à distance.

## Parcours de la configuration

Pour créer votre batterie SharePoint avec Batterie de serveurs SharePoint, procédez comme suit :

1.  Dans la [version préliminaire du portail Microsoft Azure][], cliquez sur **Machines virtuelles** > **Batterie de serveurs SharePoint**.
2.  Dans le volet **Créer une batterie de serveurs SharePoint**, tapez le nom d'un groupe de ressources.
3.  Tapez un nom d'utilisateur et un mot de passe pour le compte administrateur local sur chaque machine virtuelle de votre batterie. Sélectionnez un nom et un mot de passe difficiles à deviner.
4.  Si vous souhaitez une batterie à haute disponibilité, cliquez sur **Activer la haute disponibilité**.
5.  Pour configurer vos contrôleurs de domaine, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d'hôte (par défaut, il s'agit du nom du groupe de ressources), le nom de domaine racine de forêt (contoso.com, par défaut) et la taille de vos contrôleurs de domaine (A1, par défaut).
6.  Pour configurer vos serveurs SQL, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d'hôte (par défaut, il s'agit du nom du groupe de ressources), la taille de vos serveurs SQL (A5, par défaut), un nom et un mot de passe de compte d'accès à la base de données (par défaut, il s'agit du compte administrateur), ainsi qu'un nom de compte de service de serveur SQL (sqlservice) et un mot de passe (par défaut, le même mot de passe que le compte administrateur est utilisé).
7.  Pour configurer vos serveurs SharePoint, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d'hôte (par défaut, il s'agit du nom du groupe de ressources), la taille de vos serveurs SharePoint (A23, par défaut), un compte d'utilisateur SharePoint (par défaut, il s'agit de sp\_setup) et un mot de passe, un nom de compte de batterie de serveurs SharePoint (par défaut, il s'agit de sp\_farm) et un mot de passe, ainsi qu'une phrase secrète de batterie de serveurs SharePoint. Par défaut, vous utiliserez le mot de passe administrateur du compte d'utilisateur SharePoint, le compte de la batterie de serveurs et une phrase secrète.
8.  Pour définir la configuration facultative (réseau virtuel, compte de stockage ou diagnostics), cliquez sur la flèche.
9.  Pour indiquer l'abonnement, cliquez sur la flèche.
10. Pour indiquer l'emplacement (région), cliquez sur la flèche.
11. Une fois ces opérations effectuées, cliquez sur **Créer**.

## Azure Resource Manager

La batterie de serveurs SharePoint utilise des scripts et le module Azure Resource Manager pour créer automatiquement l'infrastructure et les configurations serveur relatives à ces batteries de serveurs SharePoint. Pour plus d'informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager][].

  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png
  [Détails de configuration de la batterie de serveurs SharePoint]: ../virtual-machines-sharepoint-farm-config-azure-preview/
  [Configurer SharePoint 2013]: http://technet.microsoft.com/library/ee836142.aspx
  [version préliminaire du portail Microsoft Azure]: https://portal.azure.com/
  [Utilisation de Windows PowerShell avec Resource Manager]: http://azure.microsoft.com/en-us/documentation/articles/powershell-azure-resource-manager/
