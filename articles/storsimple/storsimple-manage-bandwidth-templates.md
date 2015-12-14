<properties 
   pageTitle="Gérer vos modèles de bande passante StorSimple | Microsoft Azure"
   description="Décrit comment gérer les modèles de bande passante StorSimple, ce qui vous permet de contrôler la consommation de bande passante."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Utiliser le service StorSimple Manager pour gérer les modèles de bande passante StorSimple

## Vue d’ensemble

Les modèles de bande passante vous permettent de configurer plusieurs planifications selon le moment de la journée des couches de données à partir de l’appareil StorSimple vers le cloud. Vous pouvez aussi créer, modifier, supprimer et enregistrer ces planifications comme modèles. Ces modèles de bande passante peuvent ensuite être appliqués sur les conteneurs de volume pour contrôler la bande passante utilisée par votre appareil StorSimple lors de l’exécution des opérations impliquant le cloud. En fonction de votre modèle d’utilisation de la bande passante, vous pouvez également choisir dans une liste de modèles par défaut.

Avec les planifications de limitation de bande passante, vous pouvez :

- Spécifier des planifications qui personnalisent l’utilisation de la bande passante en fonction des charges de travail.

- Centraliser la gestion et réutiliser les planifications sur plusieurs appareils de manière simple et transparente.

Cette fonctionnalité est disponible uniquement pour les appareils physiques StorSimple et pas pour les appareils virtuels. Tous les modèles de la bande passante de votre service sont affichés dans un format tabulaire et contiennent les informations suivantes :

- **Nom** : nom unique affecté au modèle de bande passante lors de sa création.

- **Planification** : nombre de planifications contenues dans un modèle donné de bande passante.

- **Utilisé par** : nombre de volumes utilisant les modèles de bande passante.

Utilisez la page **Configurer** du service StorSimple Manager du portail Azure Classic pour gérer les modèles de la bande passante. Les tâches les plus courantes associées aux modèles de bande passante qui peuvent être effectuées sur cette page sont les suivantes :

- Ajouter un modèle de bande passante
- Modifier un modèle de bande passante
- Supprimer un modèle de bande passante
- Utiliser un modèle de bande passante par défaut
- Créer un modèle de bande passante sur une journée entière qui commence à une heure spécifiée

Vous trouverez également des informations supplémentaires pour aider à configurer les modèles de bande passante dans :

- Questions et réponses sur les modèles de bande passante
- Meilleures pratiques pour les modèles de bande passante

## Ajouter un modèle de bande passante

Pour créer un modèle de bande passante, procédez comme suit.

#### Pour ajouter un modèle de bande passante

1. Sur la page **Configurer** du service StorSimple Manager, cliquez sur **ajouter/modifier un modèle de bande passante**.

2. Dans la boîte de dialogue **Ajouter/modifier des modèles de bande passante** :

   1. À partir de la liste déroulante **Modèle**, sélectionnez **Créer** pour ajouter un nouveau modèle de bande passante.
   2. Spécifiez un nom unique pour votre modèle de bande passante.

3. Définissez une **Planification de la bande passante**. Pour créer une planification :

   1. Dans la liste déroulante, sélectionnez les jours de la semaine, pour lesquels la planification est configurée. Vous pouvez sélectionner plusieurs jours en cochant les cases situées en regard dans la liste.
   2. Sélectionnez l’option **Toute la journée** si la planification s’applique à toute la journée. Lorsque cette option est cochée, vous ne pouvez plus spécifier une **Heure de début** ou une **Heure de fin**. La planification s’exécute de 12h00 a.m. à 11:59 p.m.
   3. Dans la liste déroulante, sélectionnez une **Heure de début**. Cette heure correspond au début de la planification.
   4. Dans la liste déroulante, sélectionnez une **Heure de fin**. Cette heure correspond à la fin de la planification.
   
         > [AZURE.NOTE] Overlapping schedules are not allowed. If the start and end times will result in an overlapping schedule, you will see an error message to that effect.

   5. Spécifiez le **Débit de bande passante**. Il s’agit de la bande passante en mégabits par seconde (Mbits/s) utilisée par votre appareil StorSimple dans les opérations impliquant le cloud. Fournissez un nombre compris entre 1 et 1 000 pour ce champ.
   
   6. Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Le modèle que vous avez créé vient s’ajouter à la liste des modèles de bande passante sur la page **Configurer** du service.

    ![Créer un modèle de bande passante](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Cliquez sur **Enregistrer** en bas de la page, puis sur **Oui** lorsque vous êtes invité à confirmer l’opération. Les modifications de configuration que vous avez effectuées sont alors enregistrées.

## Modifier un modèle de bande passante

Pour modifier un modèle de bande passante, procédez comme suit.

### Pour modifier un modèle de bande passante

1. Cliquez sur **Ajouter/modifier des modèles de bande passante**.

2. Dans la boîte de dialogue **Ajouter/modifier des modèles de bande passante** :

   1. Dans la liste déroulante **Modèle**, sélectionnez un modèle de bande passante existant que vous souhaitez modifier.
   2. Complétez vos modifications. (Vous pouvez modifier l’un des paramètres existants.)
   3. Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Le modèle modifié s’affiche dans la liste des modèles de bande passante de la page Configurer du service.

3. Pour enregistrer les modifications, cliquez sur **Enregistrer** en bas de la page. Cliquez sur **Oui** lorsque vous êtes invité à confirmer l’opération.

> [AZURE.NOTE]Vous n’êtes pas autorisé à enregistrer vos modifications si la planification modifiée chevauche une planification existante dans le modèle de bande passante que vous modifiez.

## Supprimer un modèle de bande passante

Pour supprimer un modèle de bande passante, procédez comme suit.

#### Pour supprimer un modèle de bande passante

1. Dans la liste de la table des modèles de bande passante de votre service, sélectionnez le modèle que vous souhaitez supprimer. Une icône de suppression (**x**) s’affiche à l’extrême droite du modèle sélectionné. Cliquez sur l’icône **x** pour supprimer le modèle.

2. Vous êtes invité à confirmer l’opération. Cliquez sur **OK** pour poursuivre.

Si le modèle est en cours d’utilisation par des volumes, vous n’êtes pas autorisé à le supprimer. Vous verrez un message d’erreur indiquant que le modèle est en cours d’utilisation. Un message d’erreur s’affiche vous indiquant que toutes les références au modèle doivent être supprimées.

Vous pouvez supprimer toutes les références au modèle en accédant à la page **Conteneurs de volumes** et en modifiant les conteneurs de volume qui utilisent ce modèle afin qu’ils utilisent un autre modèle ou un paramètre de bande passante personnalisé ou illimité. Lorsque toutes les références ont été supprimées, vous pouvez supprimer le modèle.

## Utiliser un modèle de bande passante par défaut

Un modèle de bande passante par défaut est fourni et utilisé par les conteneurs de volumes par défaut pour appliquer les contrôles de bande passante lors de l’accès au cloud. Le modèle par défaut sert également de référence prête pour les utilisateurs qui créent leurs propres modèles. Les détails du modèle par défaut sont les suivants :

- **Nom** : nombre illimité de nuit et de week-ends

- **Planification** : une seule planification du lundi au vendredi qui applique un taux de bande passante de 1 Mbits/s entre 8 h 00 et 17 h 00, heure de l’appareil. Pour le reste de la semaine, la bande passante est définie sur Unlimited.

Le modèle par défaut peut être modifié. L’utilisation de ce modèle (y compris les versions modifiées) est suivie.

## Créer un modèle de bande passante sur une journée entière qui commence à une heure spécifiée

Suivez cette procédure pour créer une planification qui démarre à une heure spécifiée et s’exécute toute la journée. Dans l’exemple, la planification commence à 9 heures du matin et s’exécute jusqu’à 9 h le lendemain matin. Il est important de noter que les heures de début et de fin d’une planification donnée doivent être contenues dans la même planification de 24 heures et ne peuvent pas couvrir plusieurs jours. Si vous avez besoin configurer des modèles de bande passante qui s’étendent sur plusieurs jours, vous devrez utiliser plusieurs planifications (comme illustré dans l’exemple).

#### Pour créer un modèle de bande passante sur une journée entière

1. Créez une planification qui commence à 9 heures du matin et s’exécute jusqu’à minuit.

2. Ajoutez une autre planification. Configurez la deuxième planification pour qu’elle s’exécute à partir de minuit jusqu’à 9 heures du matin.

3. Enregistrez le modèle de bande passante.

La planification composite démarre ensuite à une heure de votre choix et s’exécute toute la journée.

## Questions et réponses sur les modèles de bande passante

**Q**. Qu’advient-il des contrôles de bande passante lorsque vous êtes entre deux planifications ? (Une planification est terminée et une autre n’a pas encore démarré).

**R**. Dans ce cas, aucun contrôle de bande passante n’est utilisé. Cela signifie que le périphérique peut utiliser une bande passante illimitée lors de la hiérarchisation des données vers le cloud.

**Q**. Peut modifier les modèles de bande passante sur un périphérique hors connexion ?

**R**. Vous ne pouvez pas modifier les modèles de bande passante sur les conteneurs de volumes si l’appareil correspondant est hors connexion.

**Q**. Peut modifier un modèle de bande passante associé à un conteneur de volumes lorsque les volumes associés sont hors connexion ?

**R**. Vous pouvez modifier un modèle de bande passante associé à un conteneur de volumes dont les volumes associés sont hors connexion ? Notez que lorsque les volumes sont hors connexion, aucune donnée n’est hiérarchisée de l’appareil vers le cloud.

**Q**. Peut-on supprimer un modèle par défaut ?

**R**. Même si vous pouvez le faire, il est déconseillé de supprimer un modèle par défaut. L’utilisation d’un modèle par défaut, y compris les versions modifiées, est suivie. Les données de suivi sont analysées et, au fil du temps, sont utilisées pour améliorer le modèle par défaut.

**Q**. Comment déterminer que vos modèles de bande passante doivent être modifiés ?

**R**. Lorsque vous commencez à constater que le réseau ralentit ou se retrouve bloqué plusieurs fois par jour, il est temps que vous modifiiez les modèles de bande passante. Si tel est le cas, surveillez le stockage et l’utilisation du réseau en examinant les graphiques des performances d’E/S et de débit du réseau.

À partir des données de débit du réseau, identifiez l’heure et les conteneurs de volumes où le goulot d’étranglement du réseau s’est produit. Si cela se produit lorsque les données sont en cours de hiérarchisation vers le cloud (obtenez ces informations à partir des performances d’E/S de tous les conteneurs de volume pour l’appareil vers le cloud), vous devez modifier les modèles de la bande passante associés à vos conteneurs de volumes.

Une fois que les modèles modifiés sont en cours d’utilisation, vous devez surveiller à nouveau le réseau en cas de latences importantes. Si des latences subsistent, vous devez revoir vos modèles de bande passante.

**Q**. Que se passe-t-il si plusieurs conteneurs de volumes sur mon appareil ont des planifications qui se chevauchent, mais que différentes limites s’appliquent à chacune ?

**R**. Supposons que vous disposiez d’un appareil avec 3 conteneurs de volumes. Les planifications associées à ces conteneurs se chevauchent totalement. Pour chacun de ces conteneurs, les limites de bande passante utilisées sont respectivement 5, 10 et 15 Mbits/s. Lorsque les E/S se produisent sur l’ensemble de ces conteneurs en même temps, la valeur minimale des 3 limites de bande passante peut s’appliquer : dans ce cas, 5 Mbits/s, car ces demandes sortantes d’E/S partagent la même file d’attente.

## Meilleures pratiques pour les modèles de bande passante

Suivez ces meilleures pratiques pour votre appareil StorSimple :

- Configurez les modèles de bande passante sur votre appareil pour activer la limitation variable du débit réseau par l’appareil à différentes heures de la journée. Ces modèles de bande passante lorsqu’ils sont utilisés avec les planifications de sauvegarde peuvent exploiter efficacement une bande passante réseau supplémentaire pour les opérations de cloud pendant les heures creuses.

- Calculez la bande passante réelle requise pour un déploiement spécifique en fonction de la taille du déploiement et de l’objectif de délai de récupération.

## Étapes suivantes

- En savoir plus sur les [composants StorSimple](storsimple-components.md). 
- En savoir plus sur [l’utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_1203_2015-->