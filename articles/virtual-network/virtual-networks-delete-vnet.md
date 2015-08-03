<properties 
   pageTitle="Suppression d’un réseau virtuel"
   description="En savoir plus sur la suppression d’un réseau virtuel existant"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/29/2015"
   ms.author="telmos" />

# Suppression d’un réseau virtuel

Si vous voulez supprimer un réseau virtuel, vous ne pouvez pas simplement cliquer sur **Supprimer**. Vous devez d’abord effectuer certaines opérations :

1. **Enregistrer les paramètres (facultatif)** : si vous voulez enregistrer les paramètres du réseau virtuel dans un fichier local, exportez le fichier de configuration avant de supprimer votre réseau virtuel. Consultez [Exportation de paramètres de réseau virtuel dans un fichier de configuration réseau](https://msdn.microsoft.com/library/azure/dn133804.aspx) pour plus d’informations. L’enregistrement des paramètres vous permet de recréer le réseau virtuel plus tard, si nécessaire.

1. **Supprimer la passerelle de réseau virtuel** : si vous avez configuré une passerelle pour le réseau virtuel, vous devez la supprimer avant de supprimer le réseau virtuel. Pour supprimer la passerelle de réseau virtuel, accédez à la page Tableau de bord de votre réseau virtuel. En bas de la page, cliquez sur **Supprimer la passerelle**.
						
	Vous devrez peut-être patienter 5 à 10 minutes pendant la suppression de la passerelle avant de passer à l’étape suivante.

1. **Supprimer des services cloud, des sites web et des machines virtuelles** : si vous avez déployé des objets sur le réseau virtuel, vous devez les supprimer avant de pouvoir supprimer le réseau virtuel.

1. **Supprimer votre réseau virtuel** : cliquez à droite de la ligne *Nom* pour mettre en surbrillance le réseau virtuel que vous voulez supprimer, puis cliquez sur **Supprimer** en bas de la page. Suivez les instructions à l’écran.

1. **Facultatif** : vous pouvez également choisir de supprimer les paramètres de réseau local, les serveurs DNS et le groupe d’affinités après la suppression de votre réseau virtuel.
 

<!---HONumber=July15_HO4-->