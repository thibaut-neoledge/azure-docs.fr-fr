<properties 
   pageTitle="Configurer DNS entre deux réseaux virtuels Azure | Microsoft Azure" 
   description="Découvrez comment configurer des connexions VPN et la résolution de noms de domaine entre deux réseaux virtuels et comment configurer la géo-réplication HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/04/2016"
   ms.author="jgao"/>

# Configurer des serveurs DNS entre deux réseaux virtuels Azure

> [AZURE.SELECTOR]
- [Configurer la connectivité VPN](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configurer DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configurer la réplication HBase](hdinsight-hbase-geo-replication.md) 


Découvrez comment ajouter et configurer des serveurs DNS sur des réseaux virtuels Azure pour gérer la résolution de nom au sein de réseaux virtuels et entre eux.

Ce didacticiel est la deuxième partie de la [série][hdinsight-hbase-geo-replication] portant sur la création d'une géo-réplication HBase :

- [Configurer une connexion VPN entre deux réseaux virtuels][hdinsight-hbase-geo-replication-vnet]
- Configurer des serveurs DNS pour les réseaux virtuels (ce didacticiel)
- [Configurer la géo-réplication HBase][hdinsight-hbase-geo-replication]


Le diagramme suivant illustre les deux réseaux virtuels que vous avez créés dans la page [Configurer une connexion VPN entre deux réseaux virtuels][hdinsight-hbase-geo-replication-vnet] :

![Diagramme du réseau virtuel de la réplication HDInsight HBase][img-vnet-diagram]

##Configuration requise
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	Avant d'exécuter vos scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l'aide de la cmdlet suivante :

		Add-AzureAccount

	Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

		Select-AzureSubscription <AzureSubscriptionName>

- **Deux réseaux virtuels Azure avec une connexion VPN**. Pour obtenir des instructions, consultez [Configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Les noms des services et des machines virtuelles Azure doivent être uniques. Le nom utilisé dans ce didacticiel est Contoso-[Azure Service/VM name]-[EU/US]. Par exemple, Contoso-VNet-EU est le réseau virtuel Azure du centre de données en Europe du Nord et Contoso-DNS-US est la machine virtuelle du serveur DNS du centre de données de l'Est des États-Unis. Vous devez trouver vos propres noms.
 
 
##Créer des machines virtuelles à utiliser en tant que serveurs DNS

**Pour créer une machine virtuelle au sein de Contoso-VNet-EU, appelée Contoso-DNS-EU**

1.	Cliquez sur **NOUVEAU** > **CALCUL** > **MACHINE VIRTUELLE** > **À PARTIR DE LA GALERIE**.
2.	Choisissez **Windows Server 2012 R2 Datacenter**.
3.	Entrez :
	- **NOM DE LA MACHINE VIRTUELLE** : Contoso-DNS-EU
	- **NOUVEAU NOM D'UTILISATEUR** : 
	- **NOUVEAU MOT DE PASSE** : 
4.	Entrez :
	- **SERVICE CLOUD** : créez un service cloud
	- **RÉGION/GROUPE D'AFFINITÉ/RÉSEAU VIRTUEL** : (sélectionnez Contoso-VNet-EU)
	- **SOUS-RÉSEAUX DU RÉSEAU VIRTUEL** : Subnet-1
	- **COMPTE DE STOCKAGE** : utilisez un compte de stockage généré automatiquement
	
		Le nom du service cloud est identique au nom de la machine virtuelle. Dans ce cas, il s’agit de Contoso-DNS-UE. Pour les machines virtuelles suivantes, il est possible d’utiliser le même service cloud. Toutes les machines virtuelles sous le même service cloud partagent le même réseau virtuel et le même suffixe de domaine.

		Le compte de stockage est utilisé pour stocker le fichier d’image de la machine virtuelle. 
	- **POINTS DE TERMINAISON** : (faites défiler vers le bas et sélectionnez **DNS**) 

Une fois la machine virtuelle créée, recherchez les adresses IP interne et externe.

1.	Cliquez sur le nom de la machine virtuelle, **Contoso-DNS-EU**.
2.	Cliquez sur **Tableau de bord**.
3.	Écrivez :
	- ADRESSE IP VIRTUELLE PUBLIQUE
	- ADRESSE IP INTERNE


**Pour créer une machine virtuelle au sein de Contoso-VNet-US, appelée Contoso-DNS-US**

- Répétez la même procédure pour créer une machine virtuelle avec les valeurs suivantes :
	- NOM DE LA MACHINE VIRTUELLE : Contoso-DNS-US
	- RÉGION/GROUPE D'AFFINITÉ/RÉSEAU VIRTUEL : sélectionnez Contoso-VNet-US
	- SOUS-RÉSEAUX DU RÉSEAU VIRTUEL : Subnet-1
	- COMPTE DE STOCKAGE : utilisez un compte de stockage généré automatiquement
	- POINTS DE TERMINAISON : (sélectionnez DNS)

##Définir les adresses IP statiques pour les deux machines virtuelles

Les serveurs DNS doivent avoir des adresses IP statiques. Cette étape ne peut pas être effectuée depuis le portail Azure Classic. Vous devez utiliser Azure PowerShell.

**Pour configurer une adresse IP statique pour les deux machines virtuelles**

1. Ouvrez Windows PowerShell ISE.
2. Exécutez les applets de commande suivantes :  

		Add-AzureAccount
		Select-AzureSubscription [YourAzureSubscriptionName]
		
		Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
		Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

	ServiceName est le nom du service cloud. Étant donné que le serveur DNS est la première machine virtuelle du service cloud, le nom du service cloud est identique à celui de la machine virtuelle.

	Vous devrez peut-être mettre à jour ServiceName et Name pour qu'ils correspondent aux noms que vous avez.


##Ajoutez les rôles Serveur DNS des deux machines virtuelles

**Pour ajouter le rôle Serveur DNS pour Contoso-DNS-EU**

1.	Dans le portail Azure Classic, cliquez sur **Machines virtuelles** à gauche. 
2.	Cliquez sur **Contoso-DNS-EU**.
3.	Cliquez sur **TABLEAU DE BORD** en haut.
4.	Cliquez sur **CONNECTER** en bas et suivez les instructions pour vous connecter à la machine virtuelle via RDP.
2.	Dans la session RDP, cliquez sur le bouton Windows en bas à gauche pour ouvrir l'écran d'accueil.
3.	Cliquez sur la vignette **Gestionnaire de serveur**.
4.	Cliquez sur **Ajouter des rôles et fonctionnalités**.
5.	Cliquez sur **Suivant**
6.	Sélectionnez **Installation basée sur un rôle ou une fonctionnalité**, puis cliquez sur **Suivant**.
7.	Sélectionnez votre machine virtuelle DNS (elle doit être mise en surbrillance), puis cliquez sur **Suivant**.
8.	Vérifiez le **Serveur DNS** :
9.	Cliquez sur **Ajouter des fonctionnalités**, puis sur **Continuer**.
10.	Cliquez sur **Suivant** trois fois de suite, puis sur **Installer**. 

**Pour ajouter le rôle Serveur DNS pour Contoso-DNS-US**

- Répétez les étapes pour ajouter le rôle DNS à **Contoso-DNS-US**.

##Assigner des serveurs DNS aux réseaux virtuels

**Pour inscrire les deux serveurs DNS**

1.	Dans le portail Azure Classic, cliquez sur **NOUVEAU**, **SERVICES RÉSEAU**, **RÉSEAU VIRTUEL**, **INSCRIRE LE SERVEUR DNS**.
2.	Entrez :
	- **NOM** : Contoso-DNS-EU
	- **ADRESSE IP DU SERVEUR DNS** : 10.1.0.4. L'adresse IP doit correspondre à celle de la machine virtuelle du serveur DNS.
	 
3.	Répétez la procédure pour inscrire les Contoso-DNS-US avec les paramètres suivants :
	- **NOM** : Contoso-DNS-US
	- **ADRESSE IP DU SERVEUR DNS** : 10.2.0.4

**Pour assigner les deux serveurs DNS aux deux réseaux virtuels**

1.	Cliquez sur **Réseaux** dans le volet gauche du portail Azure Classic.
2.	Cliquez sur **Contoso-VNet-EU**.
3.	Cliquez sur **CONFIGURER**.
4.	Sélectionnez **Contoso-DNS-EU** dans la section **serveurs dns**.
5.	Cliquez sur **ENREGISTRER** en bas de la page et sur **Oui** pour confirmer.
6.	Répétez le processus pour assigner le serveur DNS **Contoso-DNS-US** au réseau virtuel **Contoso-VNet-US**.

Toutes les machines virtuelles qui ont été déployées sur les réseaux virtuels doivent être redémarrées pour mettre à jour la configuration du serveur DNS.

**Pour redémarrer les machines virtuelles**

1. Dans le portail Azure Classic, cliquez sur **Machines virtuelles** à gauche.
2. Cliquez sur **Contoso-DNS-EU**.
3. Cliquez sur **Tableau de bord** en haut.
4. Cliquez sur **REDÉMARRER** en bas.
5. Répétez ces étapes pour redémarrer **Contoso-DNS-US**.


##Configurer des redirecteurs DNS conditionnels

Le serveur DNS sur chaque réseau virtuel peut uniquement résoudre des noms DNS au sein de ce réseau virtuel. Vous devez configurer un redirecteur conditionnel pour pointer vers le serveur DNS homologue pour les résolutions de noms du réseau virtuel homologue.

Pour configurer le redirecteur conditionnel, vous devez connaître les suffixes de domaine des deux serveurs DNS. Les suffixes DNS peuvent être différents selon la configuration des services cloud utilisés quand vous avez créé les machines virtuelles. Pour chaque suffixe DNS utilisé dans le réseau virtuel, vous devez ajouter un redirecteur conditionnel.

**Pour rechercher les suffixes de domaine des deux serveurs DNS**

1. Ouvrez une session RDP sur **Contoso-DNS-EU**.
2. Ouvrez la console Windows PowerShell ou une invite de commandes.
3. Exécutez **ipconfig** et notez le **suffixe DNS propre à la connexion**.
4. Ne fermez pas la session RDP, vous en aurez encore besoin. 
5. Répétez ces étapes pour déterminer le **suffixe DNS propre à la connexion** de **Contoso-DNS-US**.


**Pour configurer des redirecteurs DNS**
 
1.	Dans la session RDP sur **Contoso-DNS-EU**, cliquez sur la touche Windows en bas à gauche.
2.	Cliquez sur **Outils d'administration**.
3.	Cliquez sur **DNS**.
4.	Dans le volet gauche, développez **DSN**, **Contoso-DNS-EU**.
5.	Entrez les informations suivantes :
	- **Domaine DNS** : entrez le suffixe DNS de Contoso-DNS-US. Par exemple : Contoso-DNS-US.b5.internal.cloudapp.net.
	- **Adresses IP des serveurs maîtres** : entrez 10.2.0.4, qui est l'adresse IP de Contoso-DNS-US.
6.	Appuyez sur **Entrée**, puis cliquez sur **OK**. Maintenant, vous pouvez résoudre l'adresse IP de Contoso-DNS-US à partir de Contoso-DNS-EU.
7.	Répétez les étapes pour ajouter un redirecteur DNS au service DNS sur la machine virtuelle de Contoso-DNS-US avec les valeurs suivantes :
	- **Domaine DNS** : entrez le suffixe DNS de Contoso-DNS-EU. 
	- **Adresses IP des serveurs maîtres** : entrez 10.2.0.4, qui est l'adresse IP de Contoso-DNS-EU.

##Tester la résolution de noms sur les réseaux virtuels

Vous pouvez maintenant tester la résolution du nom d'hôte sur les réseaux virtuels. La commande ping est bloquée par le pare-feu par défaut. Vous pouvez utiliser nslookup pour résoudre les machines virtuelles de serveur DNS (vous devez utiliser le nom de domaine complet) dans les réseaux pair à pair.


##Étapes suivantes

Dans ce didacticiel, vous avez vu comment configurer la résolution de noms sur des réseaux virtuels avec des connexions VPN. Les deux autres articles de la série abordent les thèmes suivants :

- [Configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurer la géo-réplication HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png

<!---------HONumber=AcomDC_0309_2016-->