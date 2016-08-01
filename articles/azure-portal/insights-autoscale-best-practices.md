<properties
	pageTitle="Azure Insights : Meilleures pratiques relatives à la mise à l’échelle automatique d’Azure Insights. | Microsoft Azure"
	description="Découvrez les principes pour utiliser efficacement la mise à l’échelle automatique dans Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="ashwink"/>

# Meilleures pratiques pour la mise à l’échelle automatique d’Azure Insights

Les sections suivantes de ce document vous permettront de comprendre les meilleures pratiques pour la mise à l’échelle automatique dans Azure Insights. Après avoir consulté ces informations, vous pourrez utiliser la mise à l’échelle automatique dans votre infrastructure Azure plus efficacement.

## Concepts de la mise à l’échelle automatique

- Une ressource ne peut avoir qu’*un* paramètre de mise à l’échelle automatique
- Un paramètre de mise à l’échelle automatique peut comporter un ou plusieurs profils et chaque profil peut avoir une ou plusieurs règles de mise à l’échelle automatique.
- Un paramètre de mise à l’échelle automatique met à l’échelle les instances horizontalement, c’est-à-dire vers l’*extérieur* en augmentant la taille des instances et vers l’*intérieur* en diminuant la taille des instances. Un paramètre de mise à l’échelle automatique a une valeur d’instances maximum, minimum et par défaut.
- Une tâche de mise à l’échelle automatique lit toujours les mesures associées pour la mise à l’échelle, afin de vérifier si le seuil configuré pour l’augmentation ou la diminution de la taille des instances a été dépassé. Vous pouvez afficher une liste des mesures pour la mise à l’échelle automatique dans la rubrique [Mesures courantes de mise à l’échelle automatique Azure Insights](insights-autoscale-common-metrics.md).
- Tous les seuils sont calculés au niveau de l’instance. Par exemple, « l’augmentation de la taille des instances d’une instance lorsque la moyenne du processeur est supérieure à 80 % quand le nombre d’instances est égal à 2 » signifie l’augmentation de la taille des instances lorsque la moyenne du processeur sur toutes les instances est supérieure à 80 %.
- Vous recevez toujours les notifications d’erreur par e-mail. Plus précisément, le propriétaire, le collaborateur et les lecteurs de la ressource cible recevront un e-mail. Vous recevrez également un e-mail de *récupération* lorsque la mise à l’échelle automatique se rétablit après une défaillance et fonctionne à nouveau normalement.
- Vous pouvez choisir de recevoir une notification d’action réussie de mise à l’échelle par e-mail et webhooks.

## Meilleures pratiques relatives à la mise à l’échelle automatique

Utilisez les meilleures pratiques suivantes lorsque vous utilisez la mise à l’échelle automatique.

### Vérifiez que les valeurs minimales et maximales sont différentes et séparées par une marge suffisante.
Si le paramètre a une valeur maximum = 2, une valeur minimum = 2 et que nombre d’instances actuel est égal à 2, aucune action de mise à l’échelle ne peut se produire. Le paramètre recommandé consiste à conserver une marge suffisante entre les nombres d’instances minimum et maximum. La mise à l’échelle automatique est toujours appliquée entre ces limites, qui sont incluses. Toutefois, supposons que vous décidiez de mettre à l’échelle manuellement (mise à jour) le nombre d’instances sur une valeur supérieure à la valeur maximum. Lors de la prochaine exécution d’une tâche de mise à l’échelle automatique, celle-ci vérifie si le nombre d’instances actuel est supérieur à la valeur maximum. Dans ce cas, elle diminue la taille des instances pour correspondre à la valeur maximum, quel que soit le seuil défini sur les règles. De même, si vous arrivez manuellement à un nombre d’instances actuel inférieur à la valeur minimum, lors de la prochaine exécution d’une tâche de mise à l’échelle automatique, celle-ci augmente la taille des instances pour correspondre au nombre minimum d’instances.

### Utilisez toujours une combinaison de règle d’augmentation et de diminution de la taille des instances qui exécute une augmentation et une diminution.

Si vous n’utilisez qu’une partie de la combinaison, la mise à l’échelle automatique augmentera ou diminuera la taille des instances uniquement pour cette partie jusqu’à ce que la valeur maximum ou minimum soit atteinte.

### Ne basculez pas entre le portail Azure et le portail Azure Classic lors de la gestion de la mise à l’échelle automatique.
Pour Cloud Services et App Services (Web Apps), utilisez le portail Azure (portal.azure.com) pour créer et gérer les paramètres de mise à l’échelle automatique. Pour les jeux de mise à l’échelle de machine virtuelle, utilisez PoSH, l’interface de ligne de commande (CLI) ou l’API REST pour créer et gérer les paramètres de mise à l’échelle automatique. Ne basculez pas entre le portail Azure Classic (manage.windowsazure.com) et le portail Azure (portal.azure.com) lors de la gestion des configurations de mise à l’échelle automatique. Le portail Azure Classic et son serveur principal sous-jacent présentent des limitations. Accédez au portail Azure pour gérer la mise à l’échelle automatique à l’aide d’une interface utilisateur graphique. Les options disponibles sont : Autoscale PowerShell, l’interface de ligne de commande (CLI) ou l’API REST (via Azure Resource Explorer).

### Sélection de la statistique appropriée pour votre mesure de diagnostic
Pour les mesures de diagnostics, vous pouvez choisir entre *Moyen*, *Minimum*, *Maximum* et *Total* comme mesure de mise à l’échelle. La statistique la plus courante est *Moyen*.

### Sélection des seuils pour tous les types de mesure
Nous vous recommandons de choisir avec soin des seuils différents pour l’augmentation de la taille des instances et la diminution de la taille des instances en fonction des pratiques.

Nous vous *déconseillons* de choisir des paramètres de mise à l’échelle tels que les exemples ci-dessous, avec des valeurs de seuil très similaires ou identiques pour l’augmentation et la diminution de la taille des instances :

- Augmenter les instances de 1 lorsque le nombre de threads <= 600
- Diminuer les instances de 1 lorsque le nombre de threads >= 600


Examinons un exemple de ce qui peut entraîner un comportement qui peut sembler déroutant. Supposons qu’il existe 2 instances pour commencer et ensuite, le nombre moyen de threads par instance atteint 625. La mise à l’échelle automatique augmente la taille des instances en ajoutant une 3ème instance. Ensuite, supposons que le nombre de threads moyen entre les instances diminue pour atteindre 575. Avant la descente en puissance, la mise à l’échelle automatique essaye d’estimer quel sera l’état final en cas de diminution de la taille des instances. Par exemple, 575 x 3 (nombre d’instances actuel) = 1 725 / 2 (nombre final d’instances lors de la descente en puissance) = 862,5 threads. Cela signifie que la mise à l’échelle automatique devra immédiatement augmenter la taille des instances même après la diminution des instances, si le nombre moyen de threads reste le même ou même baisse d’une petite quantité. Toutefois, en cas de nouvelle montée en puissance, l’ensemble du processus se répète, menant à une boucle infinie. Pour éviter ce *problème*, la mise à l’échelle automatique ne descend pas du tout en puissance. Au lieu de cela, elle ignore et réévalue la condition lors de la prochaine exécution de la tâche du service. Cela peut perturber de nombreuses personnes, car la mise à l’échelle automatique semble ne pas fonctionner lorsque le nombre moyen de threads est de 575.

Ce comportement d’estimation pendant la diminution de la taille des instances vise à éviter une situation incertaine. Gardez ce comportement à l’esprit lorsque vous choisissez les mêmes seuils pour la diminution et l’augmentation de la taille des instances.

Nous vous recommandons de choisir une marge suffisante entre les seuils de diminution et d’augmentation de la taille des instances. Par exemple, examinez la combinaison de règles suivante bien plus adaptée.

- Augmenter les instances de 1 lorsque le % du processeur >= 80
- Diminuer les instances de 1 lorsque le % du processeur <= 60

Examinons le fonctionnement de cet exemple Supposons qu’il existe 2 instances pour commencer. Si le pourcentage moyen du processeur entre les instances atteint 80, la mise à l’échelle automatique augmente la taille des instances en ajoutant une 3ème instance. Supposons maintenant qu’au fil du temps, le pourcentage du processeur baisse à 60. La règle de diminution de la taille des instances de la mise à l’échelle automatique évalue l’état final en cas de diminution de la taille des instances. Par exemple, 60 x 3 (nombre d’instances actuel) = 180 / 2 (nombre final d’instances lors de la descente en puissance) = 90. Par conséquent, la mise à l’échelle automatique ne diminue pas la taille des instances, car il lui faudrait de nouveau monter en puissance immédiatement. Au lieu de cela, elle ignore la descente en puissance. Ensuite, supposons que lors du prochain contrôle, le processeur continue de baisser pour atteindre 50, alors une nouvelle estimation est effectuée : 50 x 3 instances = 150 / 2 instances = 75, ce qui est inférieur au seuil d’augmentation de la taille des instances qui est de 80, la diminution de la taille des instances s’exécute pour obtenir 2 instances.

### Considérations relatives aux valeurs de seuil de la mise à l’échelle pour les mesures spéciales
 Pour les mesures spéciales, telles que les mesures de longueur de file d’attente Service Bus ou de stockage, le seuil correspond au nombre moyen de messages disponibles en fonction du nombre actuel d’instances. Choisissez soigneusement la valeur de seuil pour cette mesure.

Examinons un exemple qui vous permettra de mieux comprendre ce comportement.

- Augmenter les instances de 1 lorsque le nombre de messages de file d’attente de stockage >= 50
- Diminuer les instances de 1 lorsque le nombre de messages de file d’attente de stockage <= 10

Supposons qu’il existe 2 instances pour commencer. Ensuite, supposons que les messages continuent d’arriver et lorsque vous examinez la file d’attente de stockage, le nombre total est de 50. Vous pourriez supposer que la mise à l’échelle automatique devrait démarrer une action de montée en charge. Toutefois, notez que le nombre de messages par instance est de 50/2 = 25 messages. Par conséquent, la montée en charge ne se produit pas. Pour que la montée en charge se produise, le nombre total de messages dans la file d’attente de stockage doit être égal à 100. Ensuite, supposons que le nombre total de messages atteigne 100. Une 3ème instance est ajoutée en raison d’une action de montée en charge. La prochaine action de montée en charge ne se produira que lorsque le nombre total de messages dans la file d’attente atteindra 150. Observons la diminution de la taille des instances en action. Supposons que le nombre d’instances est égal à 3. La première action de diminution de la taille des instances se produit lorsque le nombre total de messages dans la file d’attente atteint 30, ce qui donne 30/3 = 10 messages par instance, qui correspond au seuil de diminution de la taille des instances.

### Considérations relatives à la mise à l’échelle lorsque plusieurs profils sont configurés dans un paramètre de mise à l’échelle automatique

Dans un paramètre de mise à l’échelle automatique, vous pouvez choisir un profil par défaut, qui est toujours appliqué indépendamment de toute planification ou de l’heure, ou vous pouvez choisir un profil récurrent ou un profil pour une durée fixe avec une plage de dates et d’heures.

Lorsque le service de mise à l’échelle automatique traite ces éléments, il vérifie toujours ce qui suit dans l’ordre :

1. Profil de date fixe
2. Profil récurrent
3. Profil par défaut (« Toujours »)

Si une condition de profil est remplie, la mise à l’échelle automatique ne vérifie pas la condition de profil suivante. La mise à l’échelle automatique ne traite qu’un seul profil à la fois. Cela signifie que si vous souhaitez également inclure une condition de traitement d’un profil de niveau inférieur, vous devez également inclure ces règles dans le profil actuel.

Examinons cela à l’aide d’un exemple :

L’image ci-dessous illustre un paramètre de mise à l’échelle automatique avec un profil par défaut d’instances minimum = 2 et d’instances maximum = 10. Dans cet exemple, les règles sont configurées pour la montée en charge lorsque le nombre de messages dans la file d’attente est supérieur à 10 et une diminution de la taille des instances lorsque le nombre de messages dans la file d’attente est inférieur à 3. À présent, la ressource peut évoluer entre 2 et 10 instances.

En outre, il existe un profil récurrent défini pour Lundi. Il est défini pour des instances minimum = 2 et des instances maximum = 12. Cela signifie que le lundi, la première fois que la mise à l’échelle automatique vérifie cette condition, si le nombre d’instances est égal à 2, il sera mis à l’échelle pour correspondre au nouveau niveau minimum de 3. Tant que la mise à l’échelle automatique rencontre cette condition de profil respectée (lundi), elle ne traitera que les règles de montée/descente en puissance basées sur le processeur configurées pour ce profil. À ce stade, elle ne vérifiera pas la longueur de la file d’attente. Toutefois, si vous souhaitez également que la condition de longueur de la file d’attente soit vérifiée, vous devez inclure les règles du profil par défaut dans votre profil de Lundi.

De même, lorsque la mise à l’échelle automatique bascule vers le profil par défaut, elle vérifie d’abord si les conditions minimales et maximales sont remplies. Si le nombre d’instances à ce moment-là est égal à 12, la taille des instances diminue jusqu’à 10, le maximum autorisé pour le profil par défaut.

![paramètres de mise à l’échelle automatique](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### Considérations relatives à la mise à l’échelle lorsque plusieurs règles sont configurées dans un profil
Il existe des cas où vous devrez définir plusieurs règles dans un profil. L’ensemble de règles de mise à l’échelle automatique suivant est utilisé par les services lorsque plusieurs règles sont définies.

Pour l’*augmentation de la taille des instances*, la mise à l’échelle automatique s’exécute si une règle est respectée. Pour la *diminution de la taille des instances*, la mise à l’échelle automatique nécessite que toutes les règles soient respectées.
 
Pour illustrer cela, supposons que vous disposez des 4 règles de mise à l’échelle automatique suivantes :
 
- Si UC < 30 %, diminuer la taille des instances de 1
- ​Si mémoire < 50 %, diminuer la taille des instances de 1
- ​Si UC > 75 %, augmenter la taille des instancesde 1
- ​Si mémoire > 75 %, augmenter la taille des instances de 1

Alors, ce qui suit se produit :
- Si le processeur est à 76 % et la mémoire est à 50 %, une augmentation de la taille des instances se produit.
- Si le processeur est de 50 % et la mémoire est de 76 %, une augmentation de la taille des instances se produit.
 
En revanche, si le processeur est de 25 % et la mémoire est de 51 %, la mise à l’échelle automatique ne diminue **pas** la taille des instances. Pour diminuer la taille des instances, le processeur doit être de 29 % et la mémoire de 49 %.

### Sélectionnez toujours un nombre d’instances par défaut sans échec
Le nombre d’instances par défaut est important, car il s’agit du nombre d’instances pour la mise à l’échelle de vos services lorsque les mesures ne sont pas disponibles. Par conséquent, sélectionnez un nombre d’instances par défaut qui est sécurisé pour vos charges de travail.

### Configuration des notifications de mise à l’échelle automatique
La mise à l’échelle automatique notifie par e-mail les administrateurs et les collaborateurs de la ressource si l’une des conditions suivantes se produit :
- Le service de mise à l’échelle automatique ne parvient pas à effectuer une action.
- Les mesures ne sont pas disponibles pour que le service de mise à l’échelle automatique prenne une décision de mise à l’échelle.
- Les mesures sont de nouveau disponibles (récupération) pour prendre une décision de mise à l’échelle. Outre les conditions ci-dessus, vous pouvez configurer des notifications par e-mail ou webhook pour être averti en cas d’action de mise à l’échelle réussie.

<!---HONumber=AcomDC_0720_2016-->