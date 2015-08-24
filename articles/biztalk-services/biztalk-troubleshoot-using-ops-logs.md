<properties 
	pageTitle="Résolution des problèmes BizTalk Services à l’aide des journaux des opérations | Microsoft Azure" 
	description="Résolution des problèmes BizTalk Services à l'aide des journaux des opérations. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>


# BizTalk Services : résolution des problèmes à l’aide des journaux des opérations

## Présentation des journaux des opérations
Les journaux des opérations sont une fonction des services de gestion disponibles dans le portail de gestion Azure et qui vous permettent d'afficher l’historique des journaux des opérations effectuées sur vos services Azure, y compris BizTalk Services. Vous pouvez ainsi afficher les données d'historique des opérations de gestion liées à votre abonnement BizTalk Services jusqu'à 180 jours.

> [AZURE.NOTE]Cette fonctionnalité capture uniquement les journaux des opérations de gestion de BizTalk Services, telles que le moment du début du service, de la sauvegarde, etc. Ces opérations sont suivies, qu'elles soient effectuées à partir du portail de gestion Azure ou à l'aide de l’[API REST du service BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Pour obtenir la liste complète des opérations suivies à l'aide des services de gestion, consultez la rubrique [Opérations suivies à l'aide des services de gestion Azure](#bizops).<br/><br/> Cette fonctionnalité ne capture pas les journaux des activités liées à l'exécution de BizTalk Services (telles que les messages traités par des ponts, etc.). Pour afficher ces journaux, vous devez utiliser l’affichage de suivi du portail BizTalk Services. Pour plus d'informations, consultez la rubrique [Messages de suivi](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## Affichage des journaux des opérations BizTalk Services
1. Dans le portail de gestion Azure, sélectionnez **Services de gestion**, puis l'onglet **Journaux des opérations**.
2. Vous pouvez filtrer les journaux selon différents paramètres tels que l'abonnement, la plage de dates, le type de service (ex. : BizTalk Services), le nom du service ou le statut de l'opération (ex : réussite, échec).
3. Sélectionnez la coche pour afficher la liste filtrée. L'image suivante montre les activités liées à testbiztalkservice : ![Affichage des journaux des opérations][ViewLogs] 
4. Pour afficher plus d'informations sur une opération spécifique, sélectionnez la ligne et cliquez sur **Détails** en bas de la page.


## <a name="bizops"></a>Opérations suivies à l'aide des services de gestion Azure
Le tableau ci-dessous répertorie les opérations suivies à l'aide des services de gestion Azure :

Nom d’opération | Task
--- | ---
CreateBizTalkService | Opération de création d'un nouveau service BizTalk
DeleteBizTalkService | Opération de suppression d'un service BizTalk
RestartBizTalkService | Opération de redémarrage d'un service BizTalk
StartBizTalkService | Opération de démarrage d'un service BizTalk
StopBizTalkService | Opération d'arrêt d'un service BizTalk
DisableBizTalkService | Opération de désactivation d'un service BizTalk
EnableBizTalkService | Opération d'activation d'un service BizTalk
BackupBizTalkService | Opération de sauvegarde d'un service BizTalk
RestoreBizTalkService | Opération de restauration d'un service BizTalk depuis une sauvegarde spécifiée
SuspendBizTalkService | Opération de suspension d'un service BizTalk
ResumeBizTalkService | Opération de reprise d'un service BizTalk
ScaleBizTalkService | Opération de mise à l'échelle d'un service BizTalk
ConfigUpdateBizTalkService | Opération de mise à jour de la configuration d'un service BizTalk
ServiceUpdateBizTalkService | Opération de mise à niveau d'un service BizTalk vers une version différente
PurgeBackupBizTalkService | Opération de vidage des sauvegardes du service BizTalk en dehors de la période de rétention


## Voir aussi
- [Sauvegarde d'un service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restauration d'un service BizTalk depuis une sauvegarde](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Approvisionnement de BizTalk Services avec le portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Tableau comparatif des états d'approvisionnement BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Limitation dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Nom et clé de l'émetteur dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 

<!---HONumber=August15_HO7-->