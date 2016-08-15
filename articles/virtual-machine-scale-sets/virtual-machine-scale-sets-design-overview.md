<properties
	pageTitle="Conception de jeux de mise à l’échelle de machines virtuelles | Microsoft Azure"
	description="En savoir plus sur la conception de vos jeux de mise à l'échelle de machines virtuelles"
	keywords="machine virtuelle linux, jeux de mise à l’échelle de machine virtuelle" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="gatneil"/>

# Conception de jeux de mise à l’échelle de machine virtuelle pour la mise à l’échelle

Cette rubrique présente les considérations à prendre en compte pour créer des jeux de mise à l'échelle de machine virtuelle. Pour plus d'informations sur les jeux de mise à l'échelle de machine virtuelle, reportez-vous à la rubrique [Vue d’ensemble des jeux de mise à l’échelle de machine virtuelle](virtual-machine-scale-sets-overview.md).


## Storage

Un jeu de mise à l'échelle utilise des comptes de stockage pour stocker les disques du système d'exploitation des machines virtuelles dans le jeu. Nous recommandons un ratio maximum de 20 machines virtuelles par compte de stockage. Nous vous recommandons également de répartir dans l'alphabet les premiers caractères des noms de comptes de stockage. Cette méthode améliore la répartition sur les différents systèmes internes. Par exemple, dans le modèle suivant, nous utilisons la fonction de modèle ARM uniqueString pour générer des hachages de préfixe qui sont ajoutés aux noms de comptes de stockage : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## Sur-approvisionnement

À compter de la version d'API 2016-03-30, les jeux de mise à l'échelle de machines virtuelles sur-approvisionnent par défaut en machines virtuelles. Cela signifie que le jeu de mise à l'échelle activera davantage de machines virtuelles que vous n’en avez demandé, puis supprimera les machines virtuelles inutiles. Cela améliore le taux de réussite de l’approvisionnement car même si une machine virtuelle n’approvisionne pas correctement, la totalité du déploiement est considéré comme ayant « échoué » par Azure Resource Manager. Vous ne serez pas facturé pour ces machines virtuelles supplémentaires et elles ne seront pas comptabilisées dans vos limites de quotas.

Bien que cette méthode n'améliore pas les taux de réussite de l’approvisionnement, elle peut provoquer un comportement déroutant si une application n'est pas conçue pour gérer les machines virtuelles qui disparaissent sans avertissement. Pour désactiver le sur-approvisionnement, vérifiez que votre modèle contient la chaîne suivante : "overprovision": "false"

Si vous désactivez le sur-approvisionnement, vous pouvez obtenir un ratio plus important de machines virtuelles par compte de stockage, mais nous déconseillons d’aller au-delà de 40.


## Limites
Un groupe identique basé sur une image personnalisée (créée par vous-même) doit créer tous les disques durs virtuels du système d’exploitation dans un même compte de stockage. Par conséquent, le nombre maximal recommandé de machines virtuelles dans un groupe identique basé sur une image personnalisée est de 20. Si vous désactivez le sur-approvisionnement, vous pouvez aller jusqu’à 40.

Un groupe identique basé sur une image de plateforme est actuellement limité à 100 machines virtuelles (nous vous recommandons d’utiliser 5 comptes de stockage pour cette échelle).

Si vous souhaitez avoir plus de machines virtuelles que ne l’autorisent ces limites, vous devez déployer plusieurs groupes identiques. [Pour obtenir un exemple de la procédure à suivre, consultez ce modèle.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)

<!---HONumber=AcomDC_0803_2016-->