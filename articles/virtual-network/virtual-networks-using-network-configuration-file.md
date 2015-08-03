<properties 
	pageTitle="Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau" 
	description="Instructions pour exporter et importer un fichier de configuration réseau pour le portail de gestion Azure afin de créer ou de modifier des réseaux virtuels." 
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
	ms.date="07/09/2015"
	ms.author="cherylmc"/>

# Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau

## Vue d'ensemble

Pour configurer votre réseau virtuel, vous pouvez utiliser l'Assistant du portail de gestion ou créer et importer un fichier de configuration réseau. Azure utilise le fichier de configuration de réseau pour définir les paramètres de votre réseau virtuel.

Vous préférerez peut-être utiliser l'Assistant du Portail de gestion pour créer initialement votre configuration de réseau virtuel, puis apporter des modifications directement dans le fichier de configuration. Par exemple, si vous configurez plusieurs réseaux virtuels pour des abonnements distincts, vous souhaiterez peut-être d'abord créer un fichier de configuration de réseau à l'aide de l'Assistant du Portail de gestion. Vous pouvez ensuite exporter le fichier à utiliser comme modèle, le modifier pour spécifier différents paramètres, puis le réimporter dans le Portail de gestion. Cela peut être un moyen efficace de créer plusieurs réseaux virtuels lorsque vous avez plusieurs abonnements.

Si vous voulez apporter des modifications à vos paramètres de configuration de réseau avant de déployer des services cloud ou des machines virtuelles sur le réseau, vous pouvez exporter le fichier, le modifier, puis le réimporter dans Azure. Vous pouvez également utiliser un fichier de configuration réseau pour sauvegarder les paramètres de configuration de votre réseau si vous souhaitez recréer votre réseau virtuel.

## Création et modification d'un fichier de configuration réseau 
Pour créer un fichier de configuration réseau, le plus simple consiste à exporter les paramètres réseau de la configuration d'un réseau virtuel existant, puis à modifier ce fichier pour qu'il contienne les paramètres à configurer dans vos réseaux virtuels. Vous pouvez également récupérer un exemple de fichier et le modifier.

Pour modifier le fichier de configuration réseau, il vous suffit d'ouvrir le fichier, d'apporter les modifications appropriées et de les enregistrer. Vous pouvez utiliser n’importe quel éditeur *xml* pour apporter des modifications au fichier de configuration réseau.

Vous devez respecter les instructions relatives aux paramètres de schéma du fichier de configuration réseau. Lorsque vous créez votre fichier de configuration réseau, les paramètres du fichier remplacent les paramètres que vous avez actuellement pour cet abonnement dans Azure. Si vous apportez des modifications à des valeurs du fichier qui ne sont pas cohérentes avec les instructions sur les paramètres, votre réseau virtuel risque de ne pas être configuré comme vous l'avez prévu. Dans certains cas, Azure peut même vous empêcher d'importer le fichier. Pour plus d’informations sur les paramètres spécifiques contenus dans un fichier de configuration réseau, consultez [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Azure considère tout sous-réseau qui comprend des éléments déployés comme étant « en cours d'utilisation ». Une fois le sous-réseau utilisé, il ne peut pas être modifié. Avant toute modification, déplacez tout ce que vous avez déployé sur le sous-réseau vers un autre sous-réseau qui n'est pas modifié. Consultez [Déplacer une machine virtuelle ou une instance de rôle vers un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md).



## Exportation et importation de paramètres de réseau virtuel avec le portail de gestion  
Vous pouvez importer les paramètres d’exportation de configuration réseau contenus dans votre fichier de configuration réseau à l’aide de PowerShell ou du portail de gestion. Les instructions ci-dessous vous aideront à exporter et importer ces paramètres à l'aide du portail de gestion.

### Pour exporter vos paramètres réseau
Lorsque effectuez une exportation, tous les paramètres des réseaux virtuels de votre abonnement sont écrits dans un fichier .xml.

1. Connectez-vous au **portail de gestion**.
2. Dans le portail de gestion, en bas de la page **réseaux**, cliquez sur **Exporter**. 
3. Dans la fenêtre **Exporter la configuration de réseau**, vérifiez que vous avez sélectionné l’abonnement pour lequel vous voulez exporter vos paramètres réseau. Ensuite, cliquez sur la coche dans la partie inférieure droite. 
4. Quand vous y êtes invité, enregistrez le fichier *NetworkConfig.xml* à l’emplacement de votre choix.


### Pour importer vos paramètres réseau

1. Dans le **portail de gestion**, dans le volet de navigation en bas à gauche, cliquez sur **Nouveau**.
2. Cliquez sur **Services réseau** -> **Réseau virtuel** -> **Importer la configuration**.
3. Dans la page **Importer le fichier de configuration de réseau**, accédez à votre fichier de configuration réseau, puis cliquez sur la flèche **Suivant**.
4. Dans la page **Création de votre réseau**, vous verrez des informations à l’écran présentant les sections de votre configuration réseau qui vont être créées ou modifiées. Si les modifications vous paraissent correctes, cliquez sur la coche pour passer à la mise à jour ou à la création de votre réseau virtuel. 


## Étapes suivantes
Pour plus d’informations sur les paamètres de réseaux virtuels, consultez :

-  [Schéma de configuration réseau - Paramètres facultatifs pour les services cloud](https://msdn.microsoft.com/library/azure/jj156091.aspx)
-  [À propos des paramètres de réseau virtuel dans le Portail de gestion](https://msdn.microsoft.com/library/azure/jj156074.aspx)
-  [FAQ sur le réseau virtuel](https://msdn.microsoft.com/library/azure/dn133803.aspx)





 

<!---HONumber=July15_HO4-->