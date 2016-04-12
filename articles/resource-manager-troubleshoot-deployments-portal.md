<properties
   pageTitle="Résolution des problèmes liés aux déploiements avec le portail Azure | Microsoft Azure"
   description="Décrit comment utiliser le portail Azure pour détecter et résoudre les problèmes de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interface de ligne de commande Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Si vous avez reçu une erreur lors du déploiement des ressources sur Azure, vous devez résoudre le problème. Le portail Azure fournit une interface qui vous permet de rechercher facilement les erreurs et de déterminer des corrections potentielles.

Vous pouvez dépanner votre déploiement en examinant les journaux d'audit ou les opérations de déploiement. Cette rubrique illustre les deux méthodes.

Vous pouvez éviter certaines erreurs en validant votre modèle et votre infrastructure avant le déploiement. Pour plus d’informations, consultez la page [Déploiement d’un groupe de ressources avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

## Utilisation des journaux d'audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs d’un déploiement, procédez comme suit :

1. Affichez les journaux d’audit dans le portail en sélectionnant **Parcourir** et **Journaux d’audit**.

    ![sélectionner des journaux d’audit](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. Le panneau **Journaux d’audit** affiche un résumé des opérations récentes de tous les groupes de ressources de votre abonnement. Il inclut une représentation graphique du moment où ont eu lieu les opérations et de leur état, ainsi qu’une liste des opérations.

    ![afficher des actions](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Vous pouvez sélectionner n’importe quelle opération de la liste. Choisissez l'opération qui contient l'erreur que vous souhaitez rechercher.

    ![sélectionner une opération](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. Vous verrez tous les événements de cette opération. Remarquez les **ID de corrélation** dans le résumé. Cet ID est utilisé pour effectuer le suivi des événements connexes. Il peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème. Vous pouvez sélectionner un des événements pour afficher ses détails.

    ![sélectionner un événement](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. Vous verrez ainsi plus d'informations sur l'événement. Soyez attentif en particulier aux **Propriétés** pour en savoir plus sur l'erreur.

    ![afficher les détails du journal d'audit](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Vous pouvez filtrer votre affichage des journaux d'audit pour vous concentrer sur des conditions spécifiques. Pour personnaliser l'affichage du journal d'audit :

1. Sélectionnez **Filtrer** en haut du panneau **Journaux d’audit**.

    ![filtrer les journaux](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. À partir du panneau **Filtre**, sélectionnez les conditions pour restreindre l'affichage des journaux d'audit aux opérations que vous souhaitez afficher. Par exemple, vous pouvez filtrer les opérations pour afficher uniquement les erreurs d’un groupe de ressources en particulier.

    ![définir des options de filtre](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. Vous pouvez filtrer davantage les opérations en définissant un intervalle de temps. L'image suivante filtre l'affichage sur un intervalle de temps de 20 minutes.

    ![définir l'heure](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

Une fois la vue des journaux d’audit mise à jour, seules les opérations qui répondent à la condition spécifiée sont affichées. Ces paramètres sont conservés lors de la prochaine consultation des journaux d’audit, vous devrez donc peut-être modifier ces valeurs pour étendre l’affichage des opérations.

Nous espérons que vous avez pu découvrir pourquoi votre déploiement a échoué. Vous pouvez également consulter les opérations de déploiement pour plus d'informations sur l'état, comme l’indique la section suivante.

## Utilisation des opérations de déploiement pour résoudre les problèmes

Pour afficher les opérations de déploiement, procédez comme suit :

1. Pour le groupe de ressources impliqué dans le déploiement, notez l'état du dernier déploiement. Vous pouvez sélectionner cet état pour obtenir plus de détails.

    ![état du déploiement](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Vous verrez l'historique des déploiements récents. Sélectionnez le déploiement ayant échoué.

    ![état du déploiement](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Affichez les informations sur le déploiement, puis sélectionnez l'opération ayant échoué pour afficher les détails sur l'erreur.

    ![afficher le déploiement ayant échoué](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. Dans le panneau **Détails des opérations**, vous verrez des informations sur l'opération ayant échoué. En particulier, soyez attentif au message d'état.

    ![afficher le message d’état](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## Étapes suivantes

- Pour en savoir plus sur l'utilisation des journaux d'audit pour surveiller d'autres types d'actions, consultez [Auditer les opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant son exécution, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->