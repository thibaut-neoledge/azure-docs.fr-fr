<properties
	pageTitle="Azure Insights : configuration avancée de la mise à l’échelle automatique à l’aide des modèles Resource Manager pour les jeux de mise à l’échelle de machines virtuelles | Microsoft Azure"
	description="Configurez la mise à l’échelle automatique pour des jeux de mise à l’échelle de machines virtuelles à partir de plusieurs règles et profils, en définissant des notifications par-email et par webhoook pour les actions de mise à l’échelle."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="ashwink"/>

# Configuration avancée de la mise à l’échelle automatique à l’aide des modèles Resource Manager pour les jeux de mise à l’échelle de machines virtuelles

Vous pouvez effectuer une mise à l’échelle dans des jeux de mise à l’échelle de machines virtuelles (VMSS) en fonction de certains seuils de métriques de performances, selon une planification périodique ou à une date donnée. Vous pouvez également configurer des notifications par courrier électronique et par webhook pour les actions de mise à l’échelle. Cette procédure pas à pas présente un exemple de configuration à l’aide d’un modèle Resource Manager sur un jeu de mise à l’échelle de machines virtuelles.

>[AZURE.NOTE] Bien que cette procédure concerne plus spécifiquement les jeux de mise à l’échelle de machines virtuelles, vous pouvez appliquer cette méthode pour effectuer une mise à l’échelle automatique de Cloud Services et de Web Apps. Si vous souhaitez utiliser un simple paramètre de mise à l’échelle (avec diminution ou augmentation de la taille des instances) sur un jeu de mise à l’échelle de machines virtuelles à partir d’une simple métrique de performances (par exemple, ressources processeur), reportez-vous à la documentation [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) et [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)



## Procédure pas à pas
Dans cette procédure pas à pas, nous utilisons [Azure Resource Explorer](https://resources.azure.com/) pour configurer et mettre à jour le paramètre de mise à l’échelle automatique pour un VMSS. L’Explorateur de ressources Azure est un moyen simple de gérer des ressources Azure via des modèles Resource Manager. Si vous débutez avec l’Explorateur de ressources Azure, veuillez lire [cette introduction](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Déployez un nouveau VMSS avec un paramètre de mise à l’échelle automatique de base. Cet article utilise celui de la galerie de démarrage rapide Azure, qui comporte un VMSS Windows avec le modèle de mise à l’échelle automatique de base. Les instances VMSS Linux fonctionnent selon le même principe.

2. Une fois le VMSS créé, accédez à la ressource VMSS à partir de l’Explorateur de ressources Azure. Vous verrez le code suivant sous le nœud Microsoft.Insights.

	![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

	L’exécution du modèle a créé un paramètre de mise à l’échelle automatique par défaut portant le nom **'autoscalewad'**. Dans la partie droite, vous pouvez afficher la définition complète de ce paramètre de mise à l’échelle. Dans ce cas, le paramètre de mise à l’échelle par défaut est associé à une règle de diminution et d’augmentation de la taille des instances basé sur le % de processeur.

3. Vous pouvez maintenant ajouter d’autres profils et règles à partir d’un calendrier ou d’exigences spécifiques. Nous allons créer un paramètre de mise à l’échelle automatique avec 3 profils. Pour comprendre les profils et les règles de mise à l’échelle automatique, veuillez consulter [Meilleures pratiques pour la mise à l’échelle automatique d’Azure Insights](./insights-autoscale-best-practices.md).

    | Profils et règles | Description |
	|---------|-------------------------------------|
	| **Profil** | **En fonction des métriques/performances** |
	| Règle | Nombre de messages de la file d’attente Service Bus > x |
	| Règle | Nombre de messages de la file d’attente Service Bus < y |
	| Règle | % UC, < n |
	| Règle | % UC, < p |
	| **Profil** | **Heures dans la matinée, en semaine (aucune règle)** |
	| **Profil** | **Jour de lancement de produit (aucune règle)** |

4. Voici un scénario de mise à l’échelle hypothétique que nous utiliserons dans le cadre de cette procédure pas à pas.
	- _**Selon la charge** : je souhaite diminuer ou augmenter la taille des instances en fonction de la charge sur mon application hébergée sur mon VMSS._
	- _**Taille de la file d’attente** : j’utilise une file d’attente Service Bus pour les messages entrants dans mon application. J’utilise le nombre de messages de la file d’attente et le % processeur, et je configure un profil par défaut pour déclencher une action de mise à l’échelle si le nombre de messages ou le % processeur atteint le seuil défini._
	- _**Heure de la journée** : je souhaite définir un profil hebdomadaire basé sur « l’heure de la journée », appelé « Heures dans la matinée, en semaine ». Au vu des données historiques, je sais qu’il est préférable d’avoir un certain nombre d’instances de machine virtuelle pour gérer la charge de mon application pendant cette période._
	- _**Dates particulières** : j’ai ajouté un profil « Jour de lancement de produit ». Je planifie à l’avance des dates spécifiques afin que mon application soit prête à gérer la charge, en fonction des annonces marketing et des dates d’introduction de nouveaux produits dans l’application._
	- _Les deux derniers profils peuvent également contenir d’autres règles basées sur les métriques de performances, mais dans mon cas, j’ai décidé de ne pas en utiliser et de me concentrer uniquement sur les règles basées sur les métriques de performances par défaut. Les règles sont facultatives pour les profils récurrents et les profils basés sur la date._

	La définition des priorités des profils et des règles du moteur de mise à l’échelle automatique est également décrite dans l’article [Meilleures pratiques pour la mise à l’échelle automatique d’Azure Insights](insights-autoscale-best-practices.md). Pour obtenir une liste des métriques communément utilisées pour la mise à l’échelle, consultez [Métriques courantes pour la mise à l’échelle automatique d’Azure Insights](insights-autoscale-common-metrics.md)

5. Assurez-vous que vous utilisez l’Explorateur de ressources en mode **lecture/écriture**

	![Autoscalewad, paramètre de mise à l’échelle automatique par défaut](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Cliquez sur Modifier. **Remplacez** l’élément « profiles » dans le paramètre de mise à l’échelle automatique par le code suivant :

	![profils](./media/insights-advanced-autoscale-vmss/profiles.png)

	```
	{
	        "name": "Perf_Based_Scale",
	        "capacity": {
	          "minimum": "2",
	          "maximum": "12",
	          "default": "2"
	        },
	        "rules": [
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 10
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 3
	            },
	            "scaleAction": {
	              "direction": "Decrease",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 85
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 60
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          }
	        ]
	      },
	      {
	        "name": "Weekday_Morning_Hours_Scale",
	        "capacity": {
	          "minimum": "4",
	          "maximum": "12",
	          "default": "4"
	        },
	        "rules": [],
	        "recurrence": {
	          "frequency": "Week",
	          "schedule": {
	            "timeZone": "Pacific Standard Time",
	            "days": [
	              "Monday",
	              "Tuesday",
	              "Wednesday",
	              "Thursday",
	              "Friday"
	            ],
	            "hours": [
	              6
	            ],
	            "minutes": [
	              0
	            ]
	          }
	        }
	      },
	      {
	        "name": "Product_Launch_Day",
	        "capacity": {
	          "minimum": "6",
	          "maximum": "20",
	          "default": "6"
	        },
	        "rules": [],
	        "fixedDate": {
	          "timeZone": "Pacific Standard Time",
	          "start": "2016-06-20T00:06:00Z",
	          "end": "2016-06-21T23:59:00Z"
	        }
	      }
	```
	Pour les champs pris en charge et leurs valeurs, consultez la [documentation API REST de la mise à l’échelle automatique](https://msdn.microsoft.com/fr-FR/library/azure/dn931928.aspx).

	Votre paramètre de mise à l’échelle automatique contient à présent les 3 profils expliqués ci-dessus.

7. 	Examinons pour finir la section **notification** de la mise à l’échelle automatique. Les notifications de mise à l’échelle automatique permettent de faire trois choses lors du déclenchement d’une action de diminution ou d’augmentation de la taille des instances.

	1. Avertir l’administrateur et les co-administrateurs de votre abonnement

	2. Envoyer un e-mail à un ensemble d’utilisateurs

	3. Déclencher un appel webhook. Lorsqu’il est déclenché, ce webhook envoie les métadonnées relatives à la condition de mise à l’échelle automatique et à la ressource VMSS. Pour plus d’informations sur la charge utile du webhook de mise à l’échelle automatique, consultez la page [Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Insights](./insights-autoscale-to-webhook-email.md).

	Ajoutez le code suivant au paramètre de mise à l’échelle automatique en remplaçant votre élément **notification** dont la valeur est null

	```
	"notifications": [
	      {
	        "operation": "Scale",
	        "email": {
	          "sendToSubscriptionAdministrator": true,
	          "sendToSubscriptionCoAdministrators": false,
	          "customEmails": [
	              "user1@mycompany.com",
	              "user2@mycompany.com"
	              ]
	        },
	        "webhooks": [
	          {
	            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
	            "properties": {
	              "optional_key1": "optional_value1",
	              "optional_key2": "optional_value2"
	            }
	          }
	        ]
	      }
	    ]

	```

	Cliquez sur le bouton **Put** dans l’Explorateur de ressources pour mettre à jour le paramètre de mise à l’échelle automatique.

Vous avez mis à jour un paramètre de mise à l’échelle automatique sur un jeu de mise à l’échelle de machines virtuelles pour inclure plusieurs profils de mise à l’échelle ainsi que des notifications de mise à l’échelle.

## Étapes suivantes

Suivez ces liens pour en savoir plus sur la mise à l’échelle automatique.

[Métriques courantes pour la mise à l’échelle automatique d’Azure Insights](./insights-autoscale-common-metrics.md)

[Meilleures pratiques pour la mise à l’échelle automatique d’Azure Insights](./insights-autoscale-best-practices.md)

[Gérer les paramètres de mise à l’échelle automatique à l’aide de PowerShell](./insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Gérer les paramètres de mise à l’échelle automatique à l’aide de la CLI](./insights-cli-samples.md#autoscale)

[Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Insights](./insights-autoscale-to-webhook-email.md)

<!---HONumber=AcomDC_0810_2016-->