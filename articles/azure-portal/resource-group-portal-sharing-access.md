<properties
	pageTitle="Partage et contrôle d’accès pour les tableaux de bord Azure | Microsoft Azure"
	description="Apprenez à gérer le partage et le contrôle d’accès pour les tableaux de bord Azure."
	services="azure-resource-manager,azure-portal"
    keywords="portail,partage,accès"
	documentationCenter=""
	authors="adamabdelhamed"
	manager="dend"
	editor="dend"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="adamab"/>
    
# Partage et contrôle d’accès pour les tableaux de bord Azure

L’accès aux informations affichées par la plupart des mosaïques dans le portail est régi par le [contrôle d’accès en fonction du rôle](./active-directory/role-based-access-control-configure.md) (RBAC) Azure. Pour intégrer des tableaux de bord en toute transparence dans l’écosystème, tous les tableaux de bord publiés sont implémentés en tant que ressources Azure. Du point de vue du contrôle d’accès, les tableaux de bord sont traités de la même manière qu’une machine virtuelle ou un compte de stockage.

Voici un exemple. Supposons que vous disposez d’un abonnement Azure et que les différents membres de votre équipe ont reçu les rôles de **propriétaire**, **contributeur** ou **lecteur** de l’abonnement. Les utilisateurs associés au rôle de propriétaire ou de contributeur seront en mesure de répertorier, afficher, créer, modifier ou supprimer des tableaux de bord dans l’abonnement. Les utilisateurs ayant le rôle de lecteur pourront répertorier et afficher des tableaux de bord, sans les modifier ou les supprimer. Les utilisateurs ayant un accès en lecture seront en mesure d’apporter des modifications locales à un tableau de bord publié (par exemple, lors de la résolution d’un problème), mais n’auront pas la possibilité de republier ces modifications sur le serveur. Ils pourront effectuer une copie privée du tableau de bord pour leur propre usage.

Notez que les vignettes individuelles du tableau de bord appliquent leurs propres exigences en matière de contrôle d’accès, compte tenu des ressources dont ils affichent les données. Autrement dit, vous pouvez concevoir un tableau de bord que vous pouvez partager à plus grande échelle tout en protégeant les données contenues dans chaque vignette.

<!---HONumber=AcomDC_0622_2016-->