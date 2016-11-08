---
title: Configurer une connexion VPN entre deux réseaux virtuels | Microsoft Docs
description: Découvrez comment configurer des connexions VPN entre deux réseaux virtuels Azure et la résolution de noms de domaine entre deux réseaux virtuels Azure, et configurer la géo-réplication HBase.
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/28/2016
ms.author: jgao

---
# Configurer une connexion VPN entre deux réseaux virtuels Azure
> [!div class="op_single_selector"]
> * [Configurer la connectivité VPN](hdinsight-hbase-geo-replication-configure-VNETs.md)
> * [Configurer DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
> * [Configurer la réplication HBase](hdinsight-hbase-geo-replication.md)
> 
> 

La connexion de site à site entre réseaux virtuels Azure utilise une passerelle VPN pour fournir un tunnel sécurisé utilisant Ipsec/IKE. Les réseaux virtuels peuvent se trouver dans différents abonnements et différentes régions. Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Une connexion entre réseaux virtuels se justifie dans plusieurs scénarios :

* Géo-redondance et présence géographique dans plusieurs régions
* Applications multiniveaux régionales avec une forte limite d'isolement
* Communication interorganisationnelle entre plusieurs abonnements dans Azure

Pour plus d'informations, consultez [Configuration d'une connexion de réseau virtuel à réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

Pour voir la vidéo associée :

> [!VIDEO https://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks/player]
> 
> 

Ce didacticiel fait partie de la [série][hdinsight-hbase-replication] portant sur la création d'une géo-réplication HBase.

* Configurer une connexion VPN entre deux réseaux virtuels (ce didacticiel)
* [Configurer des serveurs DNS pour les réseaux virtuels][hdinsight-hbase-geo-replication-dns]
* [Configurer la géo-réplication HBase][hdinsight-hbase-geo-replication]

Le diagramme suivant illustre les deux réseaux virtuels que vous allez créer dans ce didacticiel :

![Diagramme du réseau virtuel de la réplication HDInsight HBase][img-vnet-diagram]

## Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un poste de travail sur lequel est installé Azure PowerShell**.
  
    Avant d’exécuter vos scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :
  
        Add-AzureAccount
  
    Si vous possédez plusieurs abonnements Azure, utilisez l’applet de commande suivante pour définir l'abonnement en cours :
  
        Select-AzureSubscription <AzureSubscriptionName>
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

> [!NOTE]
> Les noms des services et des machines virtuelles Azure doivent être uniques. Le nom utilisé dans ce didacticiel est Contoso-[Azure Service/VM name]-[EU/US]. Par exemple, Contoso-VNet-EU est le réseau virtuel Azure du centre de données en Europe du Nord et Contoso-DNS-US est la machine virtuelle du serveur DNS du centre de données de l'Est des États-Unis. Vous devez trouver vos propres noms.
> 
> 

## Créer deux réseaux virtuels Azure
**Pour créer un réseau virtuel appelé Contoso-VNet-EU en Europe du Nord**

1. Connectez-vous au [portail Azure Classic][azure-portal].
2. Click **NOUVEAU**, **SERVICES RÉSEAU**, **RÉSEAU VIRTUEL**, **CRÉATION PERSONNALISÉE**.
3. Entrez :
   
   * **NOM** : Contoso-VNet-EU
   * **EMPLACEMENT** : Europe du Nord
     
     Ce didacticiel utilise des centres de données des régions Europe du Nord et Est des États-Unis. Vous pouvez choisir vos propres centres de données.
4. Entrez :
   
   * **SERVEUR DNS** : (laisser vide)
     
     Vous avez besoin de votre propre serveur DNS pour la résolution de noms dans les réseaux virtuels. Pour plus d’informations sur l’utilisation de la résolution de noms dans Azure ou de votre propre serveur DNS, consultez la rubrique [Résolution de noms (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Pour obtenir des instructions sur la configuration de la résolution de noms entre des réseaux virtuels, consultez la page [Configurer des serveurs DNS entre deux réseaux virtuels Azure][hdinsight-hbase-dns].
   * **Configurer un réseau VPN de point à site** : (décoché)
     
     La configuration de point à site ne s’applique pas à ce scénario.
   * **Configurer un réseau VPN de site à site** : (décoché)
     
     Vous allez configurer la connexion VPN de site à site au réseau virtuel Azure dans le centre de données Est des États-Unis.
5. Entrez :
   
   * **ADRESSE IP DE DÉPART DE L'ESPACE D'ADRESSAGE** : 10.1.0.0
   * **CIDR DE L'ESPACE D'ADRESSAGE** : /16
   * **IP DE DÉPART de subnet-1** : 10.1.0.0
   * **CIDR de subnet-1** : /24
   
   L'espace d'adressage ne peut pas chevaucher le réseau virtuel des États-Unis.

**Pour créer un réseau virtuel appelé Contoso-VNet-EU en Europe de l'Ouest**

* Répétez la procédure précédente avec les valeurs suivantes :
  
  * **NOM** : Contoso-VNet-US
  * **EMPLACEMENT** : Est des États-Unis
  * **SERVEUR DNS** : (laisser vide)
  * **Configurer un réseau VPN de point à site** : (décoché)
  * **Configurer un réseau VPN de site à site** : (décoché)
  * **ADRESSE IP DE DÉPART DE L'ESPACE D'ADRESSAGE** : 10.2.0.0
  * **CIDR DE L'ESPACE D'ADRESSAGE** : /16
  * **IP DE DÉPART de subnet-1** : 10.2.0.0
  * **CIDR de subnet-1** : /24

## Configurer une connexion VPN entre les deux réseaux virtuels
### Créer des réseaux locaux
Quand vous créez une configuration de réseau virtuel à réseau virtuel, vous devez configurer chaque réseau virtuel pour identifier l'autre comme un site de réseau local. Dans cette section, vous allez configurer chaque réseau virtuel comme un réseau local. Les réseaux locaux partagent les mêmes espaces d'adressage IP que ceux du réseau virtuel correspondant.

![Configurer la configuration de site à site VPN Azure - réseaux locaux Azure][img-vnet-lnet-diagram]

**Pour créer un réseau local appelé Contoso-LNet-EU correspondant à l'espace d'adressage du réseau Contoso-VNet-EU**

1. Dans le portail Azure Classic, cliquez sur **NOUVEAU**, **SERVICES RÉSEAU**, **RÉSEAU VIRTUEL**, **AJOUTER UN RÉSEAU LOCAL**.
2. Entrez :
   
   * **NOM** : Contoso-LNet-EU
   * **ADRESSE IP DU PÉRIPHÉRIQUE VPN** : 192.168.0.1 (cette adresse sera actualisée ultérieurement)
     
       En général, vous utilisez la véritable adresse IP externe d’un périphérique VPN. Pour les configurations de réseau virtuel à réseau virtuel, vous allez utiliser l’adresse IP de la passerelle VPN. Étant donné que vous n’avez pas encore créé les passerelles VPN pour les deux réseaux virtuels, entrez une adresse IP arbitraire que vous modifierez plus tard.
3. Entrez :
   
   * **ADRESSE IP DE DÉPART DE L'ESPACE D'ADRESSAGE** : 10.1.0.0
   * **CIDR DE L'ESPACE D'ADRESSAGE** : /16
   
   Ces valeurs doivent correspondre exactement à la plage spécifiée précédemment pour Contoso-VNet-EU.

**Pour créer un réseau local appelé Contoso-LNet-US correspondant à l'espace d'adressage du réseau Contoso-VNet-US**

* Répétez la procédure précédente avec les paramètres suivants :
  
  * **NOM** : Contoso-LNet-US
  * **ADRESSE IP DU PÉRIPHÉRIQUE VPN** : 192.168.0.1 (cette adresse sera actualisée ultérieurement)
  * **ADRESSE IP DE DÉPART DE L'ESPACE D'ADRESSAGE** : 10.2.0.0
  * **CIDR DE L'ESPACE D'ADRESSAGE** : /16

### Créer des passerelles VPN
Cette configuration se divise en deux parties. Tout d'abord, vous configurez une connexion de site à site d'un réseau virtuel à un réseau local, puis vous créez un VPN à routage dynamique. La connexion de réseau virtuel à réseau virtuel nécessite des passerelles VPN Azure avec des VPN à routage dynamique. Les VPN à routage statique Azure ne sont pas pris en charge.

**Pour configurer la connexion de site à site de Contoso-VNet-EU à Contoso-LNet-US**

1. Dans le portail Azure Classic, cliquez sur **RÉSEAUX** dans le volet gauche.
2. Cliquez sur **Contoso-VNet-EU**.
3. Cliquez sur l'onglet **CONFIGURER**.
4. Cochez la case **Se connecter au réseau local**.
5. Dans **RÉSEAU LOCAL**, sélectionnez **Contoso-LNet-US**.
6. Cliquez sur **Ajouter un sous-réseau de passerelle** dans la section des espaces d'adressage de réseau virtuel.
7. Cliquez sur **ENREGISTRER**.
8. Cliquez sur **OK** pour confirmer.

**Pour créer une passerelle VPN pour Contoso-VNet-EU**

1. Dans le portail Azure Classic, cliquez sur l'onglet **TABLEAU DE BORD**.
2. Cliquez sur **CRÉER UNE PASSERELLE** en bas de la page, puis sur **Routage dynamique**.
3. Cliquez sur **Oui** pour confirmer. Notez que le graphique de la passerelle sur la page devient jaune et indique Création d'une passerelle. La création d'une passerelle dure généralement environ 15 minutes.
   
   Quand l'état de la passerelle est Connexion en cours, l'adresse IP de chaque passerelle s'affiche dans le Tableau de bord. Notez l'adresse IP qui correspond à chaque réseau virtuel, en prenant soin de ne pas les mélanger. Vous utiliserez ces adresses IP pour remplacer les adresses IP d'espace réservé du périphérique VPN dans les réseaux locaux.
4. Copiez l'**ADRESSE IP DE LA PASSERELLE**. Vous allez l'utiliser pour configurer l'adresse IP de la passerelle VPN pour Contoso-VNet-EU dans la section suivante.

**Pour créer une passerelle VPN pour Contoso-VNet-EU**

* Répétez les deux dernières procédures pour configurer la connexion de site à site de Contoso-VNet-US à Contoso-LNet-EU et créer une passerelle VPN pour Contoso-VNet-US. Une fois terminé, vous aurez l'adresse IP de la passerelle VPN pour Contoso-VNet-US.

### Définir les adresses IP du périphérique VPN pour des réseaux locaux
Dans la dernière section, vous avez créé une passerelle VPN pour chacun des réseaux virtuels. Vous avez donc les adresses IP des passerelles VPN. Vous pouvez maintenant revenir à la configuration des adresses IP du périphérique VPN des réseaux locaux.

**Pour configurer l'adresse IP du périphérique VPN pour Contoso-LNet-EU**

1. Dans le portail Azure Classic, cliquez sur **RÉSEAUX** dans le volet gauche.
2. Cliquez sur **RÉSEAUX LOCAUX** en haut.
3. Cliquez sur **Contoso-LNet-EU**, puis sur **MODIFIER** en bas.
4. Mettez à jour l'**ADRESSE IP DU PÉRIPHÉRIQUE VPN**. C'est l'adresse située sous l'onglet TABLEAU DE BORD de Contoso-VNet-EU.
5. Cliquez avec le bouton droit.
6. Cliquez sur le bouton représentant une coche.

**Pour configurer l'adresse IP du périphérique VPN pour Contoso-LNet-US**

* Répétez la dernière procédure pour configurer l'adresse IP du périphérique VPN pour Contoso-LNet-US.

### Définir des clés de passerelle de réseau virtuel
Les passerelles de réseau virtuel utilisent une clé partagée pour authentifier les connexions entre les réseaux virtuels. La clé ne peut pas être configurée dans le portail Azure Classic. Vous devez utiliser PowerShell ou le Kit de développement logiciel (SDK) .NET.

**Pour définir les clés**

1. Dans votre station de travail, ouvrez **Windows PowerShell ISE** ou la console Windows PowerShell.
2. Mettez à jour les paramètres du script suivant et exécutez-le :
   
        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 

## Vérifier la connexion VPN
Si aucune machine virtuelle n'est déployée sur les réseaux virtuels, vous pouvez utiliser le schéma visuel des réseaux virtuels de la page Tableau de bord des réseaux virtuels sur le portail Azure Classic pour vérifier l'état de la connexion :

![État de la connexion VPN du réseau virtuel de la réplication HDInsight Hbase][img-vpn-status]

## Étapes suivantes
Dans ce didacticiel, vous avez vu comment configurer une connexion VPN entre deux réseaux virtuels Azure. Les deux autres articles de la série abordent les thèmes suivants :

* [Configurer des serveurs DNS entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-dns]
* [Configurer la géo-réplication HBase][hdinsight-hbase-geo-replication]

[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-DNS.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.LNet.diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.status.png

<!---HONumber=AcomDC_0914_2016-->