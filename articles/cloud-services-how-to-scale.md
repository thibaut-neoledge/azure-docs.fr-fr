<properties linkid="manage-services-how-to-scale-a-cloud-service" urlDisplayName="How to scale" pageTitle="How to scale a cloud service - Azure" metaKeywords="Azure link resource, scaling cloud service" description="Learn how to scale a cloud service and linked resources in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Scale an Application" authors="davidmu" solutions="" manager="jeffreyg" editor="mattshel" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="davidmu" />

# Mise à l'échelle d'une application

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Sur la page Scale du portail de gestion Azure, vous pouvez mettre à l'échelle manuellement votre application ou définir des paramètres afin que la mise à l'échelle soit automatique. Vous pouvez mettre à l'échelle des applications qui exécutent des rôles Web, des rôles de travail ou des machines virtuelles. Pour mettre à l'échelle une application qui exécute des instances de rôles Web ou de rôles de travail, vous ajoutez ou supprimez des instances de rôle pour absorber la charge de travail.

Lorsque vous augmentez ou diminuez l'échelle d'une application qui exécute des machines virtuelles, les nouvelles machines ne sont pas créées ni supprimées, mais sont activées ou désactivées à partir d'un groupe à haute disponibilité de machines précédemment créées. Vous pouvez spécifier la mise à l'échelle en fonction du pourcentage moyen d'utilisation de l'unité centrale ou du nombre de messages dans une file d'attente.

Vous devez tenir compte des informations suivantes avant de configurer la mise à l'échelle de votre application :

-   Vous devez ajouter les machines virtuelles que vous créez dans un groupe à haute disponibilité pour mettre à l'échelle une application qui les utilise. Les machines virtuelles que vous ajoutez peuvent être initialement activées ou désactivées, mais elles seront activées pour une action d'augmentation et désactivées pour une action de diminution. Pour plus d'informations sur la gestion des machines virtuelles et des groupes à haute disponibilité, consultez la page [Gestion de la disponibilité des machines virtuelles][Gestion de la disponibilité des machines virtuelles].
-   L'utilisation des cœurs a une incidence sur la mise à l'échelle. Des machines virtuelles ou des instances de rôle plus importantes utilisent davantage de cœurs. Vous ne pouvez mettre à l'échelle une application que dans les limites des cœurs de votre abonnement. Par exemple, si la limite de votre abonnement est de vingt cœurs et que vous exécutez une application avec deux machines virtuelles de taille moyenne (quatre cœurs au total), vous pouvez uniquement augmenter l'échelle des autres déploiements de service cloud de votre abonnement en ajoutant seize cœurs. Toutes les machines virtuelles appartenant à un groupe à haute disponibilité qui sont utilisées dans la mise à l'échelle d'une application doivent avoir la même taille. Pour plus d'informations sur l'utilisation des cœurs et la taille des machines, consultez la page [Tailles de machines virtuelles et services cloud pour Microsoft Azure][Tailles de machines virtuelles et services cloud pour Microsoft Azure].
-   Vous devez créer une file d'attente et l'associer à un rôle ou à un groupe à haute disponibilité avant de mettre à l'échelle une application en fonction d'un seuil de message. Pour plus d'informations, consultez la page [Utilisation du service de stockage de file d'attente][Utilisation du service de stockage de file d'attente].
-   Vous pouvez mettre à l'échelle des ressources qui sont liées à votre service cloud. Pour plus d'informations sur la liaison des ressources, consultez la page [Liaison d'une ressource à un service cloud][Liaison d'une ressource à un service cloud].
-   Pour permettre la haute disponibilité de votre application, vous devez vous assurer qu'elle est déployée avec plusieurs instances de rôle ou machines virtuelles. Pour plus d'informations, consultez la page [Contrats de niveau de service][Contrats de niveau de service].

Vous pouvez effectuer les actions de mise à l'échelle suivantes pour un service cloud :

-   [Mise à l'échelle manuelle d'une application exécutant des rôles Web ou de travail][Mise à l'échelle manuelle d'une application exécutant des rôles Web ou de travail]
-   [Mise à l'échelle automatique d'une application exécutant des rôles Web, des rôles de travail ou des machines virtuelles][Mise à l'échelle automatique d'une application exécutant des rôles Web, des rôles de travail ou des machines virtuelles]
-   [Mise à l'échelle des ressources liées][Mise à l'échelle des ressources liées]
-   [Planification de la mise à l'échelle de votre application][Planification de la mise à l'échelle de votre application]

## <span id="manualscale"></span></a>Mise à l'échelle manuelle d'une application exécutant des rôles Web ou de travail

Sur la page Scale, vous pouvez augmenter ou diminuer manuellement le nombre des instances s'exécutant dans un service cloud.

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.

2.  Cliquez sur **Scale**. La mise à l'échelle automatique est désactivée par défaut pour tous les rôles, ce qui signifie que vous pouvez modifier manuellement le nombre d'instances utilisées par votre application.

    ![Page Mettre à l'échelle][Page Mettre à l'échelle]

3.  Chaque rôle dans le service cloud est doté d'un curseur permettant de modifier le nombre d'instances à utiliser. Pour ajouter une instance de rôle, faites glisser la barre vers la droite. Pour supprimer une instance, faites glisser la barre vers la gauche.

    ![Mettre à l'échelle le rôle][Mettre à l'échelle le rôle]

    Vous pouvez uniquement augmenter le nombre d'instances utilisées si le nombre approprié de cœurs est disponible pour prendre en charge les instances. Les couleurs du curseur représentent les cœurs utilisés et disponibles dans votre abonnement :

    -   Le bleu représente les cœurs utilisés par le rôle sélectionné.
    -   Le gris foncé représente les cœurs utilisés par tous les rôles et les machines virtuelles de l'abonnement.
    -   Le gris clair représente les cœurs disponibles à utiliser pour la mise à l'échelle.
    -   Le rose représente une modification effectuée qui n'a pas été enregistrée.

4.  Cliquez sur **Enregistrer**. Les instances de rôle sont ajoutées ou supprimées en fonction de vos sélections.

## <span id="autoscale"></span></a>Mise à l'échelle automatique d'une application exécutant des rôles Web, des rôles de travail ou des machines virtuelles

Sur la page Scale, vous pouvez configurer votre service cloud pour augmenter ou diminuer automatiquement le nombre d'instances ou de machines virtuelles utilisées par votre application. Vous pouvez configurer la mise à l'échelle à l'aide des paramètres suivants :

-   [Average CPU usage][Average CPU usage] : si le pourcentage moyen d'utilisation de l'unité centrale est supérieur ou inférieur aux seuils spécifiés, les instances de rôle sont créées ou supprimées, ou les machines virtuelles sont activées ou désactivées d'un groupe à haute disponibilité.
-   [Queue messages][Queue messages] : si le nombre de messages dans une file d'attente est supérieur ou inférieur au seuil spécifié, les instances de rôle sont créées ou supprimées, ou les machines virtuelles sont activées ou désactivées d'un groupe à haute disponibilité.

### <span id="averagecpu"></span></a>Utilisation moyenne de l'UC

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
2.  Cliquez sur **Scale**.
3.  Faites défiler jusqu'à la section du rôle ou du groupe à haute disponibilité, puis cliquez sur **CPU**. Cela permet d'activer la mise à l'échelle automatique de votre application en fonction du pourcentage moyen d'utilisation des ressources de l'unité centrale utilisées.

    ![Mise à l'échelle automatique activée][Mise à l'échelle automatique activée]

4.  Chaque rôle ou groupe à haute disponibilité est doté d'un curseur permettant de modifier le nombre d'instances à utiliser. Pour définir le nombre maximal d'instances à utiliser, faites glisser la barre située à droite vers la droite. Pour définir le nombre minimal d'instances à utiliser, faites glisser la barre située à gauche vers la gauche.

    **Remarque :** sur la page Scale, **Instance** représente un rôle d'instance ou une instance de machine virtuelle.

    ![Plage d'instances][Plage d'instances]

    Le nombre maximal d'instances se limite au nombre de cœurs disponibles dans l'abonnement. Les couleurs du curseur représentent les cœurs utilisés et disponibles dans votre abonnement :

    -   Le bleu représente le nombre maximal de cœurs utilisés par le rôle.
    -   Le gris foncé représente les cœurs utilisés par tous les rôles et les machines virtuelles de l'abonnement. Lorsque cette valeur chevauche les cœurs utilisés par le rôle, la couleur passe au bleu foncé.
    -   Le gris clair représente les cœurs disponibles à utiliser pour la mise à l'échelle.
    -   Le rose représente une modification effectuée qui n'a pas été enregistrée.

5.  Un curseur est utilisé pour spécifier la plage de pourcentage moyen d'utilisation de l'unité centrale. Lorsque ce pourcentage est supérieur au paramètre maximal, davantage d'instances de rôle sont créées ou supprimées, ou des machines virtuelles sont activées. Lorsque ce pourcentage est inférieur au paramètre minimal, des instances de rôle sont supprimées ou des machines virtuelles sont désactivées. Pour définir le pourcentage moyen d'utilisation de l'unité centrale maximal, faites glisser la barre située à droite vers la droite. Pour définir le pourcentage minimal moyen d'utilisation de l'unité centrale, faites glisser la barre située à gauche vers la gauche.

    ![UC cible][UC cible]

6.  Vous pouvez spécifier le nombre d'instances à ajouter ou à activer à chaque fois que votre application augmente d'échelle. Pour augmenter le nombre d'instances créées ou activées lorsque votre application est mise à l'échelle, faites glisser la barre vers la droite. Pour en diminuer le nombre, faites glisser la barre vers la gauche.

    ![Augmenter l'échelle de l'UC][Augmenter l'échelle de l'UC]

7.  Définissez le nombre de minutes d'attente entre la dernière action de mise à l'échelle et l'action d'augmentation d'échelle suivante. La dernière action de mise à l'échelle peut être une augmentation ou une diminution d'échelle.

    ![Durée d'augmentation][Durée d'augmentation]

    Toutes les instances sont incluses lors du calcul du pourcentage moyen d'utilisation de l'unité centrale et la moyenne est calculée sur l'utilisation au cours de la dernière heure. Selon le nombre d'instances que votre application utilise, le délai d'attente peut être supérieur à celui spécifié pour l'action de mise à l'échelle, si ce délai a été défini avec une valeur très faible. La durée minimale entre les actions de mise à l'échelle est de cinq minutes. Les actions de mise à l'échelle ne peuvent pas se produire si aucune des instances n'est en état de transition.

8.  Vous pouvez également spécifier le nombre d'instances à supprimer ou à désactiver lorsque votre application diminue d'échelle. Pour augmenter le nombre d'instances supprimées ou désactivées lorsque votre application est mise à l'échelle, faites glisser la barre vers la droite. Pour en diminuer le nombre, faites glisser la barre vers la gauche.

    ![Diminuer l'échelle de l'UC][Diminuer l'échelle de l'UC]

    Si votre application connaît des pics soudains d'utilisation de l'unité centrale, assurez-vous de disposer d'un nombre minimal d'instances suffisant pour gérer ces pics.

9.  Définissez le nombre de minutes d'attente entre la dernière action de mise à l'échelle et l'action de diminution d'échelle suivante. La dernière action de mise à l'échelle peut être une augmentation ou une diminution d'échelle.

    ![Durée de diminution][Durée de diminution]

10. Cliquez sur **Enregistrer**. L'action de mise à l'échelle peut durer jusqu'à cinq minutes.

### <span id="queuemessages"></span></a>Messages de file d'attente

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
2.  Cliquez sur **Scale**.
3.  Faites défiler jusqu'à la section du rôle ou du groupe à haute disponibilité, puis cliquez sur **File d'attente**. Cela permet d'activer la mise à l'échelle automatique de votre application en fonction du nombre cible de messages de file d'attente.

    ![File d'attente de mise à l'échelle][File d'attente de mise à l'échelle]

4.  Chaque rôle ou groupe à haute disponibilité défini dans le service cloud est doté d'un curseur permettant de modifier le nombre d'instances à utiliser. Pour définir le nombre maximal d'instances à utiliser, faites glisser la barre située à droite vers la droite. Pour définir le nombre minimal d'instances à utiliser, faites glisser la barre située à gauche vers la gauche.

    ![Plage de file d'attente][Plage de file d'attente]

    **Remarque :** sur la page Scale, **Instance** représente un rôle d'instance ou une instance de machine virtuelle.

    Le nombre maximal d'instances se limite au nombre de cœurs disponibles dans l'abonnement. Les couleurs du curseur représentent les cœurs utilisés et disponibles dans votre abonnement :

    -   Le bleu représente le nombre maximal de cœurs utilisés par le rôle.
    -   Le gris foncé représente les cœurs utilisés par tous les rôles et les machines virtuelles de l'abonnement. Lorsque cette valeur chevauche les cœurs utilisés par le rôle, la couleur passe au bleu foncé.
    -   Le gris clair représente les cœurs disponibles à utiliser pour la mise à l'échelle.
    -   Le rose représente une modification effectuée qui n'a pas été enregistrée.

5.  Sélectionnez le compte de stockage associé à la file d'attente que vous souhaitez utiliser.

    ![Nom de stockage][Nom de stockage]

6.  Sélectionnez la file d'attente.

    ![Nom de la file d'attente][Nom de la file d'attente]

7.  Spécifiez le nombre de messages que chaque instance peut prendre en charge. Les instances sont mises à l'échelle en fonction du nombre total de messages divisé par le nombre cible de messages par machine.

    ![Nombre de messages][Nombre de messages]

8.  Vous pouvez spécifier le nombre d'instances à ajouter ou à activer à chaque fois que votre application augmente d'échelle. Pour augmenter le nombre d'instances ajoutées ou activées lorsque votre application est mise à l'échelle, faites glisser la barre vers la droite. Pour en diminuer le nombre, faites glisser la barre vers la gauche.

    ![Augmenter l'échelle de l'UC][Augmenter l'échelle de l'UC]

9.  Définissez le nombre de minutes d'attente entre la dernière action de mise à l'échelle et l'action d'augmentation d'échelle suivante. La dernière action de mise à l'échelle peut être une augmentation ou une diminution d'échelle.

    ![Durée d'augmentation][Durée d'augmentation]

    La durée minimale entre les actions de mise à l'échelle est de cinq minutes. Les actions de mise à l'échelle ne peuvent pas se produire si aucune des instances n'est en état de transition.

10. Vous pouvez également spécifier le nombre d'instances à supprimer ou à ne pas utiliser lorsque votre application diminue d'échelle. Un curseur permet de spécifier l'augmentation d'échelle. Pour augmenter le nombre d'instances supprimées ou non utilisées lorsque votre application diminue d'échelle, faites glisser la barre vers la droite. Pour en diminuer le nombre, faites glisser la barre vers la gauche.

    ![Diminuer l'échelle de l'UC][Diminuer l'échelle de l'UC]

11. Définissez le nombre de minutes d'attente entre la dernière action de mise à l'échelle et l'action de diminution d'échelle suivante. La dernière action de mise à l'échelle peut être une augmentation ou une diminution d'échelle.

    ![Durée de diminution][Durée de diminution]

12. Cliquez sur **Enregistrer**. L'action de mise à l'échelle peut durer jusqu'à cinq minutes.

## <span id="scalelink"></span></a>Mise à l'échelle des ressources liées

Lorsque vous mettez à l'échelle un rôle, il est souvent avantageux de mettre également à l'échelle la base de données qui est utilisée par l'application. Si vous liez la base de données au service cloud, vous modifiez l'édition de la base de données SQL et redimensionnez la base de données sur la page Scale.

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
2.  Cliquez sur **Scale**.
3.  Dans la section Linked Resources, sélectionnez l'édition à utiliser pour la base de données.

    ![Ressources liées][Ressources liées]

4.  Sélectionnez la taille de la base de données.
5.  Cliquez sur **Enregistrer** pour mettre à jour les ressources liées.

## <span id="schedule"></span></a>Planification de la mise à l'échelle de votre application

Vous pouvez planifier la mise à l'échelle automatique de votre application en configurant des planifications à différentes heures. Les options suivantes vous permettent d'effectuer une mise à l'échelle automatique :

-   **No schedule** : option par défaut qui permet de mettre à l'échelle votre application automatiquement, de la même façon, à tout moment.

-   **Day and night** : option qui permet de spécifier une mise à l'échelle à des heures précises du jour et de la nuit.

**Remarque :** les planifications ne sont actuellement pas disponibles pour les applications qui utilisent des machines virtuelles.

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Cloud Services**, puis sur le nom du service cloud pour ouvrir le tableau de bord.
2.  Cliquez sur **Scale**.
3.  Sur la page Scale, cliquez sur **set up schedule times**.

    ![Planifier la mise à l'échelle][Planifier la mise à l'échelle]

4.  Sélectionnez le type de planification de mise à l'échelle que vous souhaitez configurer.

5.  Spécifiez les heures de début et de fin de la journée et définissez le fuseau horaire. Pour la planification de jour et de nuit, les heures représentent le début et la fin de la journée, les heures restantes correspondant à la nuit.

6.  Cliquez sur la coche en bas de la page pour enregistrer les planifications.

7.  Une fois les planifications enregistrées, elles apparaissent dans la liste. Vous pouvez sélectionner la planification horaire que vous voulez utiliser, puis modifier les paramètres de mise à l'échelle. Ces derniers s'appliqueront uniquement pendant la planification sélectionnée. Pour modifier les planifications, cliquez sur **set up schedule times**.

  [Gestion de la disponibilité des machines virtuelles]: http://www.windowsazure.com/fr-fr/manage/windows/common-tasks/manage-vm-availability/
  [Tailles de machines virtuelles et services cloud pour Microsoft Azure]: http://msdn.microsoft.com/fr-fr/library/dn197896.aspx
  [Utilisation du service de stockage de file d'attente]: http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/queue-service
  [Liaison d'une ressource à un service cloud]: http://www.windowsazure.com/fr-fr/manage/services/cloud-services/how-to-manage-a-cloud-service/#linkresources
  [Contrats de niveau de service]: https://www.windowsazure.com/fr-fr/support/legal/sla/
  [Mise à l'échelle manuelle d'une application exécutant des rôles Web ou de travail]: #manualscale
  [Mise à l'échelle automatique d'une application exécutant des rôles Web, des rôles de travail ou des machines virtuelles]: #autoscale
  [Mise à l'échelle des ressources liées]: #scalelink
  [Planification de la mise à l'échelle de votre application]: #schedule
  [portail de gestion]: https://manage.windowsazure.com/
  [Page Mettre à l'échelle]: ./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png
  [Mettre à l'échelle le rôle]: ./media/cloud-services-how-to-scale/CloudServices_SliderRole.png
  [Average CPU usage]: #averagecpu
  [Queue messages]: #queuemessages
  [Mise à l'échelle automatique activée]: ./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png
  [Plage d'instances]: ./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png
  [UC cible]: ./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png
  [Augmenter l'échelle de l'UC]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png
  [Durée d'augmentation]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png
  [Diminuer l'échelle de l'UC]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png
  [Durée de diminution]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png
  [File d'attente de mise à l'échelle]: ./media/cloud-services-how-to-scale/CloudServices_QueueScale.png
  [Plage de file d'attente]: ./media/cloud-services-how-to-scale/CloudServices_QueueRange.png
  [Nom de stockage]: ./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png
  [Nom de la file d'attente]: ./media/cloud-services-how-to-scale/CloudServices_QueueName.png
  [Nombre de messages]: ./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png
  [Ressources liées]: ./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png
  [Planifier la mise à l'échelle]: ./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png
