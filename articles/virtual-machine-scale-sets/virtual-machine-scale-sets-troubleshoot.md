<properties
	pageTitle="Dépannage de la mise à l’échelle automatique à l’aide des jeux de mise à l’échelle de machines virtuelles | Microsoft Azure"
	description="Dépanner la mise à l’échelle automatique avec des jeux de mise à l’échelle de machine virtuelle Découvrez les problèmes couramment rencontrés et apprenez à les résoudre."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="guybo"/>
    
# Dépannage de la mise à l’échelle automatique avec des jeux de mise à l’échelle de machine virtuelle

**Problème** : vous avez créé une infrastructure de mise à l’échelle automatique dans Azure Resource Manager à l’aide de jeux de mise à l’échelle de machine virtuelle (par exemple en déployant un modèle comme https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale), vos règles de mise à l’échelle sont définies et fonctionnent très bien, sauf que, quelle que soit la charge placée sur les machines virtuelles, elle n’est pas mise à l’échelle automatiquement.

## Étapes de dépannage

Parmi les éléments à prendre en considération :

- De combien de cœurs chaque machine virtuelle dispose-t-elle et chargez-vous chaque cœur ? L’exemple de modèle Azure QuickStart ci-dessus a un script do\_work.php, qui charge un seul cœur. Si vous utilisez une machine virtuelle plus volumineuse que Standard\_A1, vous devez exécuter cette charge plusieurs fois. Vérifiez le nombre de cœurs de vos machines virtuelles en consultant [Tailles des machines virtuelles Windows dans Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- Combien de machines virtuelles le jeu de mise à l’échelle de machines virtuelles inclut-il, travaillez-vous sur chacun d’eux ?

    La taille des instances augmentera uniquement lorsque l’utilisation moyenne du processeur sur **toutes** les machines virtuelles d’un jeu de mise à l’échelle dépassera la valeur seuil, sur la période définie dans les règles de mise à l’échelle automatique.

- Vous avez manqué un événement de mise à l’échelle ?

    Consultez les journaux d’audit dans le portail Azure pour les événements de mise à l’échelle. Peut-être qu’une mise à l’échelle vers le haut ou vers le bas a été ignorée. Vous pouvez filtrer sur « Mise à l’échelle ».

	![Journaux d’audit][audit]

- Les seuils d’augmentation et de diminution de la taille des instances sont-ils suffisamment différents ?

    Supposons que vous définissez une règle d’augmentation de la taille des instances lorsque l’utilisation moyenne du processeur est supérieure à 50 % pendant 5 minutes et une règle de diminution de la taille des instances lorsque l’utilisation moyenne du processeur est inférieure à 50 %. Cela entraînera un problème d’oscillation lorsque l’utilisation du processeur est proche de ce seuil, avec des actions de mise à l’échelle entraînant constamment l’augmentation et la diminution de la taille du jeu. De ce fait, le service de mise à l’échelle automatique tente d’empêcher l’oscillation, ce qui peut se manifester par une absence de mise à l’échelle. Par conséquent, assurez-vous que les seuils d’augmentation et de diminution de la taille des instances sont suffisamment différents pour laisser une marge lors de la mise à l’échelle.

- Avez-vous écrit votre propre modèle JSON ?

    Il est facile de commettre des erreurs. Commencez avec un modèle comme celui ci-dessus, dont l’efficacité a été prouvée, et apportez de petites modifications incrémentielles. Le modèle doit mettre en corrélation un compte de stockage d’extension de diagnostic, le jeu de mise à l’échelle et la ressource Microsoft.Insights, et référencer correctement le nom de la mesure des données de performance, qui diffère entre Windows et Linux.

- Pouvez vous procéder manuellement à une augmentation ou à une diminution de la taille des instances ?

    Essayez de redéployer la ressource de jeu de mise à l’échelle de machine virtuelle avec un paramètre de capacité différent pour modifier le nombre de machines virtuelles manuellement. Vous trouverez un exemple de modèle ici : https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing (vous devrez peut-être modifier le modèle pour vous assurer que la taille de machine est identique à celle de votre jeu de mise à l’échelle). Si vous pouvez modifier le nombre de machines virtuelles manuellement, vous savez que le problème est lié à la mise à l’échelle automatique.

- Consultez vos ressources Microsoft.Compute/virtualMachineScaleSet et Microsoft.Insights dans [l’explorateur de ressources Azure](https://resources.azure.com/)

    Il s’agit d’un outil de dépannage indispensable qui vous indique l’état de vos ressources Azure Resource Manager. Cliquez sur votre abonnement et examinez le groupe de ressources sur lequel vous effectuez un dépannage. Sous le fournisseur de ressources de calcul, consultez le jeu de mise à l’échelle de machine virtuelle que vous avez créé et consultez la vue d’instance, qui vous indique l’état d’un déploiement. Vérifiez également la vue d’instance des machines virtuelles dans le jeu de mise à l’échelle de machine virtuelle. Ensuite, accédez au fournisseur de ressources Microsoft.Insights et vérifiez que les règles de mise à l’échelle automatique sont correctes.

- L’extension de diagnostic fonctionne-t-elle et génère-t-elle des données de performance ?
 
    Dans Azure Resource Manager, la mise à l’échelle automatique fonctionne au moyen d’une extension de machine virtuelle appelée l’extension de diagnostic (divisée en une extension de diagnostic Linux et une extension Windows). Elle envoie des données de performance à un compte de stockage que vous définissez dans le modèle. Ces données sont ensuite regroupées par le service Azure Insights.

    Si le service Insights ne peut pas lire les données fournies par les machines virtuelles, il doit vous envoyer un courrier électronique. Par exemple, si les machines virtuelles étaient en panne, consultez votre messagerie (celle indiquée lors de la création du compte Azure).

    Vous pouvez également rechercher vous-même les données. Examinez le compte de stockage Azure à l’aide d’un explorateur de cloud. Par exemple avec [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), connectez-vous et choisissez l’abonnement Azure que vous utilisez et le nom de compte de stockage de diagnostics référencé dans la définition de l’extension de diagnostic de votre modèle de déploiement.

	![Cloud Explorer][explorer]

    Vous y trouverez un ensemble de tables où sont stockées les données de chaque machine virtuelle. Prenons Linux et la mesure de processeur comme exemple. Examinez les lignes les plus récentes. L’explorateur de cloud Visual Studio prend en charge un langage de requête. Vous pouvez donc exécuter une requête telle que « Timestamp gt datetime’2016-02-02T21:20:00Z’ » pour être sûr d’obtenir les événements les plus récents (en supposant que l’heure est au format UTC). Les données qui figurent ici correspondent-elles aux règles de mise à l’échelle que vous avez configurées ? Dans l’exemple ci-dessous, l’utilisation du processeur de la machine 20 a commencé à augmenter jusqu’à 100 % au cours des 5 dernières minutes.

	![Tables de stockage][tables]

    Si les données ne sont pas visibles, cela implique que le problème provient de l’extension de diagnostic en cours d’exécution sur les machines virtuelles. Si les données sont présentes, cela implique un problème lié à vos règles de mise à l’échelle ou au service Insights. Vérifiez le [statut Azure](https://azure.microsoft.com/status/).

    Une fois que vous avez suivi ces étapes, vous pouvez chercher de l’aide sur les forums [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp) ou sur [Stack Overflow](http://stackoverflow.com/questions/tagged/azure), ou contacter le support par téléphone. Soyez prêt à partager le modèle et une vue des données de performance.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

<!---HONumber=AcomDC_0427_2016-->