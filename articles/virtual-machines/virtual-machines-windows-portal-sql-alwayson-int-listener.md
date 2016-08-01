<properties
   pageTitle="Créer l’écouteur de groupe de disponibilité AlwaysOn pour SQL Server dans des machines virtuelles Azure"
   description="Instructions détaillées pour créer un écouteur pour un groupe de disponibilité AlwaysOn SQL Server dans des machines virtuelles Azure"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# Configurer un équilibrage de charge interne pour un groupe de disponibilité AlwaysOn dans Azure

Cette rubrique explique comment créer un équilibrage de charge interne pour un groupe de disponibilité AlwaysOn SQL Server dans des machines virtuelles Azure en cours d’exécution dans le modèle Resource Manager. Un groupe de disponibilité AlwaysOn requiert un équilibrage de charge lorsque les instances SQL Server se trouvent sur des machines virtuelles Azure. Cet équilibrage de charge stocke l’adresse IP de l’écouteur de groupe de disponibilité. Si un groupe de disponibilité englobe plusieurs régions, chacune d’elles a besoin d’un équilibrage de charge.

Pour effectuer cette tâche, vous devez disposer d’un groupe de disponibilité AlwaysOn SQL Server déployé sur des machines virtuelles Azure dans le modèle Resource Manager. Les deux machines virtuelles SQL Server doivent appartenir au même groupe à haute disponibilité. Vous pouvez utiliser le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pour créer automatiquement le groupe de disponibilité AlwaysOn dans Azure Resource Manager. Ce modèle crée automatiquement l’équilibrage de charge interne.

Si vous préférez, vous pouvez [configurer manuellement un groupe de disponibilité AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Cette rubrique requiert que vos groupes de disponibilité soient déjà configurés.

Rubriques connexes :

 - [Configuration de groupes de disponibilité AlwaysOn dans Azure VM (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
 
 - [Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## Étapes

Dans ce document, vous allez créer et configurer un équilibrage de charge dans le portail Azure. Une fois cette opération terminée, vous allez configurer le cluster afin qu’il utilise l’adresse IP de l’équilibrage de charge pour l’écouteur de groupe de disponibilité AlwaysOn.

## Créer et configurer l’équilibrage de charge dans le portail Azure

Dans cette partie de la tâche, vous allez exécuter les étapes suivantes dans le portail Azure :

1. Créer l’équilibrage de charge et configurer l’adresse IP

1. Configurer le pool principal

1. Créer la sonde

1. Configurer les règles d’équilibrage de charge

>[AZURE.NOTE] Si les serveurs SQL se trouvent dans des régions et des groupes de ressources différents, vous effectuerez toutes ces étapes à deux reprises, une fois par groupe de ressources.

## 1\. Créer l’équilibrage de charge et configurer l’adresse IP

La première étape consiste à créer l’équilibrage de charge. Dans le portail Azure, ouvrez le groupe de ressources contenant les machines virtuelles SQL Server. Dans le groupe de ressources, cliquez sur **Ajouter**.

- Recherchez **l’équilibrage de charge**. Dans les résultats de la recherche, sélectionnez l’élément **Équilibrage de charge** publié par **Microsoft**.

- Dans le panneau **Équilibrage de charge**, cliquez sur **Créer**.

- Dans **Créer l’équilibrage de charge**, configurez l’équilibrage de charge comme suit :

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Nom de l’équilibrage de charge. Par exemple, **sqlLB**. |
| **Schéma** | **Interne** |
| **Réseau virtuel** | Sélectionnez le réseau virtuel contenant les serveurs SQL. |
| **Sous-réseau** | Sélectionnez le sous-réseau contenant les serveurs SQL. |
| **Abonnement** | Si vous avez plusieurs abonnements, ce champ peut s’afficher. Sélectionnez l’abonnement que vous souhaitez associer à cette ressource. Normalement, il s’agit du même abonnement que pour toutes les ressources du groupe de disponibilité. |
| **Groupe de ressources** | Sélectionnez le groupe de ressources réseau contenant les serveurs SQL. | 
| **Emplacement** | Sélectionnez l’emplacement Azure contenant les serveurs SQL. |

- Cliquez sur **Créer**.

Azure crée l’équilibrage de charge que vous avez configuré précédemment. Cet équilibrage de charge appartient à un réseau, un sous-réseau, un groupe de ressources et un emplacement spécifiques. Une fois l’opération terminée, vérifiez les paramètres de l’équilibrage de charge dans Azure.

Maintenant, configurez l’adresse IP de l’équilibrage de charge.

- Dans le panneau **Paramètres** de l’équilibrage de charge, cliquez sur **Adresse IP**. Le panneau **Adresse IP** indique qu’il s’agit d’un équilibrage de charge privé sur le même réseau virtuel que vos serveurs SQL.

- Spécifiez les paramètres suivants :

| Paramètre | Valeur |
| ----- | ----- |
| **Sous-réseau** | Sélectionnez le sous-réseau contenant les serveurs SQL. |
| **Affectation** | **Statique** |
| **Adresse IP** | Tapez une adresse IP virtuelle inutilisée appartenant au sous-réseau. |

- Enregistrez les paramètres.

Maintenant, l’équilibrage de charge a une adresse IP. Notez-la. Vous allez l’utiliser pour créer un écouteur sur le cluster. Utilisez cette adresse pour la variable `$ILBIP` dans un script PowerShell plus loin dans cet article.



## 2\. Configurer le pool principal

L’étape suivante consiste à créer un pool d’adresses principal. Azure appelle *pool principal* ce pool d’adresses principal. En l’occurrence, le pool principal contient les adresses des deux serveurs SQL dans votre groupe de disponibilité.

- Dans votre groupe de ressources, cliquez sur l’équilibrage de charge créé.

- Dans **Paramètres**, cliquez sur **Pools principaux**.

- Dans **Pools d’adresses principaux**, cliquez sur **Ajouter** pour créer un pool d’adresses principal.

- Dans **Ajouter un pool principal**, tapez le nom du pool principal sous **Nom**.

- Dans **Machines virtuelles**, cliquez sur **+ Ajouter une machine virtuelle**.

- Dans **Choisir des machines virtuelles**, cliquez sur **Choisir un groupe à haute disponibilité** et spécifiez le groupe à haute disponibilité auquel appartiennent les machines virtuelles SQL Server.

- Après avoir choisi le groupe à haute disponibilité, cliquez sur **Choisir les machines virtuelles**. Cliquez sur les deux machines virtuelles qui hébergent les instances SQL Server dans le groupe de disponibilité. Cliquez sur **Sélectionner**.

- Cliquez sur **OK** pour fermer les panneaux **Choisir les machines virtuelles** et **Ajouter un pool principal**.

Azure met à jour les paramètres du pool d’adresses principal. Votre groupe à haute disponibilité contient maintenant un pool de deux serveurs SQL.

## 3\. Créer une sonde

L’étape suivante consiste à créer une sonde. La sonde vérifie comment Azure va identifier celui des serveurs SQL qui possède l’écouteur de groupe de disponibilité. Azure teste le service avec l’adresse IP sur un port que vous définissez lors de la création de la sonde.

- Dans le panneau **Paramètres** de l’équilibrage de charge, cliquez sur **Sondes**.

- Dans le panneau **Sondes**, cliquez sur **Ajouter**.

- Configurez la sonde dans le panneau **Ajouter une sonde**. Utilisez les valeurs suivantes pour configurer la sonde :

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Nom de la sonde. Par exemple, **SQLAlwaysOnEndPointProbe**. |
| **Protocole** | **TCP** |
| **Port** | Vous pouvez utiliser n’importe quel port disponible. Par exemple, *59999*. |
| **Intervalle** | *5* | 
| **Seuil de défaillance sur le plan de l’intégrité** | *2* | 

- Cliquez sur **OK**.

>[AZURE.NOTE] Vérifiez que le port spécifié est ouvert sur le pare-feu des deux serveurs SQL. Les deux serveurs requièrent une règle de trafic entrant sur le port TCP utilisé. Pour plus d’informations, consultez [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx).

Azure crée la sonde. Azure va utiliser cette sonde pour identifier le serveur SQL propriétaire de l’écouteur de groupe de disponibilité.

## 4\. Configurer les règles d’équilibrage de charge

Configurez les règles d’équilibrage de charge. Les règles d’équilibrage de charge déterminent comment l’équilibrage de charge achemine le trafic aux serveurs SQL. Pour cet équilibrage de charge, vous allez activer le retour direct du serveur, car seul un serveur SQL peut posséder l’écouteur de groupe de disponibilité.

- Dans le panneau **Paramètres** de l’équilibrage de charge, cliquez sur **Règles d’équilibrage de charge**.

- Dans le panneau **Règles d’équilibrage de charge**, cliquez sur **Ajouter**.

- Utilisez le panneau **Ajouter une règle d’équilibrage de charge** pour configurer la règle d’équilibrage de charge. Utilisez les paramètres suivants :

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Nom de la règle d’équilibrage de charge. Par exemple, **SQLAlwaysOnEndPointListener**. |
| **Protocole** | **TCP** |
| **Port** | *1433* |
| **Port principal** | *1433*. Notez que ce paramètre sera désactivé, car cette règle utilise le paramètre **Adresse IP flottante (retour serveur direct)**. |
| **Sonde** | Utilisez le nom de la sonde que vous avez créée pour cet équilibrage de charge. |
| **Persistance de session** | **Aucun** | 
| **Délai d’inactivité (minutes).** | *4* | 
| **Adresse IP flottante (retour serveur direct)** | **Activé** | 

 >[AZURE.NOTE] Vous devrez peut-être faire défiler le panneau vers le bas pour afficher tous les paramètres.

- Cliquez sur **OK**.

- Azure configure la règle d’équilibrage de charge. L’équilibrage de charge est maintenant configuré pour acheminer le trafic vers le serveur SQL qui héberge l’écouteur de groupe de disponibilité.

À ce stade, le groupe de ressources a un équilibreur de charge qui relie les deux ordinateurs SQL Server. L’équilibrage de charge contient également l’adresse IP de l’écouteur de groupe de disponibilité AlwaysOn SQL Server, afin que l’un ou l’autre ordinateur puisse répondre aux demandes des groupes de disponibilité.

>[AZURE.NOTE] Si vos serveurs SQL se trouvent dans deux régions, répétez les étapes dans l’autre région. Chaque région nécessite un équilibrage de charge.

## Configurer le cluster pour qu’il utilise l’adresse IP de l’équilibrage de charge 

L’étape suivante consiste à configurer l’écouteur sur le cluster et à le mettre en ligne. Pour ce faire, procédez comme suit :

1. Créer l’écouteur de groupe de disponibilité sur le cluster de basculement

1. Mettre l'écouteur en ligne

## 1\. Créer l’écouteur de groupe de disponibilité sur le cluster de basculement

Dans cette étape, vous créez manuellement l'écouteur du groupe de disponibilité dans le Gestionnaire du cluster de basculement et SQL Server Management Studio (SSMS).

- Utilisez le protocole RDP pour vous connecter à la machine virtuelle Azure qui héberge le réplica principal.

- Ouvrez le Gestionnaire du cluster de basculement.

- sélectionnez le nœud **Réseaux** et notez le nom de réseau du cluster. Ce nom sera utilisé dans la variable `$ClusterNetworkName` du script PowerShell.

- Développez le nom du cluster, puis cliquez sur **Rôles**.

- Dans le volet**Rôles**, cliquez avec le bouton droit sur le nom du groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d'accès client**.

- Dans la zone **Nom**, créez un nom pour ce nouveau port d'écoute, puis cliquez à deux reprises sur **Suivant** et cliquez sur **Terminer**. Ne mettez pas l'écouteur ou la ressource en ligne à ce stade.

 >[AZURE.NOTE] Le nom du nouvel écouteur est le nom du réseau que les applications vont utiliser pour se connecter aux bases de données du groupe de disponibilité SQL Server.

- Cliquez sur l'onglet **Ressources**, puis développez le Point d'accès Client vous venez de créer. Cliquez avec le bouton droit sur la ressource IP, puis cliquez sur Propriétés. Notez le nom de l’adresse IP. Vous utiliserez ce nom dans la variable `$IPResourceName` du script PowerShell.

- Dans **Adresse IP**, cliquez sur **Adresse IP statique** et spécifiez l’adresse IP que vous avez utilisée pour l’équilibrage de charge sur le portail Azure. Activez NetBIOS pour cette adresse et cliquez sur **OK**. Répétez cette étape pour chaque ressource IP si votre solution couvre plusieurs réseaux virtuels Azure.

- Sur le nœud de cluster qui héberge actuellement le réplica principal, ouvrez une fenêtre PowerShell ISE avec élévation de privilèges et collez les commandes suivantes dans un nouveau script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Mettez à jour les variables et exécutez le script PowerShell pour configurer l’adresse IP et le port du nouvel écouteur.

 >[AZURE.NOTE] Si vos serveurs SQL se trouvent dans différentes régions, vous devez exécuter le script PowerShell à deux reprises. La première fois, utilisez le nom de réseau du cluster, le nom de la ressource IP du cluster et l’adresse IP de l’équilibrage de charge correspondant au premier groupe de ressources. La seconde fois, utilisez le nom de réseau du cluster, le nom de la ressource IP du cluster et l’adresse IP de l’équilibrage de charge correspondant au second groupe de ressources.

Maintenant, le cluster a un écouteur de groupe de disponibilité.

## 2\. Mettre l'écouteur en ligne

Une fois l’écouteur de groupe de disponibilité configuré, vous pouvez le mettre en ligne afin que les applications puissent se connecter aux bases de données du groupe de disponibilité avec celui-ci.

- Retournez dans le Gestionnaire du cluster de basculement. Développez les **Rôles**, puis mettez votre groupe de disponibilité en surbrillance. Dans l’onglet **Ressources**, cliquez avec le bouton droit sur le nom de l’écouteur, puis cliquez sur **Propriétés**.

- Cliquez sur l'onglet **Dépendances**. Si plusieurs ressources sont répertoriées, vérifiez que les adresses IP ont des dépendances OR (et non des dépendances AND). Cliquez sur **OK**.

- Cliquez avec le bouton droit sur l'écouteur, puis cliquez sur **Mettre en ligne**.


- Une fois l'écouteur en ligne, allez dans l'onglet **Ressources**, cliquez avec le bouton droit sur le groupe de disponibilité, puis cliquez sur **Propriétés**.

- Créez une dépendance sur la ressource de nom d'écouteur (pas le nom de ressources Adresse IP). Cliquez sur **OK**.


- Lancez SQL Server Management Studio et connectez-vous au réplica principal.


- Accédez à **Haute disponibilité AlwaysOn** | **Groupes de disponibilité** | **Écouteurs de groupe de disponibilité**.


- Vous devez maintenant voir le nom de l'écouteur que vous avez créé dans le Gestionnaire du cluster de basculement. Cliquez avec le bouton droit sur l’écouteur, puis cliquez sur **Propriétés**.


- Dans le champ **Port**, indiquez le numéro du port de l’écouteur de groupe de disponibilité à l’aide du paramètre $EndpointPort utilisé précédemment (valeur par défaut : 1433), puis cliquez sur **OK**.

Vous avez maintenant un groupe de disponibilité AlwaysOn SQL Server sur des machines virtuelles Azure en mode Resource Manager.

## Tester la connexion à l’écouteur

Pour tester la connexion :

1. Envoyez une requête RDP à un serveur SQL qui se trouve dans le même réseau virtuel, mais qui ne possède pas le réplica. Il peut s’agir de l’autre serveur SQL du cluster.

1. Utilisez l’utilitaire **sqlcmd** pour tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** au réplica principal par le biais de l’écouteur avec une authentification Windows :

        sqlcmd -S <listenerName> -E

La connexion SQLCMD se connecte automatiquement à l’instance SQL Server hébergeant le réplica principal.

## Instructions et limitations

Notez les instructions suivantes concernant l’écouteur de groupe de disponibilité dans Azure utilisant l’équilibrage de charge interne :

- Le service cloud ne prend en charge qu’un écouteur de groupe de disponibilité interne, car l’écouteur est configuré sur le seul équilibrage de charge interne. Toutefois, il est possible de créer plusieurs écouteurs externes.

- Avec un équilibrage de charge interne, vous n’accédez à l’écouteur qu’à partir du même réseau virtuel.
 

<!---HONumber=AcomDC_0720_2016-->