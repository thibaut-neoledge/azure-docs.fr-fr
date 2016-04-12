<properties 
   pageTitle="Avertir les utilisateurs de données reçues à partir des capteurs ou d’autres systèmes | Microsoft Azure"
   description="Décrit l’utilisation des hubs d'événements pour informer les utilisateurs des données transmises par les capteurs."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/08/2016"
   ms.author="spyros;sethm" />

# Avertir les utilisateurs de données reçues à partir des capteurs ou d’autres systèmes

Supposons que vous possédez une application qui surveille les données en temps réel, ou génère des rapports selon une planification. Si vous consultez le site web sur lequel figurent les graphiques ou les rapports en temps réel, vous verrez quelque chose qui nécessite une action. Que faire si vous devez être alerté pour ce genre de situation, plutôt que de devoir vous rappeler vous-même de vérifier le site web ? Imaginez que vous avez une lumière de croissance dans une serre, et que vous avez besoin de savoir immédiatement si cette lumière est éteinte. Installer un capteur d’éclairage dans la serre pour recevoir une alerte par e-mail serait un moyen de savoir si cette lumière est éteinte.

![][1]

Dans un autre scénario, imaginons que vous gérez une pension pour animaux, et que vous avez besoin d’être alerté lorsque les niveaux de stock d’alimentation sont bas. Vous pourriez par exemple faire en sorte de recevoir un SMS de votre système ERP si votre stock de nourriture pour chien en entrepôt atteignent un niveau critique.

![][2]

La question est de savoir comment obtenir ces informations critiques selon certaines conditions remplies, et non pas de les obtenir à partir d’un rapport statique. Si vous utilisez un [Azure Event Hub][] ou un [Azure IoT Hub][] pour recevoir des données à partir d’appareils ou d’applications d’entreprise telles que [Dynamics AX][], vous disposez de plusieurs options pour déterminer de quelle façon les traiter. Vous pouvez les afficher sur un site web, les analyser, les stocker, et les utiliser pour déclencher des commandes permettant des actions. Pour ce faire, vous pouvez utiliser des outils puissants tels que les [sites web Azure][], [SQL Azure][], [HDInsight][], [Cortana Analytics Suite][], [IoT Suite][], [Logic Apps][], ou [Azure Notification Hubs][]. Mais parfois, vous souhaitez simplement envoyer ces données à un utilisateur pour un minimum de surcharge. Pour vous montrer comment procéder simplement avec un peu de code, nous vous présentons un nouvel exemple [AppToNotifyUsers][]. Les options incluses sont le téléphone, les SMS et les e-mails (SMTP).

## Structure d'application

L'application est écrite en code C#, et le fichier Lisez-moi de l'exemple contient toutes les informations dont vous avez besoin pour modifier, générer et publier l'application. Les sections suivantes fournissent une vue d'ensemble détaillée de l'application.

Nous partons du principe que vous avez transmis des événements critiques vers un Azure Event Hub ou un IoT Hub. N'importe quel Hub fera l'affaire, tant que vous y avez accès et connaissez la chaîne de connexion.

Si vous ne disposez pas d'un Event Hub ou IoT hub, vous pouvez facilement configurer un banc d'essai avec un shield Arduino et un Raspberry Pi, en suivant les instructions du projet [Relier les points](https://github.com/Azure/connectthedots). Le capteur d'éclairage sur le shield Arduino envoie les informations du niveau d’éclairage via le Raspberry Pi à un [Azure Event Hub][] (**ehdevices**), et un travail [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) transmet des alertes à un deuxième concentrateur d'événements (**ehalerts**) si l’éclairage capté est inférieur à un certain niveau.

Lorsque **AppToNotify** démarre, il lit un fichier de configuration (App.config) pour obtenir l'URL et les informations d'identification pour que le concentrateur d'événements reçoive les alertes. Il génère ensuite un processus pour surveiller en permanence le concentrateur d'événements et tous les messages que celui-ci transmet (tant que vous avez accès à l'URL du Event Hub ou du IoT Hub et que les informations d'identification sont valides), ce code de lecteur de hubs d’événement pourra en permanence lire chaque nouvel événement. Lors du démarrage, l'application lit également les URL et les informations d'identification pour le service de messagerie (téléphone, e-mail, SMS) à utiliser, ainsi que le nom et l’adresse de l'expéditeur et une liste de destinataires.

Une fois que le moniteur du concentrateur d'événements détecte un message, il déclenche un processus qui envoie ce message selon la méthode spécifiée dans le fichier de configuration. Notez qu'il envoie chaque message qu'il détecte. Si vous définissez le moniteur selon un concentrateur d'événements qui reçoit dix messages par seconde, l'expéditeur enverra 10 messages par seconde (dix messages par seconde, 10 SMS par seconde, et 10 appels par seconde). Pour cette raison, assurez-vous de contrôler le concentrateur d'événements qui ne reçoit que les alertes qui doivent être envoyées, et non un concentrateur d'événements qui reçoit toutes les données brutes transmises par vos applications ou capteurs.

## Applicabilité

Le code dans cet exemple montre uniquement comment surveiller des hubs d’événement et comment avoir recours à des services de messagerie externe dans le cas où vous souhaitez ajouter cette fonctionnalité à votre application. Notez que cette solution est un exemple de développement et de Loisirs créatifs uniquement. Elle ne répond pas aux exigences d'entreprise telles que la redondance, le basculement, le redémarrage en cas d’échec, etc.. Pour plus de solutions complètes et de production, consultez les rubriques suivantes :

- Utilisation de connecteurs ou des notifications Push avec le service [Azure Logic Apps](../app-service-logic/app-service-logic-connectors-list.md).
- Utilisation de [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx), tel que décrit dans le blog [Diffusion des notifications Push à des millions d'appareils mobiles avec Azure Notification Hubs](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs). 

## Étapes suivantes

Créer un service de notification simple qui envoie des e-mails ou des SMS aux destinataires, ou les appelle, pour transmettre des données reçues d’un Event Hub ou d’un IoT Hub, est un processus très simple. Pour déployer la solution pour informer les utilisateurs en fonction des données reçues par ces concentrateurs, visitez [AppToNotifyUsers][].

Pour plus d'informations sur ces concentrateurs, consultez les articles suivants :

- [Azure Event Hubs]
- [Azure IoT Hub]
- Prise en main avec un [didacticiel des hubs d'événements].
- Un [exemple d'application complet qui utilise des hubs d’événements].
- Une [solution de messages de file d'attente] utilisant les files d'attente Service Bus.

Pour déployer la solution pour informer les utilisateurs en fonction des données reçues par ces concentrateurs, visitez :

- [AppToNotifyUsers][]

[didacticiel des hubs d'événements]: event-hubs-csharp-ephcs-getstarted.md
[Azure IoT Hub]: https://azure.microsoft.com/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/services/event-hubs/
[Azure Event Hub]: https://azure.microsoft.com/services/event-hubs/
[exemple d'application complet qui utilise des hubs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solution de messages de file d'attente]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[sites web Azure]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Analytics Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[Logic Apps]: https://azure.microsoft.com/services/app-service/logic/
[Azure Notification Hubs]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png

<!---HONumber=AcomDC_0316_2016-->