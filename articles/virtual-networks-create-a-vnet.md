<properties 
   pageTitle="Créer un réseau virtuel" 
   description="Parcourez les étapes permettant de créer facilement un réseau virtuel de base." 
   services="virtual-network" 
   documentationCenter="" 
   authors="cherylmc" 
   manager="adinah" 
   editor="tysonn"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/20/2015"
   ms.author="cherylmc"/>

# Créer un réseau virtuel 



Quand vous créez un réseau virtuel, les services et les machines virtuelles présents dans le réseau virtuel peuvent communiquer entre eux en toute sécurité sans passer par Internet. Créer un réseau virtuel cloud dédié est un processus relativement simple et rapide. Comme les réseaux virtuels cloud ne sont pas faits pour établir une connexion entre différents locaux, vous n'avez pas besoin d'acquérir ni de configurer un périphérique VPN ou des certificats d'authentification. 

Une fois que vous avez créé un réseau virtuel, vous pouvez lui ajouter de nouvelles machines virtuelles et instances PaaS. À noter que si vous utilisez le portail de gestion pour créer des machines virtuelles, veillez à sélectionner **À partir de la galerie** pour pouvoir spécifier le réseau virtuel. Il s'agit d'un point important, car vous ne pourrez pas revenir en arrière et placer une machine virtuelle dans un réseau virtuel après l'avoir créée.

[Azure Note] **Utilisez cette procédure pour créer un réseau virtuel cloud dédié.** La création d'une configuration entre différents locaux étant plus complexe, évitez d'utiliser cette procédure pour créer un réseau virtuel destiné à être par la suite connecté à votre réseau local. Si vous voulez créer une connexion intersite sécurisée entre Azure et votre réseau local, consultez [À propos de la connectivité sécurisée entre sites](https://msdn.microsoft.com/library/azure/dn133798.aspx).

## <a name="CreateyourVNet">Création de votre réseau virtuel</a>

1. Connectez-vous au **portail de gestion**.
2. En bas à gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Création personnalisée** pour lancer l'Assistant Configuration.
3. Dans la page **Détails du réseau virtuel**, entrez les informations suivantes, puis cliquez sur la flèche Suivant en bas à droite. Pour plus d'informations sur les paramètres de la page Détails, consultez la section **Détails du réseau virtuel** de la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	-  **Nom :** nommez votre réseau virtuel. Sachant que vous utiliserez ce nom de réseau virtuel au moment de déployer vos machines virtuelles et services, il est préférable de ne pas choisir un nom trop compliqué.

	-  **Emplacement :** dans la liste déroulante, sélectionnez la région souhaitée. Votre réseau virtuel sera créé dans le centre de données Azure situé dans la région spécifiée.



4. Dans la page **Serveurs DNS et connectivité VPN**, n'apportez aucune modification. Passez simplement à la page suivante en cliquant sur la flèche. Par défaut, Azure assure une résolution de noms de base pour votre réseau virtuel. Il est possible que vos besoins en matière de résolution de noms soient trop complexes au regard des capacités de traitement de la résolution de noms de base d'Azure. Dans ce cas, vous pouvez par la suite ajouter une machine virtuelle qui exécute DNS sur votre réseau virtuel. Pour plus d'informations sur la résolution de noms Azure et DNS, consultez [Résolution de noms](https://msdn.microsoft.com/library/azure/jj156088.aspx). 
5. C'est dans la page **Espaces d'adresses du réseau virtuel** que vous indiquez l'espace d'adresses que vous voulez utiliser pour ce réseau virtuel. À moins que vous ayez besoin d'une certaine plage d'adresses IP internes pour vos machines virtuelles ou que vous vouliez créer un sous-réseau spécifique pour les machines virtuelles destinées à recevoir une adresse DIP statique, vous n'avez pas besoin d'apporter de modifications dans cette page. Si vous voulez créer plusieurs sous-réseaux, vous pouvez le faire dans cette page en cliquant sur **ajouter un sous-réseau**. Pour plus d'informations sur les paramètres de la page Détails, consultez la section **Détails du réseau virtuel** de la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](https://msdn.microsoft.com/library/azure/jj156074.aspx).

	-  Pour plus d'informations sur les paramètres de la page Détails, consultez la section **Détails du réseau virtuel** de la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	-  Sachant que vous n'allez pas connecter ce réseau privé virtuel à votre réseau local en utilisant une configuration VPN entre différents locaux, vous n'aurez pas à coordonner ces paramètres avec les plages d'adresses IP existants de votre réseau local. Si vous pensez être amené par la suite à créer une configuration entre différents locaux, vous devez coordonner les espaces d'adresses maintenant avec les plages qui existent déjà sur votre site local pour éviter des problèmes de routage. Une modification ultérieure des plages peut s'avérer quelque peu compliquée et vous risquez très probablement de devoir redéployer votre


6. Cliquez sur la coche située en bas à droite de la page Espaces d'adresses du réseau virtuel pour lancer la création de votre réseau virtuel. Après la création de votre réseau virtuel, le statut Créé apparaît sous Statut sur la page des réseaux dans le portail de gestion.
7. Une fois que votre réseau virtuel est créé, vous pouvez le déployer sur votre réseau virtuel. Par exemple, si vous voulez déployer une machine virtuelle sur votre réseau virtuel, consultez [Ajouter une machine virtuelle à un réseau virtuel](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/). Pour pouvoir sélectionner votre réseau virtuel, veillez à sélectionner **À partir de la galerie** au moment de créer une machine virtuelle. À noter que si vous avez déjà déployé des machines virtuelles et des instances PaaS existantes, il est tout simplement impossible de les déplacer vers votre nouveau réseau virtuel. Cela est dû au fait que leurs paramètres de configuration réseau ont été configurés pendant le déploiement. Vous devrez les redéployer vers le nouveau réseau virtuel.



## Étapes suivantes
-  [Vue d'ensemble technique du réseau virtuel](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

 
-  [Ajouter une machine virtuelle à un réseau virtuel](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [FAQ Virtual Network](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configuration d'un réseau virtuel à l'aide de fichiers de configuration réseau](http://azure.microsoft.com/documentation/articles/virtual-networks-using-network-configuration-file/)

-  [Vue d'ensemble de la résolution de noms Azure](http://go.microsoft.com/fwlink/?LinkId=248097)
 



<!--HONumber=47-->
