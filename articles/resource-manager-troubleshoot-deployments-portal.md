<properties
   pageTitle="Affichage des opérations de déploiement avec le Portail | Microsoft Azure"
   description="Explique comment utiliser le portail Azure pour détecter les problèmes de déploiement de Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# Afficher les opérations de déploiement avec le Portail Azure

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interface de ligne de commande Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Vous pouvez afficher les opérations d’un déploiement via le portail Azure. Il est plus intéressant d’afficher les opérations lorsque vous recevez une erreur lors du déploiement ; cet article porte donc sur l’affichage des opérations ayant échoué. Le portail fournit une interface qui vous permet de rechercher facilement les erreurs et de déterminer des corrections potentielles.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Utilisation des opérations de déploiement pour résoudre les problèmes

Pour afficher les opérations de déploiement, procédez comme suit :

1. Pour le groupe de ressources impliqué dans le déploiement, notez l'état du dernier déploiement. Vous pouvez sélectionner cet état pour obtenir plus de détails.

    ![état du déploiement](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Vous verrez l'historique des déploiements récents. Sélectionnez le déploiement ayant échoué.

    ![état du déploiement](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Sélectionnez **Échec. Cliquez ici pour obtenir des détails** afin de connaître la raison de l’échec du déploiement. Dans l’image ci-dessous, l’enregistrement DNS n’est pas unique.

    ![afficher le déploiement ayant échoué](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Ce message d’erreur devrait vous suffire pour lancer le dépannage. Toutefois, si vous avez besoin de plus d’informations sur les tâches ayant été effectuées, vous pouvez afficher les opérations, comme indiqué dans les étapes suivantes.

4. Vous pouvez afficher toutes les opérations de déploiement dans le panneau **Déploiement**. Sélectionnez n’importe quelle opération pour afficher plus de détails.

    ![afficher des opérations](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    Dans ce cas, vous verrez que le compte de stockage, le réseau virtuel et le groupe à haute disponibilité ont été créés avec succès. L’adresse IP publique a cependant échoué, et d’autres ressources n’ont pas été tentées.

5. Pour afficher les événements pour le déploiement, sélectionnez **Événements**.

    ![visualiser les événements](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Vous affichez tous les événements pour le déploiement ; pour voir plus de détails, cliquez sur un événement quelconque.

    ![voir les événements](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## Utilisation des journaux d'audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs d’un déploiement, procédez comme suit :

1. Pour afficher les journaux d’audit pour un groupe de ressources, sélectionnez **Journaux d’audit**.

    ![sélectionner des journaux d’audit](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. Le panneau **Journaux d’audit** affiche un résumé des opérations récentes de tous les groupes de ressources de votre abonnement. Il inclut une représentation graphique du moment où ont eu lieu les opérations et de leur état, ainsi qu’une liste des opérations.

    ![afficher des actions](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Vous pouvez filtrer votre affichage des journaux d'audit pour vous concentrer sur des conditions spécifiques. Sélectionnez **Filtrer** en haut du panneau **Journaux d’audit**.

    ![filtrer les journaux](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. À partir du panneau **Filtre**, sélectionnez les conditions pour restreindre l’affichage des journaux d’audit aux opérations que vous souhaitez afficher. Par exemple, vous pouvez filtrer les opérations pour afficher uniquement les erreurs du groupe de ressources.

    ![définir des options de filtre](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. Vous pouvez filtrer davantage les opérations en définissant un intervalle de temps. L’image suivante filtre l’affichage sur un intervalle de temps de 20 minutes.

    ![définir l'heure](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. Vous pouvez sélectionner n’importe quelle opération de la liste. Choisissez l'opération qui contient l'erreur que vous souhaitez rechercher.

    ![sélectionner une opération](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Vous verrez tous les événements de cette opération. Remarquez les **ID de corrélation** dans le résumé. Cet ID est utilisé pour effectuer le suivi des événements connexes. Il peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème. Vous pouvez sélectionner un des événements pour afficher ses détails.

    ![sélectionner un événement](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Vous verrez ainsi plus d'informations sur l'événement. Soyez attentif en particulier aux **Propriétés** pour en savoir plus sur l’erreur.

    ![afficher les détails du journal d'audit](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Le filtre appliqué au journal d’audit est conservé lors de la prochaine consultation de ce dernier ; vous devrez donc peut-être modifier ces valeurs pour étendre l’affichage des opérations.

## Étapes suivantes

- Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, consultez [Auditer les opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant son exécution, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0622_2016-->