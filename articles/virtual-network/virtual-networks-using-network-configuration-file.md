<properties 
	pageTitle="Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau" 
	description="Instructions pour exporter et importer un fichier de configuration réseau pour le portail de gestion Azure afin de créer ou de modifier des réseaux virtuels. " 
	services="virtual-network" 
	documentationCenter="" 
	authors="jimdial" 
	manager="carmonm" 
	editor="tysonn"/>

<tags
	ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services" 
	ms.date="03/15/2016"
	ms.author="jdial"/>

# Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau

Vous pouvez configurer un réseau virtuel (VNet) à l'aide du portail de gestion Azure, ou à l'aide d'un fichier de configuration réseau.

## Création et modification d'un fichier de configuration réseau 
Pour créer un fichier de configuration réseau, le plus simple consiste à exporter les paramètres réseau de la configuration d'un réseau virtuel existant, puis à modifier ce fichier pour qu'il contienne les paramètres à configurer dans vos réseaux virtuels.

Pour modifier le fichier de configuration réseau, il vous suffit d'ouvrir le fichier, d'apporter les modifications appropriées et d’enregistrer le fichier. Vous pouvez utiliser n’importe quel éditeur *xml* pour apporter des modifications au fichier de configuration réseau.

Vous devez respecter les instructions relatives aux [paramètres de schéma du fichier de configuration réseau](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Azure considère tout sous-réseau qui comprend des éléments déployés comme étant **« en cours d'utilisation »**. Une fois le sous-réseau utilisé, il ne peut pas être modifié. Avant toute modification, déplacez tout ce que vous avez déployé sur le sous-réseau vers un autre sous-réseau qui n'est pas modifié. Consultez [Déplacer une machine virtuelle ou une instance de rôle vers un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md).

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

<!---HONumber=AcomDC_0810_2016-->