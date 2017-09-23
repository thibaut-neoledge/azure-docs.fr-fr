---
title: "Exemple DMZ : créer une zone démilitarisée (DMZ) pour protéger les applications avec un pare-feu et des groupes de sécurité réseau | Microsoft Docs"
description: "Créer une zone DMZ avec un pare-feu et des groupes de sécurité réseau (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0
ms.contentlocale: fr-fr
ms.lasthandoff: 02/09/2017

---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemple 2 : Créer une zone démilitarisée (DMZ) pour protéger les applications avec un pare-feu et des groupes de sécurité réseau
[Revenir à la page Meilleures pratiques relatives aux frontières de sécurité][HOME]

Cet exemple crée une zone DMZ avec un pare-feu, quatre serveurs Windows et des groupes de sécurité réseau. Vous y découvrirez également comment chacune des commandes concernées fournit une meilleure connaissance de chaque opération. Il comporte également une section Scénario de trafic (Traffic Scenario) qui explique en détail et étape par étape l’évolution du trafic à travers les couches de défense dans la zone DMZ. Enfin, dans la section de référence se trouve l’intégralité du code et des instructions permettant d’élaborer l’environnement destiné à tester et à expérimenter différents scénarios. 

![Zone DMZ entrante avec NVA et NSG][1]

## <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

* deux services cloud : « FrontEnd001 », « BackEnd001 »,
* Un réseau virtuel « CorpNetwork » avec deux sous-réseaux : « FrontEnd » et « BackEnd »
* Un groupe de sécurité réseau unique qui est appliqué aux deux sous-réseaux
* Une appliance virtuelle du réseau, dans cet exemple un pare-feu Barracuda NextGen Firewall, connectée au sous-réseau Frontend
* un serveur Windows Server représentant un serveur web d’application (« IIS01 »),
* Deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »)
* Un serveur Windows Server qui représente un serveur DNS (« DNS01 »)

> [!NOTE]
> Bien que cet exemple utilise un pare-feu Barracuda NextGen Firewall, différentes appliances virtuelles du réseau peuvent être utilisées pour cet exemple.
> 
> 

Dans la section Références ci-dessous figure un script PowerShell qui générera une grande partie l’environnement décrit ci-dessus. La création de machines virtuelles et de réseaux virtuels, bien qu’effectuée par l’exemple de script, ne figure pas en détail dans ce document.

Pour créer l’environnement :

1. Enregistrer le fichier XML de configuration réseau contenu dans la section Références (mis à jour avec les noms, l’emplacement et les adresses IP correspondant à un scénario donné)
2. Mettre à jour les variables de l’utilisateur dans le script pour qu’elles correspondent à l’environnement dans lequel le script est exécuté (abonnements, noms de service, etc.)
3. Exécuter le script dans PowerShell

**Remarque**: la région indiquée dans le script PowerShell doit correspondre à la région indiquée dans le fichier xml de configuration réseau.

Une fois que le script s’exécute correctement, les opérations de post-script qui suivent doivent être exécutées :

1. Configurer les règles de pare-feu. Ce sujet est traité dans la section ci-dessous, intitulée Règles de pare-feu.
2. Dans la section Références, il existe deux scripts pour configurer le serveur web et le serveur d’application avec une application web simple permettant le test avec cette configuration DMZ.

La section suivante explique la plupart des instructions de scripts relatives aux groupes de sécurité réseau.

## <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Dans cet exemple, un groupe NSG est créé, puis chargé avec six règles. 

> [!TIP]
> En règle générale, vous devez d’abord créer les règles d’« autorisation » spécifiques, puis les règles de « refus » plus générales. La priorité établit les règles évaluées en premier. Une fois qu’il a été déterminé que le trafic répond à une règle spécifique, aucune autre règle n’est évaluée. Les règles du groupe de sécurité réseau peuvent s’appliquer dans le sens entrant ou sortant (du point de vue du sous-réseau).
> 
> 

Les règles qui suivent sont générées de façon déclarative pour le trafic entrant :

1. Le trafic DNS interne (port 53) est autorisé
2. Le trafic RDP (port 3389) à partir d’Internet vers n’importe quelle machine virtuelle est autorisé
3. Le trafic HTTP (port 80) à partir d’Internet vers l’appliance virtuelle réseau (pare-feu) est autorisé
4. Tout le trafic (tous les ports) IIS01 vers AppVM1 est autorisé
5. Tout trafic (tous les ports) en provenance d’Internet vers l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6. Tout trafic (tous les ports) en provenance du sous-réseau frontal vers le sous-réseau principal est refusé.

Lorsque ces règles sont associées à chacun des sous-réseaux, si une requête HTTP entrante en provenance d’HTTP arrive d’Internet à destination du serveur web, les 3 règles (autorisation) et les 5 règles (refus) s’appliquent. Cependant, comme la règle 3 a une priorité plus élevée, elle seule s’applique, et la règle 5 n’entre pas en jeu. La requête HTTP est donc autorisée à accéder au pare-feu. Si le même trafic tentait d’atteindre le serveur DNS01, la règle 5 (Refus) serait la première à s’appliquer et le trafic ne serait pas autorisé à accéder au serveur. La règle 6 (Refus) bloque la communication du sous-réseau frontal vers le sous-réseau principal (excepté le trafic autorisé dans les règles 1 et 4), ce qui protège le réseau principal en cas d’attaque d’une personne mal intentionnée sur l’application web sur le serveur frontal. Cette personne aurait alors un accès limité au réseau principal « protégé » (uniquement les ressources exposées sur le serveur AppVM01).

Il existe une règle par défaut qui autorise le trafic sortant vers Internet. Pour cet exemple, nous allons autoriser le trafic sortant sans modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux directions, le routage défini par l’utilisateur est requis. Cette opération est expliquée dans un autre exemple qui figure dans le [document de frontière de sécurité principal][HOME].

Les règles NSG abordées ci-dessus sont très similaires aux règles NSG décrites dans [Exemple 1 : créer une zone DMZ simple à l’aide de groupes de sécurité réseau][Example1]. Veuillez consulter la description NSG dans ce document pour obtenir une description détaillée de chaque règle NSG et de ses attributs.

## <a name="firewall-rules"></a>Règles de pare-feu
Un client de gestion devra être installé sur un ordinateur pour gérer le pare-feu et créer les configurations nécessaires. Consultez la documentation du fournisseur de votre pare-feu (ou autre NVA) sur la façon de gérer l’appareil. Le reste de cette section décrit la configuration du pare-feu lui-même par le biais du client de gestion des fournisseurs (et non par le portail Azure ou PowerShell).

Les instructions de téléchargement client et de connexion à Barracuda utilisées dans cet exemple se trouvent ici : [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Sur le pare-feu, vous devrez créer des règles de transfert. Étant donné que cet exemple achemine uniquement le trafic Internet entrant vers le pare-feu, puis vers le serveur web, seule une règle NAT de transfert est requise. Sur le pare-feu Barracuda NextGen Firewall utilisé dans cet exemple, la règle serait une règle NAT de destination (« Dst NAT ») pour autoriser ce trafic.

Pour créer la règle suivante (ou vérifier les règles par défaut existantes), sur le tableau de bord du client administrateur de Barracuda NG, accédez à l’onglet Configuration. Dans la section Configuration opérationnelle, cliquez sur Ensemble de règles. Une grille nommée « Règles principales » affiche les règles actives et désactivées sur le pare-feu. Dans le coin supérieur droit de cette grille se trouve un petit bouton « + » vert. Cliquez dessus pour créer une nouvelle règle (Remarque : votre pare-feu peut être « verrouillé » contre les modifications. Si vous voyez un bouton marqué « Verrouiller » et que vous ne pouvez pas créer ou modifier les règles, cliquez dessus pour « déverrouiller » l’ensemble de règles et autoriser la modification). Si vous souhaitez modifier une règle existante, sélectionnez cette règle, cliquez avec le bouton droit et sélectionnez Modifier la règle.

Créez une nouvelle règle et indiquez un nom, par exemple « WebTraffic ». 

L'icône de la règle NAT de destination se présente ainsi : ![Icône NAT de destination][2]

La règle proprement dite se présente ainsi :

![Règle de pare-feu][3]

Ici, toute adresse entrante qui atteint le pare-feu en essayant d'atteindre HTTP (port 80 ou 443 pour HTTPS) est envoyée à l'interface « DHCP1 Local IP » du pare-feu et redirigée vers le serveur web avec l'adresse IP 10.0.1.5. Dans la mesure où le trafic entrant utilise le port 80 et le trafic sortant à destination du serveur web utilise le port 80, aucun changement de port n’est nécessaire. Toutefois, la liste des cibles aurait pu être 10.0.1.5:8080 si notre serveur Web avait été à l'écoute sur le port 8080 et avait par conséquent traduit le port 80 entrant sur le pare-feu en port 8080 entrant sur le serveur web.

Une méthode de connexion doit également être indiquée. Pour la règle de destination à partir d'Internet, « SNAT dynamique » est la plus appropriée. 

Bien qu'une seule règle soit créée, il est important de définir correctement sa priorité. Si, dans la grille de toutes les règles du pare-feu, cette nouvelle règle se trouve en bas (sous la règle « BLOCKALL »), elle n’entrera jamais en jeu. Assurez-vous que la règle nouvellement créée pour le trafic web soit au-dessus de la règle BLOCKALL.

Une fois la règle créée, elle doit être transférée vers le pare-feu et activée. Si cette opération n’est pas effectuée, la modification de règle ne prendra pas effet. Le processus de push et d’activation est décrit dans la section suivante.

## <a name="rule-activation"></a>Activation d’une règle
Une fois l'ensemble de règles modifié par l’ajout de cette règle, l’ensemble de règles doit être chargé sur le pare-feu et activé.

![Activation de règle de pare-feu][4]

Dans le coin supérieur droit du client de gestion se trouve un ensemble de boutons. Cliquez sur le bouton « Envoyer les modifications » pour envoyer les règles modifiées au pare-feu, puis cliquez sur le bouton « Activer ».

Avec l’activation de l’ensemble de règles de pare-feu, la création de l’environnement de cet exemple est terminée. Les scripts post-build de la section Références peuvent si nécessaire être exécutés pour ajouter une application à cet environnement afin de tester les scénarios de trafic ci-dessous.

> [!IMPORTANT]
> Il est essentiel de comprendre que vous n’atteindrez pas le serveur web directement. Lorsqu'un navigateur demande une page HTTP à FrontEnd001.CloudApp.Net, le point de terminaison HTTP (port 80) transmet ce trafic au pare-feu et non au serveur web. Ensuite, le pare-feu, en fonction de la règle créée ci-dessus, exécute la traduction NAT de cette demande sur le serveur Web.
> 
> 

## <a name="traffic-scenarios"></a>Scénarios de trafic
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Autorisé) web vers serveur web via le pare-feu
1. Un utilisateur Internet demande une page HTTP en provenance de FrontEnd001.CloudApp.Net (service cloud face à Internet)
2. Le service cloud transfère le trafic via un point de terminaison ouvert sur le port 80 vers l’interface locale de pare-feu sur 10.0.1.4:80 (serveur web)
3. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
   1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
   2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
   3. La règle NSG 3 (Internet vers pare-feu) s’applique, le trafic est autorisé, arrêter le traitement
4. Le trafic parvient à l’adresse IP du pare-feu (10.0.1.4)
5. La règle de transfert du pare-feu constate que le trafic utilise le port 80, elle redirige vers le serveur web IIS01
6. IIS01 écoute le trafic web, reçoit cette requête et commence à traiter la demande
7. IIS01 demande des informations au serveur SQL Server sur AppVM01
8. Aucune règle sortante sur le sous-réseau du serveur frontal, le trafic est autorisé
9. Le sous-réseau du serveur principal commence le traitement de la règle de trafic entrant :
   1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
   2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
   3. La règle NSG 3 (Internet vers le pare-feu) ne s’applique pas, passer à la règle suivante
   4. La règle NSG 4 (IIS01 vers AppVM01) s’applique, le trafic est autorisé, arrêter le traitement des règles
10. AppVM01 reçoit la requête SQL et répond
11. Comme il n’existe aucune règle sur le trafic sortant sur le sous-réseau du serveur principal, la réponse est autorisée.
12. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
    1. Aucune règle NSG ne s’applique au trafic entrant en provenance du sous-réseau du serveur principal vers le sous-réseau du serveur frontal, par conséquent aucune des règles NSG ne s’applique
    2. La règle du système par défaut autorisant le trafic entre sous-réseaux autorise le trafic, le trafic est donc autorisé.
13. Le serveur IIS reçoit la réponse SQL, complète la réponse HTTP et l’envoie au demandeur
14. Dans la mesure où il s'agit d'une session NAT provenant du pare-feu, la destination de la réponse est (initialement) le pare-feu
15. Le pare-feu reçoit la réponse du serveur Web et le transfère à l'utilisateur Internet
16. Comme il n’existe aucune règle sortante sur le sous-réseau du serveur frontal, la réponse est autorisée, et l’utilisateur Internet reçoit la page web demandée.

#### <a name="allowed-rdp-to-backend"></a>(Autorisé) RDP vers le serveur principal
1. L’administrateur du serveur sur Internet demande une session RDP AppVM01 sur BackEnd001.CloudApp.Net:xxxxx, où xxxxx est le numéro de port attribué de façon aléatoire au trafic RDP vers AppVM01 (le port attribué se trouve sur le portail Azure ou via PowerShell)
2. Étant donné que le pare-feu n'écoute que sur l'adresse FrontEnd001.CloudApp.Net, il n'est pas impliqué dans ce flux de trafic
3. Le sous-réseau du serveur principal entame le traitement du réseau entrant :
   1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
   2. La règle NSG 2 (RDP) s’applique, le trafic est autorisé, arrêter le traitement des règles
4. En l’absence de réseau sortant, les règles par défaut s’appliquent et le retour de trafic est autorisé
5. La session RDP est activée
6. AppVM01 demande le mot de passe utilisateur

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Autorisé) recherche DNS du serveur web sur le serveur DNS
1. Le serveur Web Server IIS01 a besoin d’un flux de données sur www.data.gov, mais doit résoudre l’adresse.
2. La configuration réseau du réseau virtuel définit DNS01 (10.0.2.4 sur le sous-réseau du serveur principal) comme serveur DNS principal, IIS01 envoie la requête DNS à DNS01
3. Aucune règle sortante sur le sous-réseau du serveur frontal, le trafic est autorisé
4. Le sous-réseau du serveur principal entame le traitement du réseau entrant :
   1. La règle NSG 1 (DNS) s’applique, le trafic est autorisé, arrêter le traitement des règles
5. Le serveur DNS reçoit la demande
6. Le serveur DNS n’a pas d’adresse en cache et demande à un serveur DNS racine sur Internet.
7. Aucune règle sortante sur le sous-réseau du serveur principal, le trafic est autorisé
8. Un serveur Internet DNS répond, car cette session a été initialisée en interne, la réponse est autorisée
9. Le serveur DNS met en cache la réponse et répond à la demande initiale à IIS01
10. Aucune règle sortante sur le sous-réseau du serveur principal, le trafic est autorisé
11. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
    1. Aucune règle NSG ne s’applique au trafic entrant en provenance du sous-réseau du serveur principal vers le sous-réseau du serveur frontal, par conséquent aucune des règles NSG ne s’applique
    2. La règle système par défaut autorisant le trafic entre sous-réseaux autorise le trafic, le trafic est donc autorisé
12. IIS01 reçoit la réponse de la part de DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Autorisé) fichier d’accès de serveur Web sur AppVM01
1. IIS01 demande un fichier sur AppVM01
2. Aucune règle sortante sur le sous-réseau du serveur frontal, le trafic est autorisé
3. Le sous-réseau du serveur principal commence le traitement de la règle de trafic entrant :
   1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
   2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
   3. La règle NSG 3 (Internet vers le pare-feu) ne s’applique pas, passer à la règle suivante
   4. La règle NSG 4 (IIS01 vers AppVM01) s’applique, le trafic est autorisé, arrêter le traitement des règles
4. AppVM01 reçoit la demande et répond avec un fichier (en supposant que l’accès est autorisé)
5. Comme il n’existe aucune règle sur le trafic sortant sur le sous-réseau du serveur principal, la réponse est autorisée.
6. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
   1. Aucune règle NSG ne s’applique au trafic entrant en provenance du sous-réseau du serveur principal vers le sous-réseau du serveur frontal, par conséquent aucune des règles NSG ne s’applique
   2. La règle du système par défaut autorisant le trafic entre sous-réseaux autorise le trafic, le trafic est donc autorisé.
7. Le serveur IIS reçoit le fichier

#### <a name="denied-web-direct-to-web-server"></a>(Refusé) Web direct vers le serveur Web
Étant donné que le serveur Web, IIS01 et le pare-feu sont dans le même service cloud, ils partagent la même adresse IP publique. Par conséquent, tout le trafic HTTP est dirigé vers le pare-feu. Alors que la demande est servie avec succès, elle ne peut pas accéder directement au serveur Web. Elle est d’abord transmise, comme prévu, via le pare-feu. Consultez le premier scénario de cette section sur le flux de trafic.

#### <a name="denied-web-to-backend-server"></a>(Refusé) Web vers le serveur principal
1. L’utilisateur Internet tente d’accéder à un fichier sur AppVM01 via le service BackEnd001.CloudApp.Net
2. Comme il n’y a aucun point de terminaison ouvert pour le partage de fichiers, il ne passe pas le service cloud et n’atteint pas le serveur
3. Si les points de terminaison ont été ouverts pour une raison quelconque, la règle NSG 5 (Internet vers le réseau virtuel) bloque ce trafic

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Refusé) recherche DNS web sur le serveur DNS
1. L’utilisateur Internet tente de rechercher un enregistrement DNS interne sur DNS01 par le biais du service BackEnd001.CloudApp.Net
2. Comme aucun point de terminaison n’est ouvert pour DNS, il ne passe pas le service Cloud et n’atteint pas le serveur
3. Si les points de terminaison ont été ouverts pour une raison quelconque, la règle NSG 5 (Internet vers réseau virtuel) bloque ce trafic (Remarque : cette règle 1 (DNS) ne s’applique pas pour deux raisons : tout d’abord l’adresse source est sur Internet, et cette règle s’applique uniquement lorsque la source locale est le réseau virtuel local ; de plus, s’il s’agit d’une règle d’autorisation, le trafic n’est jamais refusé)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Refusé) Accès web vers SQL via le pare-feu
1. Un utilisateur Internet demande des données SQL de FrontEnd001.CloudApp.Net (Service cloud face à Internet)
2. Comme aucun point de terminaison n’est ouvert pour SQL, la demande ne franchit pas le service cloud et n’atteint pas le pare-feu
3. Si des points de terminaison étaient ouverts pour une raison quelconque, le sous-réseau frontal commence le traitement des règles entrantes :
   1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
   2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
   3. La règle NSG 2 (Internet vers pare-feu) s’applique, le trafic est autorisé, arrêter le traitement
4. Le trafic parvient à l’adresse IP du pare-feu (10.0.1.4)
5. Le pare-feu n'a aucune règle de transfert pour SQL et abandonne le trafic

## <a name="conclusion"></a>Conclusion
Il s’agit d’un moyen relativement simple de protéger votre application avec un pare-feu et d’isoler le sous-réseau principal du trafic entrant.

Vous trouverez d’autres exemples et une vue d’ensemble des frontières de sécurité réseau [ici][HOME].

## <a name="references"></a>Références
### <a name="main-script-and-network-config"></a>Script principal et configuration réseau
Enregistrez le script complet dans un fichier de script PowerShell. Enregistrez la configuration réseau dans un fichier nommé « NetworkConf2.xml ».
Modifiez les variables définies par l’utilisateur selon vos besoins. Exécutez le script, puis suivez les instructions d’installation de règle de pare-feu ci-dessus.

#### <a name="full-script"></a>Script complet
Ce script exécutera les actions suivantes en fonction des variables définies par l’utilisateur :

1. Connexion à un abonnement Azure
2. Création d’un nouveau compte de stockage
3. Création d’un nouveau réseau virtuel et de deux sous-réseaux, comme indiqué dans le fichier de configuration du réseau
4. Génération de 4 machines virtuelles Windows Server
5. Configurez un groupe de sécurité réseau, notamment :
   * Création d’un groupe de sécurité réseau
   * Ajout de règles à ce dernier
   * La liaison du groupe de sécurité réseaux au sous-réseaux appropriés

Ce script PowerShell doit être exécuté localement sur un PC ou un serveur connecté à Internet.

> [!IMPORTANT]
> Lorsque ce script est exécuté, des avertissements ou autres messages d’information peuvent s’afficher dans PowerShell. Seuls les messages d’erreur affichés en rouge sont source de préoccupation.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Fichier de configuration réseau
Enregistrer ce fichier XML avec l’emplacement mis à jour et ajouter le lien vers ce fichier à la variable $NetworkConfigFile dans le script ci-dessus.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Exemples de scripts d’application
Si vous souhaitez installer un exemple d’application et d’autres exemples de zone DMZ, vous en trouverez à l’adresse suivante : [Exemple de script d’application][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Zone DMZ avec groupe de sécurité réseau (NSG)"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icône NAT de destination"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Règle de pare-feu"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Activation de règle de pare-feu"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md

