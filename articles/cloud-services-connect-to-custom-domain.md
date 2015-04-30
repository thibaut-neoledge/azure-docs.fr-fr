<properties
  pageTitle="Connexion des rôles de services cloud Azure à un contrôleur de domaine Active Directory personnalisé hébergé dans Azure"
  description="Découvrez comment connecter vos rôles web/de travail à un domaine Active Directory personnalisé à l'aide de Powershell et l'extension de domaine Active Directory"
  services="cloud-services"
  documentationCenter=""
  authors="VMak"
  manager="MadhanA"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="3/5/2015"
    ms.author="vmaker"/>

# Connexion des rôles de services cloud Azure à un contrôleur de domaine Active Directory personnalisé hébergé dans Azure

## Guide par étapes pour connecter vos rôles web/de travail Azure à un contrôleur de domaine personnalisé hébergé sur Azure

Nous allons tout d'abord définir un réseau virtuel (VNET) dans Azure. Nous allons ensuite ajouter un contrôleur de domaine Active Directory (hébergé sur une machine virtuelle Azure) sur le réseau virtuel. Ensuite, nous ajouterons des rôles de service cloud existants sur le réseau virtuel créé au préalable avant de les connecter au contrôleur de domaine.

Avant de commencer, quelques aspects à prendre en compte :
1.	Ce didacticiel utilise Powershell. Par conséquent, vérifiez que vous disposez d'Azure Powershell installé et prêt à l'emploi. Pour obtenir de l'aide sur la configuration d'Azure Powershell, consultez l'article [Installation et configuration d'Azure PowerShell](install-configure-powershell.md).
2.	Vos instances de contrôleur de domaine Active Directory et de rôle web/de travail doivent se trouver dans le réseau virtuel.

Suivez ce guide pas à pas et, si vous rencontrez des problèmes, laissez-nous un commentaire ci-dessous. Nous reviendrons vers vous (oui, nous lisons les commentaires).

## Création d'un réseau virtuel

Vous pouvez créer un réseau virtuel dans Azure via le portail Azure ou Powershell. Pour ce didacticiel, nous utiliserons Powershell. Pour créer un réseau virtuel à l'aide du portail Azure, consultez l'article [Création d'un réseau virtuel](create-virtual-network.md).

    #Create Virtual Network

    $vnetStr =
    @"<?xml version="1.0" encoding="utf-8"?>
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
            <AddressSpace>
              <AddressPrefix>[your-address-prefix]</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="[your-subnet-name]">
                <AddressPrefix>[your-subnet-range]</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    "@;

    $vnetConfigPath = "<path-to-vnet-config>"
    Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath;

## Création d'une machine virtuelle

Une fois que vous avez terminé la configuration du réseau virtuel, vous devrez créer un contrôleur de domaine Active Directory. Pour ce didacticiel, nous configurerons un contrôleur de domaine Active Directory sur une machine virtuelle Azure.

Pour ce faire, créez une machine virtuelle via Powershell à l'aide des commandes ci-dessous.

    #Initialize variables

    $vnetname = '<your-vnet-name>'
    $subnetname = '<your-subnet-name>'
    $vmsvc1 = '<your-hosted-service>'
    $vm1 = '<your-vm-name>'
    $username = '<your-username>'
    $password = '<your-password>'
    $ affgrp = '<your- affgrp>'

    #Create a VM and add it to the Virtual Network

    New-AzureQuickVM -Windows -ServiceName $vmsvc1 -name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname


## Promotion de votre machine virtuelle vers un contrôleur de domaine
Pour configurer la machine virtuelle comme un contrôleur de domaine Active Directory, vous devez ouvrir une session sur la machine virtuelle et la configurer.

Pour vous connecter à la machine virtuelle, vous pouvez obtenir le fichier RDP via Powershell. Utilisez les commandes ci-dessous.

    #Get RDP file

    Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>

Une fois connecté à la machine virtuelle, configurez votre machine virtuelle comme un contrôleur de domaine Active Directory en suivant le guide pas à pas de [Configuration de votre contrôleur de domaine Active Directory de client](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## Ajout du déploiement de votre service cloud sur le réseau virtuel

Ensuite, vous devez ajouter le déploiement de votre service cloud sur le réseau virtuel que vous venez de créer. Pour ce faire, modifiez votre cscfg de service cloud en ajoutant les sections pertinentes à votre cscfg à l'aide de Visual Studio ou de l'éditeur de votre choix.

    <ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
        <Role name="[role-name]">
        <Instances count="[number-of-instances]" />
      </Role>
      <NetworkConfiguration>

        <!--optional-->
        <Dns>
          <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
        </Dns>
        <!--optional-->

        <!--VNET settings-->
        <VirtualNetworkSite name="[virtual-network-name]" />
        <AddressAssignments>
          <InstanceAddress roleName="[role-name]">
            <Subnets>
              <Subnet name="[subnet-name]" />
            </Subnets>
          </InstanceAddress>
        </AddressAssignments>
        <!--VNET settings-->

      </NetworkConfiguration>
    </ServiceConfiguration>

Générez ensuite votre projet de services cloud et déployez-le dans Azure. Pour obtenir de l'aide pour le déploiement de votre package de services cloud dans Azure, consultez l'article [Création et déploiement d'un service cloud](cloud-services-how-to-create-deploy.md#deploy)

## Connexion de vos rôles web/de travail au domaine personnalisé à l'aide de l'extension de domaine Active Directory

Une fois votre projet de service cloud déployé sur Azure, connectez vos instances de rôle pour le domaine Active Directory personnalisé à l'aide de l'extension de domaine Active Directory. Pour ajouter l'extension de domaine Active Directory à votre déploiement de services cloud existants et rejoindre le domaine personnalisé, exécutez les commandes suivantes dans Powershell :

    #Initialize domain variables

    $domain = '<your-domain-name>';
    $dmuser = '$domain\<your-username>';
    $dmpswd = '<your-domain-password>';
    $dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force;
    $dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd);

    #Add AD Domain Extension to the cloud service roles

    Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35;

Et voilà.

Vos services cloud doivent désormais être joints à votre contrôleur de domaine personnalisé. Si vous souhaitez en savoir plus sur les différentes options disponibles pour la configuration de l'extension de domaine Active Directory, utilisez l'aide de la commande PS comme indiqué ci-dessous.

    help Set-AzureServiceADDomainExtension;
    help New-AzureServiceADDomainExtensionConfig;

Nous aimerions également connaître votre avis sur l'utilité pour vous d'avoir une extension qui promeut une machine virtuelle vers un contrôleur de domaine. Si vous pensez que cela peut vous être utile, merci de nous l'indiquer dans la section des commentaires.

Nous espérons que cela vous a été utile !

<!--HONumber=52-->