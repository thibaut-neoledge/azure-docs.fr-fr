<properties
   pageTitle="Créer une identité professionnelle ou scolaire dans AAD | Microsoft Azure"
   description="Apprenez à créer une identité professionnelle ou scolaire dans Azure Active Directory afin de l’utiliser avec vos machines virtuelles Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# Création d’une identité professionnelle ou scolaire dans Azure Active Directory afin de l’utiliser avec des machines virtuelles Linux

Si vous avez créé un compte Azure personnel ou si vous disposez d’un abonnement MSDN personnel et avez créé le compte Azure pour profiter des crédits Azure MSDN, cela signifie que vous avez utilisé une identité de *compte Microsoft* pour le créer. Pour fonctionner correctement, de nombreuses fonctionnalités d'Azure, parmi lesquelles les [modèles de groupes de ressources](../resource-group-overview.md), nécessitent un compte professionnel ou scolaire (une identité gérée par Azure Active Directory). Vous pouvez suivre les instructions ci-dessous pour créer un compte professionnel ou scolaire car, heureusement, l’un des atouts de votre compte personnel Azure est qu'il est fourni avec un domaine Azure Active Directory par défaut que vous pouvez utiliser pour créer un nouveau compte professionnel ou scolaire à utiliser avec les fonctionnalités Azure qui le nécessitent.

Toutefois, de récentes modifications permettent de gérer votre abonnement avec n’importe quel type de compte Azure grâce à la `azure login`méthode de connexion interactive décrite [ici](../xplat-cli-connect.md). Vous pouvez utiliser ce mécanisme ou suivre les instructions ci-dessous. Vous pouvez également [créer une identité professionnelle ou scolaire dans Azure Active Directory afin de l’utiliser avec des machines virtuelles Windows](virtual-machines-windows-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]

<!---HONumber=AcomDC_0824_2016-->