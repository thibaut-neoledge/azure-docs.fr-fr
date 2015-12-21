<properties 
	pageTitle="Configurer la mise à l’échelle automatique pour un service cloud | Microsoft Azure" 
	description="Découvrez comment utiliser le portail pour configurer des règles de mise à l’échelle automatique pour un service cloud et les ressources liées dans Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015"
	ms.author="adegeo"/>





# Mise à l’échelle automatique d’une application

Sur la page Scale du portail Azure Classic, vous pouvez mettre à l'échelle manuellement votre application ou définir des paramètres afin que la mise à l'échelle soit automatique. Vous pouvez mettre à l'échelle des applications qui exécutent des rôles Web, des rôles de travail ou des machines virtuelles. Pour mettre à l'échelle une application qui exécute des instances de rôles Web ou de rôles de travail, vous ajoutez ou supprimez des instances de rôle pour absorber la charge de travail.

Lorsque vous augmentez ou diminuez l'échelle d'une application qui exécute des machines virtuelles, les nouvelles machines ne sont pas créées ni supprimées, mais sont activées ou désactivées à partir d'un groupe à haute disponibilité de machines précédemment créées. Vous pouvez spécifier la mise à l'échelle en fonction du pourcentage moyen d'utilisation de l'unité centrale ou du nombre de messages dans une file d'attente.

Vous devez tenir compte des informations suivantes avant de configurer la mise à l'échelle de votre application :

- Vous devez ajouter les machines virtuelles que vous créez dans un groupe à haute disponibilité pour mettre à l'échelle une application qui les utilise. Les machines virtuelles que vous ajoutez peuvent être initialement activées ou désactivées, mais elles seront activées pour une action d'augmentation et désactivées pour une action de diminution. Pour plus d'informations sur la gestion des machines virtuelles et des groupes à haute disponibilité, consultez la page [Gestion de la disponibilité des machines virtuelles](../virtual-machines-manage-availability.md).

- L'utilisation des cœurs a une incidence sur la mise à l'échelle. Des machines virtuelles ou des instances de rôle plus importantes utilisent davantage de cœurs. Vous ne pouvez mettre à l'échelle une application que dans les limites des cœurs de votre abonnement. Par exemple, si la limite de votre abonnement est de vingt cœurs et que vous exécutez une application avec deux machines virtuelles de taille moyenne (quatre cœurs au total), vous pouvez uniquement augmenter l'échelle des autres déploiements de service cloud de votre abonnement en ajoutant seize cœurs. Toutes les machines virtuelles appartenant à un groupe à haute disponibilité qui sont utilisées dans la mise à l'échelle d'une application doivent avoir la même taille. Pour plus d'informations sur l'utilisation des cœurs et la taille des machines, consultez la page [Tailles de machines virtuelles et services cloud pour Microsoft Azure](http://msdn.microsoft.com/library/dn197896.aspx).

- Vous devez créer une file d'attente et l'associer à un rôle ou à un groupe à haute disponibilité avant de mettre à l'échelle une application en fonction d'un seuil de message. Pour plus d'informations, consultez la page [Utilisation du service de stockage de file d'attente](../storage-dotnet-how-to-use-queues.md).

- Vous pouvez mettre à l'échelle des ressources qui sont liées à votre service cloud. Pour plus d’informations sur la liaison des ressources, consultez la rubrique [Liaison d’une ressource à un service cloud](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- Pour permettre la haute disponibilité de votre application, vous devez vous assurer qu'elle est déployée avec plusieurs instances de rôle ou machines virtuelles. Pour plus d'informations, consultez la page [Contrats de niveau de service](http://azure.microsoft.com/support/legal/sla/).


## Mise à l'échelle manuelle d'une application exécutant des rôles Web ou de travail

Sur la page Scale, vous pouvez augmenter ou diminuer manuellement le nombre des instances s'exécutant dans un service cloud.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.

2. Cliquez sur **Scale**. La mise à l'échelle automatique est désactivée par défaut pour tous les rôles, ce qui signifie que vous pouvez modifier manuellement le nombre d'instances utilisées par votre application.

    ![Scale page][manual_scale]

3. Chaque rôle dans le service cloud est doté d'un curseur permettant de modifier le nombre d'instances à utiliser. Pour ajouter une instance de rôle, faites glisser la barre vers la droite. Pour supprimer une instance, faites glisser la barre vers la gauche.
    
    ![Mettre à l'échelle le rôle][slider_role]
    
    Vous pouvez uniquement augmenter le nombre d'instances utilisées si le nombre approprié de cœurs est disponible pour prendre en charge les instances. Les couleurs du curseur représentent les cœurs utilisés et disponibles dans votre abonnement :
    
    - Le bleu représente les cœurs utilisés par le rôle sélectionné.
    
    - Le gris foncé représente les cœurs utilisés par tous les rôles et les machines virtuelles de l'abonnement.
    
    - Le gris clair représente les cœurs disponibles à utiliser pour la mise à l'échelle.
    
    - Le rose représente une modification effectuée qui n'a pas été enregistrée.

4. Cliquez sur **Enregistrer**. Les instances de rôle sont ajoutées ou supprimées en fonction de vos sélections.

## Mise à l'échelle automatique d'une application exécutant des rôles Web, des rôles de travail ou des machines virtuelles

Sur la page Scale, vous pouvez configurer votre service cloud pour augmenter ou diminuer automatiquement le nombre d'instances ou de machines virtuelles utilisées par votre application. Vous pouvez configurer la mise à l'échelle à l'aide des paramètres suivants :

- [Average CPU usage](#averagecpu) : si le pourcentage moyen d'utilisation de l'unité centrale est supérieur ou inférieur aux seuils spécifiés, les instances de rôle sont créées ou supprimées, ou les machines virtuelles sont activées ou désactivées d'un groupe à haute disponibilité.
- [Queue messages](#queuemessages) : si le nombre de messages dans une file d'attente est supérieur ou inférieur au seuil spécifié, les instances de rôle sont créées ou supprimées, ou les machines virtuelles sont activées ou désactivées d'un groupe à haute disponibilité.

## Utilisation moyenne de l'UC

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.

2. Cliquez sur **Scale**.

3. Faites défiler jusqu'à la section du rôle ou du groupe à haute disponibilité, puis cliquez sur **CPU**. Cela permet d'activer la mise à l'échelle automatique de votre application en fonction du pourcentage moyen d'utilisation des ressources de l'unité centrale utilisées.

    ![Mise à l'échelle automatique activée][autoscale_on]

4. Chaque rôle ou groupe à haute disponibilité est doté d'un curseur permettant de modifier le nombre d'instances à utiliser. Pour définir le nombre maximal d'instances à utiliser, faites glisser la barre située à droite vers la droite. Pour définir le nombre minimal d'instances à utiliser, faites glisser la barre située à gauche vers la gauche.
    
    **Remarque :** sur la page Échelle, **Instance** représente un rôle d’instance ou une instance de machine virtuelle.
    
    ![Plage d'instances][instance_range]
    
    Le nombre maximal d'instances se limite au nombre de cœurs disponibles dans l'abonnement. Les couleurs du curseur représentent les cœurs utilisés et disponibles dans votre abonnement :
    
    - Le bleu représente le nombre maximal de cœurs utilisés par le rôle.
    
    - Le gris foncé représente les cœurs utilisés par tous les rôles et les machines virtuelles de l'abonnement. Lorsque cette valeur chevauche les cœurs utilisés par le rôle, la couleur passe au bleu foncé.
    
    - Le gris clair représente les cœurs disponibles à utiliser pour la mise à l'échelle.
    
    - Le rose représente une modification effectuée qui n'a pas été enregistrée.

5. Un curseur est utilisé pour spécifier la plage de pourcentage moyen d'utilisation de l'unité centrale. Lorsque ce pourcentage est supérieur au paramètre maximal, davantage d'instances de rôle sont créées ou supprimées, ou des machines virtuelles sont activées. Lorsque ce pourcentage est inférieur au paramètre minimal, des instances de rôle sont supprimées ou des machines virtuelles sont désactivées. Pour définir le pourcentage moyen d'utilisation de l'unité centrale maximal, faites glisser la barre située à droite vers la droite. Pour définir le pourcentage minimal moyen d'utilisation de l'unité centrale, faites glisser la barre située à gauche vers la gauche.

    ![UC cible][target_cpu]

6. Vous pouvez spécifier le nombre d'instances à ajouter ou à activer à chaque fois que votre application augmente d'échelle. Pour augmenter le nombre d'instances créées ou activées lorsque votre application est mise à l'échelle, faites glisser la barre vers la droite. Pour en diminuer le nombre, faites glisser la barre vers la gauche.

    ![Augmenter l'échelle de l'UC][scale_cpuup]

7. Définissez le nombre de minutes d'attente entre la dernière action de mise à l'échelle et l'action d'augmentation d'échelle suivante. La dernière action de mise à l'échelle peut être une augmentation ou une diminution d'échelle.

    ![Durée d'augmentation][scale_uptime]

    Toutes les instances sont incluses lors du calcul du pourcentage moyen d'utilisation de l'unité centrale et la moyenne est calculée sur l'utilisation au cours de la dernière heure. Selon le nombre d'instances que votre application utilise, le délai d'attente peut être supérieur à celui spécifié pour l'action de mise à l'échelle, si ce délai a été défini avec une valeur très faible. La durée minimale entre les actions de mise à l'échelle est de cinq minutes. Les actions de mise à l'échelle ne peuvent pas se produire si aucune des instances n'est en état de transition.

8. Vous pouvez également spécifier le nombre d'instances à supprimer ou à désactiver lorsque votre application diminue d'échelle. Pour augmenter le nombre d'instances supprimées ou désactivées lorsque votre application est mise à l'échelle, faites glisser la barre vers la droite. Pour en diminuer le nombre, faites glisser la barre vers la gauche.

    ![Diminuer l'échelle de l'UC][scale_cpudown]
    
    Si votre application connaît des pics soudains d'utilisation de l'unité centrale, assurez-vous de disposer d'un nombre minimal d'instances suffisant pour gérer ces pics.

9. Définissez le nombre de minutes d'attente entre la dernière action de mise à l'échelle et l'action de diminution d'échelle suivante. La dernière action de mise à l'échelle peut être une augmentation ou une diminution d'échelle.

    ![Durée de diminution][scale_downtime]

10. Cliquez sur **Save**. L'action de mise à l'échelle peut durer jusqu'à cinq minutes.

## Messages de file d'attente

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
2. Cliquez sur **Scale**.
3. Faites défiler jusqu'à la section du rôle ou du groupe à haute disponibilité, puis cliquez sur **File d'attente**. Cela permet d'activer la mise à l'échelle automatique de votre application en fonction du nombre cible de messages de file d'attente.

    ![File d'attente de mise à l'échelle][scale_queue]

4. Chaque rôle ou groupe à haute disponibilité défini dans le service cloud est doté d'un curseur permettant de modifier le nombre d'instances à utiliser. Pour définir le nombre maximal d'instances à utiliser, faites glisser la barre située à droite vers la droite. Pour définir le nombre minimal d'instances à utiliser, faites glisser la barre située à gauche vers la gauche.

    ![Plage de file d'attente][queue_range]
    
    **Remarque :** sur la page Échelle, **Instance** représente un rôle d’instance ou une instance de machine virtuelle.
    
    Le nombre maximal d'instances se limite au nombre de cœurs disponibles dans l'abonnement. Les couleurs du curseur représentent les cœurs utilisés et disponibles dans votre abonnement : - Le bleu représente le nombre maximal de cœurs utilisés par le rôle. - Le gris foncé représente les cœurs utilisés par tous les rôles et les machines virtuelles de l’abonnement. Lorsque cette valeur chevauche les cœurs utilisés par le rôle, la couleur passe au bleu foncé. - Le gris clair représente les cœurs disponibles pouvant être utilisés pour la mise à l’échelle. - Le rose indique qu’une modification a été apportée, mais qu’elle n’a pas été enregistrée.

5. Sélectionnez le compte de stockage associé à la file d'attente que vous souhaitez utiliser.

    ![Nom de stockage][storage_name]

6. Sélectionnez la file d'attente.

    ![Nom de la file d'attente][queue_name]

7. Spécifiez le nombre de messages que chaque instance peut prendre en charge. Les instances sont mises à l'échelle en fonction du nombre total de messages divisé par le nombre cible de messages par machine.

    ![Nombre de messages][message_number]

8. Vous pouvez spécifier le nombre d'instances à ajouter ou à activer à chaque fois que votre application augmente d'échelle. Pour augmenter le nombre d'instances ajoutées ou activées lorsque votre application est mise à l'échelle, faites glisser la barre vers la droite. Pour en diminuer le nombre, faites glisser la barre vers la gauche.

    ![Augmenter l'échelle de l'UC][scale_cpuup]

9. Définissez le nombre de minutes d'attente entre la dernière action de mise à l'échelle et l'action d'augmentation d'échelle suivante. La dernière action de mise à l'échelle peut être une augmentation ou une diminution d'échelle.

    ![Durée d'augmentation][scale_uptime]
    
    La durée minimale entre les actions de mise à l'échelle est de cinq minutes. Les actions de mise à l'échelle ne peuvent pas se produire si aucune des instances n'est en état de transition.

10. Vous pouvez également spécifier le nombre d'instances à supprimer ou à ne pas utiliser lorsque votre application diminue d'échelle. Un curseur permet de spécifier l'augmentation d'échelle. Pour augmenter le nombre d'instances supprimées ou non utilisées lorsque votre application diminue d'échelle, faites glisser la barre vers la droite. Pour en diminuer le nombre, faites glisser la barre vers la gauche.

    ![Diminuer l'échelle de l'UC][scale_cpudown]

11.	Définissez le nombre de minutes d'attente entre la dernière action de mise à l'échelle et l'action de diminution d'échelle suivante. La dernière action de mise à l'échelle peut être une augmentation ou une diminution d'échelle.

    ![Durée de diminution][scale_downtime]

12. Cliquez sur **Save**. L'action de mise à l'échelle peut durer jusqu'à cinq minutes.

## Mise à l'échelle des ressources liées

Lorsque vous mettez à l'échelle un rôle, il est souvent avantageux de mettre également à l'échelle la base de données qui est utilisée par l'application. Si vous liez la base de données au service cloud, vous modifiez l'édition de la base de données SQL et redimensionnez la base de données sur la page Scale.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
2. Cliquez sur **Scale**.
3. Dans la section Linked Resources, sélectionnez l'édition à utiliser pour la base de données.

    ![Ressources liées][linked_resources]

4. Sélectionnez la taille de la base de données.
5. Cliquez sur **Enregistrer** pour mettre à jour les ressources liées.

## Planification de la mise à l'échelle de votre application

Vous pouvez planifier la mise à l'échelle automatique de votre application en configurant des planifications à différentes heures. Les options suivantes vous permettent d'effectuer une mise à l'échelle automatique :

- **No schedule** : option par défaut qui permet de mettre à l'échelle votre application automatiquement, de la même façon, à tout moment.

- **Day and night** : option qui permet de spécifier une mise à l'échelle à des heures précises du jour et de la nuit.

**Remarque :** actuellement, les planifications ne sont pas disponibles pour les applications qui utilisent des machines virtuelles.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
2. Cliquez sur **Scale**.
3. Sur la page Scale, cliquez sur **set up schedule times**.

    ![Planifier la mise à l'échelle][scale_schedule]

4. Sélectionnez le type de planification de mise à l'échelle que vous souhaitez configurer.

5. Spécifiez les heures de début et de fin de la journée et définissez le fuseau horaire. Pour la planification de jour et de nuit, les heures représentent le début et la fin de la journée, les heures restantes correspondant à la nuit.

6. Cliquez sur la coche en bas de la page pour enregistrer les planifications.

7. Une fois les planifications enregistrées, elles apparaissent dans la liste. Vous pouvez sélectionner la planification horaire que vous voulez utiliser, puis modifier les paramètres de mise à l'échelle. Ces derniers s'appliqueront uniquement pendant la planification sélectionnée. Pour modifier les planifications, cliquez sur **set up schedule times**.

[manual_scale]: ./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png
[slider_role]: ./media/cloud-services-how-to-scale/CloudServices_SliderRole.png
[autoscale_on]: ./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png
[instance_range]: ./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png
[target_cpu]: ./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png
[scale_cpuup]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png
[scale_uptime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png
[scale_cpudown]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png
[scale_downtime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png
[scale_queue]: ./media/cloud-services-how-to-scale/CloudServices_QueueScale.png
[queue_range]: ./media/cloud-services-how-to-scale/CloudServices_QueueRange.png
[storage_name]: ./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png
[queue_name]: ./media/cloud-services-how-to-scale/CloudServices_QueueName.png
[message_number]: ./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png
[linked_resources]: ./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png
[scale_schedule]: ./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png
 

<!---HONumber=AcomDC_1210_2015-->