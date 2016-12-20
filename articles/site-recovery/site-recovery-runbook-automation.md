---
title: "Ajouter des Runbooks Azure Automation à des plans de récupération | Microsoft Docs"
description: "Cet article explique comment Microsoft Azure Site Recovery vous permet désormais d’étendre des plans de récupération à l’aide de Microsoft Azure Automation, afin d’effectuer des tâches complexes lors de la récupération vers Microsoft Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 10/23/2016
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 9f9a71cd7c6688dfd42dcb7ce52847f2016daf58


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Ajouter des Runbooks Azure Automation à des plans de récupération
Ce didacticiel explique comment Microsoft Azure Site Recovery s’intègre dans Microsoft Azure Automation pour permettre l’extensibilité des plans de récupération. Les plans de récupération peuvent orchestrer la récupération des machines virtuelles protégées via Microsoft Azure Site Recovery, pour la réplication vers un cloud secondaire et la réplication vers Microsoft Azure. Ils garantissent que le récupération est **précise et cohérente**, **répétable** et **automatisée**. Si vous effectuez le basculement de vos machines virtuelles vers Microsoft Azure, l’intégration avec Microsoft Azure Automation étend les plans de récupération et vous offre la possibilité d’exécuter des Runbooks, ce qui vous donne accès à des tâches d’automatisation très puissantes.

Si vous n’avez pas encore entendu parler de Microsoft Azure Automation, connectez-vous [ici](https://azure.microsoft.com/services/automation/) et téléchargez [ici](https://azure.microsoft.com/documentation/scripts/) des exemples de scripts. Lisez plus d’informations sur [Microsoft Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) et découvrez [ici](https://azure.microsoft.com/blog/?p=166264) comment orchestrer la récupération vers Microsoft Azure à l’aide de plans de récupération.

Dans ce didacticiel, nous allons voir comment intégrer des Runbooks Microsoft Azure dans des plans de récupération. Nous allons automatiser des tâches simples, qui nécessitaient auparavant une intervention manuelle, et savoir comment convertir une opération de récupération en plusieurs étapes en une seule action, impliquant un seul clic. Nous verrons également comment dépanner un script simple en cas de problème.

## <a name="customize-the-recovery-plan"></a>Personnaliser un plan de récupération
1. Commençons par ouvrir le panneau de ressources du plan de récupération. Vous pouvez voir que deux machines virtuelles ont été ajoutées au plan de récupération pour la récupération.

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. Cliquez sur le bouton Personnaliser pour commencer l’ajout d’un runbook. Le panneau de personnalisation du plan de récupération s’ouvre.

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. Cliquez avec le bouton droit sur Groupe 1 : Démarrer et sélectionnez Ajouter une action postérieure.
2. Optez pour le choix d’un script dans le nouveau panneau.
3. Nommez le script « Hello World ».
4. Choisissez un nom de compte Automation. Il s’agit du compte Azure Automation. Notez que ce compte peut se trouver dans n’importe quel emplacement géographique Azure, mais qu’il doit se trouver dans le même abonnement que le coffre Site Recovery.
5. Sélectionnez un runbook à partir du compte Automation. Il s’agit du script qui s’exécute pendant l’exécution du plan de récupération après la récupération du premier groupe.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. Cliquez sur OK pour enregistrer le script. Cela ajoute le script au groupe d’actions postérieures de Groupe 1 : Démarrer.

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Points essentiels de l’ajout d’un script
1. Vous pouvez cliquer avec le bouton droit sur le script et choisir « Supprimer l’étape » ou « Mettre à jour le script ».
2. Un script peut s’exécuter sur Azure lors du basculement depuis l’emplacement local vers Azure. Il peut s’exécuter sur Azure en tant que script du côté principal avant l’arrêt, pendant la restauration d’Azure vers l’emplacement local.
3. Lorsqu’un script s’exécute, il injecte un contexte de plan de récupération.

Voici à quoi ressemble la variable de contexte.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Le tableau ci-dessous contient le nom et la description de chaque variable dans le contexte.

| **Nom de la variable** | **Description** |
| --- | --- |
| RecoveryPlanName |Nom du plan en cours d'exécution. Vous permet d'entreprendre une action basée sur le nom à l'aide du même script |
| FailoverType |Indique si le basculement est un test, planifié ou non planifié. |
| FailoverDirection |Indique si la récupération est principale ou secondaire |
| GroupID |Identifie le numéro de groupe dans le plan de récupération lorsque le plan est en cours d'exécution. |
| VmMap |Tableau de toutes les machines virtuelles du groupe. |
| Clé VMMap |Clé unique (GUID) pour chaque machine virtuelle. Ce GUID est identique à l’ID VMM de la machine virtuelle, le cas échéant. |
| RoleName |Nom de la machine virtuelle Azure qui est en cours de récupération |
| CloudServiceName |Nom Azure Cloud Service sous lequel la machine virtuelle est créée. |
| CloudServiceName (dans le modèle de déploiement Resource Manager) |Nom du groupe de ressources Azure sous lequel la machine virtuelle est créée. |

## <a name="using-complex-variables-per-recovery-plan"></a>Utilisation de variables complexes par le plan de récupération
Parfois, un runbook nécessite davantage d’informations que le contexte du plan de récupération. Il n’existe aucun mécanisme de première classe pour transmettre un paramètre à un runbook. Toutefois, si vous souhaitez utiliser le même script via plusieurs plans de récupération, vous pouvez utiliser la variable de contexte de plan de récupération « RecoveryPlanName » et utiliser la technique expérimentale ci-dessous afin d’utiliser une variable complexe Azure Automation dans un runbook. L’exemple ci-dessous montre comment vous pouvez créer trois différentes ressources de variables complexes et les utiliser dans le runbook en fonction du nom du plan de récupération.

Supposons que vous souhaitez utiliser 3 paramètres supplémentaires dans un runbook. Encodez-les au format JSON {« Var1 » : « testautomation », « Var2 » : « Unplanned », « Var3 » : « PrimaryToSecondary »}

Utilisez une [variable complexe AA](../automation/automation-variables.md#variable-types) pour créer une ressource Automation.
Nommez la variable <RecoveryPlanName>-params.
Vous pouvez utiliser la référence ici pour créer une [variable complexe](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Pour différents plans de récupération, nommez la variable comme suit :

1. recoveryPlanName1>-params
2. recoveryPlanName2>-params
3. recoveryPlanName3>-params

À présent, dans le script, faites référence aux paramètres comme suit :

1. Obtenez le nom du plan de récupération à partir de la variable $rpname = $Recoveryplancontext.
2. Obtenez la ressource de $paramValue = « $($rpname)-params ».
3. Utilisez ceci comme variable complexe pour le plan de récupération en appelant Get-AzureAutomationVariable [-AutomationAccountName] <String> -Name $paramValue.

Par exemple, pour obtenir le paramètre de variable complexe pour le plan de récupération SharepointApp, créez une variable complexe Azure Automation appelée « SharepointApp-params ».

Utilisez ceci dans le plan de récupération en extrayant la variable à partir de la ressource à l’aide de l’instruction Get-AzureAutomationVariable [-AutomationAccountName] <String> [-Name] $paramValue. [Pour plus d’informations, lisez ceci](https://msdn.microsoft.com/library/dn913772.aspx).

De cette façon, le même script peut servir pour différents plans de récupération en stockant la variable complexe spécifique du plan dans les ressources.

## <a name="sample-scripts"></a>Exemples de scripts
Pour voir un référentiel de scripts que vous pouvez importer directement dans votre compte Automation, consultez [Référentiel OMS de scripts de Kristian Nese](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation).

Le script ici est un modèle Azure Resource Manager qui déploie tous les scripts ci-dessous.

* Groupe de sécurité réseau

Le runbook du groupe de sécurité réseau attribue des adresses IP publiques à chaque machine virtuelle au sein du plan de récupération et joint les cartes réseau virtuelles associées à un groupe de sécurité réseau, ce qui autorise la communication par défaut.

* PublicIP

Le runbook PublicIP attribue des adresses IP publiques à chaque machine virtuelle au sein du plan de récupération. L’accès aux machines et applications varie selon les paramètres de pare-feu de chaque invité.

* CustomScript

Le runbook CustomScript attribue des adresses IP publiques à chaque machine virtuelle au sein du plan de récupération et installe une extension de script personnalisé qui extrait le script auquel vous avez fait référence lors du déploiement du modèle de script.

* NSGwithCustomScript

Le runbook NSGwithCustomScript attribue des adresses IP publiques à chaque machine virtuelle au sein du plan de récupération, installe un script personnalisé à l’aide d’une extension et connecte les cartes réseau virtuelles à un groupe de sécurité réseau, ce qui autorise par défaut des communications entrantes et sortantes pour l’accès à distance.

## <a name="additional-resources"></a>Ressources supplémentaires
[Compte d’identification du service Azure Automation](../automation/automation-sec-configure-azure-runas-account.md)

[Vue d’ensemble de Microsoft Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "Vue d’ensemble de Microsoft Azure Automation")

[Exemples de scripts Microsoft Azure Automation](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Exemples de scripts Microsoft Azure Automation")



<!--HONumber=Nov16_HO3-->


