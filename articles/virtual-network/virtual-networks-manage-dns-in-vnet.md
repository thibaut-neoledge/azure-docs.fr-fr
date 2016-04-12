<properties 
   pageTitle="Gestion des serveurs DNS utilisés par un réseau virtuel"
   description="En savoir plus sur l’ajout et la suppression de serveurs DNS dans un réseau virtuel"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos" />

# Gestion des serveurs DNS utilisés par un réseau virtuel

Vous pouvez gérer la liste des serveurs DNS utilisés dans un réseau virtuel à partir du portail de gestion ou du fichier de configuration réseau. Vous pouvez ajouter jusqu’à 12 serveurs DNS pour chaque réseau virtuel. Quand vous spécifiez des serveurs DNS, assurez-vous de les indiquer dans l’ordre approprié pour votre environnement. Les listes de serveurs DNS ne fonctionnent pas sur le modèle du tourniquet (round-robin). Elles sont utilisées dans l’ordre où elles sont spécifiées. Si le premier serveur DNS sur la liste est accessible, le client utilise ce serveur DNS, que le serveur DNS fonctionne correctement ou non. Pour modifier l’ordre des serveurs DNS de votre réseau virtuel, supprimez-les de la liste et rajoutez-les dans l’ordre souhaité.

>[AZURE.WARNING] Une fois la liste DNS mise à jour, vous devez redémarrer les machines virtuelles de votre réseau virtuel pour qu’elles adoptent les nouveaux paramètres de serveur DNS. Les machines virtuelles continueront à utiliser la configuration actuelle jusqu’à ce qu’elles soient redémarrées.

## Modifier une liste de serveurs DNS pour un réseau virtuel à l’aide du portail de gestion

1. Connectez-vous au **portail de gestion**.

1. Dans le volet de navigation, cliquez sur **Réseaux**, puis sur le nom de votre réseau virtuel dans la colonne **Nom**.

1. Cliquez sur **Configurer**.

1. Dans **Serveurs DNS**, vous pouvez configurer les éléments suivants :

	- **Pour enregistrer (ajouter) un nouveau serveur DNS** : tapez simplement le nom et l’adresse IP dans les zones. Un serveur DNS est ajouté à votre liste de serveurs DNS du réseau virtuel et est enregistré auprès d’Azure.

	- **Pour ajouter un serveur DNS précédemment enregistré** : si vous avez déjà enregistré un serveur DNS dans Azure, vous pouvez le sélectionner dans la liste préremplie.

	- **Pour supprimer un serveur DNS de votre réseau virtuel** : cliquez sur le X à côté du serveur à supprimer. Notez que cela supprime uniquement le serveur de cette liste de réseaux virtuels. Le serveur DNS reste enregistré dans Azure pour être utilisé par vos autres réseaux virtuels. Pour supprimer un serveur DNS de votre abonnement, accédez à la page **Réseaux -> Serveurs DNS**.

	- **Pour réordonner les serveurs DNS** : supprimez tous les serveurs DNS répertoriés, puis rajoutez-les dans l’ordre souhaité. N’oubliez pas qu’il ne s’agit pas d’une liste DNS de type tourniquet (round-robin).

	- **Pour renommer un serveur DNS** : sélectionner le serveur DNS dans la liste, puis tapez le nouveau nom. Un nouveau serveur DNS est alors enregistré dans Azure et ajouté à la liste des serveurs DNS de votre réseau virtuel. L’ancien serveur DNS et son adresse IP restent enregistrés dans Azure. Vous pouvez le supprimer sur la page **Serveurs DNS**, si vous ne l’utilisez pas pour un autre réseau virtuel.

1. Cliquez sur **Enregistrer** en bas de la page pour enregistrer votre nouvelle configuration de serveurs DNS.

1. Redémarrez les machines virtuelles situées sur le réseau virtuel pour leur permettre d’acquérir les nouveaux paramètres DNS.

## Modifier une liste de serveurs DNS à l’aide d’un fichier de configuration réseau

Pour modifier une liste de serveurs DNS à l’aide d’un fichier de configuration réseau, vous devez d’abord exporter vos paramètres de configuration à partir du portail de gestion. Ensuite, vous modifiez le fichier de configuration réseau et le réimportez via le portail de gestion. Voici une liste globale des étapes de ce processus.

1. Exportez vos paramètres de réseau virtuel dans un fichier de configuration réseau. Pour plus d’informations et pour connaître les étapes d’exportation de vos paramètres de configuration réseau, consultez [Exportation de paramètres du réseau virtuel dans un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Spécifiez les informations du serveur DNS pour votre réseau virtuel. Pour plus d’informations sur la spécification d’un serveur DNS, consultez [Spécification de paramètres DNS dans un fichier de configuration de réseau virtuel](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Pour plus d’informations sur les fichiers de configuration réseau, consultez [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) et [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Importez le fichier de configuration réseau. Pour plus d’informations et pour connaître les étapes d’importation de votre fichier de configuration réseau, consultez [Importation de fichier de configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Redémarrez les machines virtuelles situées sur le réseau virtuel pour leur permettre d’acquérir les nouveaux paramètres DNS.

## Étapes suivantes

[Gestion des propriétés du réseau virtuel](../virtual-networks-settings)

[Utilisation d’adresses IP publiques dans un réseau virtuel](../virtual-networks-public-ip-within-vnet)

[Suppression d’un réseau virtuel](../virtual-networks-delete-vnet)

<!---HONumber=AcomDC_0323_2016-->