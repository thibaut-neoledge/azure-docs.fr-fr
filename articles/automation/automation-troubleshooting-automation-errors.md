<properties
   pageTitle="Conseils de dépannage pour les erreurs courantes dans Azure Automation| Microsoft Azure"
   description="Cet article fournit des procédures de dépannage de base servant à résoudre les erreurs courantes que vous avez peut-être atteintes au moment de travailler avec Azure Automation."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/02/2016"
   ms.author="sngun; v-reagie"/>

# Conseils de dépannage pour les erreurs courantes dans Azure Automation

Cet article explique certaines des erreurs courantes que vous pouvez rencontrer lorsque vous travaillez avec Azure Automation et suggère d’éventuelles corrections.

## Résoudre les erreurs d’authentification lorsque vous travaillez avec des runbooks Azure Automation  

### Scénario : la connexion à un compte Azure a échoué

**Erreur :** vous recevez l’erreur « Unknown\_user\_type : Type d’utilisateur inconnu » lorsque vous travaillez avec les applets de commande Add-AzureAccount ou AzureRmAccount de connexion.

**Raison de l'erreur :** cette erreur se produit si le nom d’actif des informations d'identification n'est pas valide ou si le nom d'utilisateur et le mot de passe que vous avez utilisés pour configurer l’actif des informations d'identification Automation ne sont pas valides.

**Conseils de dépannage :** pour déterminer quel est le problème, procédez comme suit :

1. Assurez-vous qu’il n’existe aucun caractère spécial, notamment le caractère **@** dans le nom d’actif des informations d’identification d’automatisation que vous utilisez pour vous connecter à Azure.  

2. Vérifiez que vous pouvez utiliser le nom d’utilisateur et le mot de passe stockés dans les informations d’identification Azure Automation dans votre éditeur PowerShell ISE local. Vous pouvez le faire en exécutant les applets de commande suivantes dans PowerShell ISE :

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Si l’authentification échoue localement, cela signifie que vous n’avez pas correctement configuré vos informations d’identification Azure Active Directory. Reportez-vous au billet de blog [Authentification auprès d’Azure à l’aide d’Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) pour configurer correctement Azure Active Directory.


### Scénario : Impossible de trouver l’abonnement Azure

**Erreur :** vous recevez l’erreur « L’abonnement ``<subscription name>`` est introuvable » lorsque vous travaillez avec les applets de commande Select-AzureSubscription ou sélectionnez-AzureRmSubscription.

**Raison de l’erreur :** cette erreur se produit si le nom de l’abonnement n’est pas valide ou si l’utilisateur Azure Active Directory qui essaie d’obtenir les détails de l’abonnement n’est pas configuré en tant qu’administrateur de l’abonnement.

**Conseils de dépannage :** pour déterminer si vous vous êtes correctement authentifié dans Azure et avez accès à l’abonnement que vous essayez de sélectionner, procédez comme suit :

1. Assurez-vous d’exécuter l’applet de commande **Add-AzureAccount** avant d’exécuter l’applet de commande **Select-AzureSubscription**.  

2. Si vous le message d’erreur persiste, modifiez votre code en ajoutant l’applet de commande **Get-AzureSubscription** à la suite de l’applet de commande **Add-AzureAccount**, puis exécutez le code. Maintenant, vérifiez si la sortie de Get-AzureSubscription contient les détails de votre abonnement.
    * Si la sortie ne contient aucun des détails de l’abonnement, vous pouvez en conclure que l’abonnement n’est pas encore initialisé.  
    * Si vous ne voyez pas les détails de l’abonnement dans la sortie, vérifiez que vous utilisez le bon nom d’abonnement ou le bon identificateur avec l’applet de commande **Select-AzureSubscription**.   


### Scénario : L’authentification auprès d’Azure a échoué car l’authentification multifacteur est activée.

**Erreur :** vous recevez l’erreur « Add-AzureAccount : AADSTS50079 : une inscription de l’authentification forte (résistante) est requise » pendant une authentification dans Azure avec votre nom d’utilisateur et votre mot de passe Azure.

**Motif de l’erreur :** si votre compte Azure est doté de l’authentification multifacteur, vous ne pouvez pas vous authentifier dans Azure avec un utilisateur Azure Active Directory. Au lieu de cela, vous devez utiliser un certificat ou un principal de service pour l’authentification dans Azure.

**Conseils de dépannage :** pour utiliser un certificat avec les applets de commande de gestion des services Azure, reportez-vous à [Création et ajout d’un certificat de gestion des services Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pour utiliser un principal du service avec les applets de commande Azure Resource Manager, consultez [Création du principal du service à l’aide du portail Azure](./resource-group-create-service-principal-portal.md) et [Authentification d’un principal du service à l’aide d’Azure Resource Manager](./resource-group-authenticate-service-principal.md).


## Dépannage d’erreurs communes lors de l’utilisation de runbooks

### Scénario : échec du runbook en raison d’un objet désérialisé

**Erreur :** votre runbook a échoué en émettant l’erreur « Impossible de lier le paramètre ``<ParameterName>``. Impossible de convertir la valeur ``<ParameterType>`` du type désérialisé ``<ParameterType>`` en type ``<ParameterType>`` ».

**Motif de l’erreur :** si votre runbook est un flux de travail PowerShell, il stocke des objets complexes au format désérialisé afin de conserver l’état du runbook si le flux de travail est suspendu.

**Conseils de dépannage :**

L’une des trois solutions qui suivent pour corriger ce problème :

1. Si vous transférez des objets complexes d’une applet de commande vers une autre, encapsulez ces applets de commande dans un bloc InlineScript.  
2. Transmettez le nom ou la valeur dont vous avez besoin depuis l’objet complexe au lieu de transmettre la totalité de l’objet.  

3. Utilisez un runbook PowerShell au lieu d’un runbook Workflow PowerShell.


### Scénario : le travail de Runbook a échoué car le quota alloué dépassé

**Erreur :** votre travail de runbook a échoué en émettant l’erreur « Le quota mensuel de durée d’exécution totale pour cet abonnement a été atteint ».

**Motif de l’erreur :** cette erreur se produit quand l’exécution du travail dépasse le quota disponible de 500 minutes gratuit pour votre compte. Ce quota s’applique à tous les types de tâches d’exécution de travail, notamment un le test d’un travail, le démarrage d’un travail à partir du portail, l’exécution d’un travail à l’aide de webhooks et la planification d’un travail à exécuter en utilisant le portail Azure ou dans votre centre de données. Pour en savoir plus sur la tarification d’Automation, consultez [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/).

**Conseils de dépannage :** si vous souhaitez utiliser plus de 500 minutes de traitement par mois, vous devez modifier votre abonnement du niveau Gratuit vers le niveau De base. Une mise à niveau vers le niveau de base s’effectue de la façon suivante :

1. Connectez-vous à votre abonnement Azure :  
2. Sélectionnez le compte Automation que vous souhaitez mettre à niveau  
3. Cliquez sur **Paramètres** > **Niveau tarifaire et utilisation** > **Niveau tarifaire**.  
4. Dans le panneau **Choisir votre niveau tarifaire**, sélectionnez **De base**    


### Scénario : l’applet de commande n’est pas reconnue lors de l’exécution d’un runbook

**Erreur :** le travail de runbook échoue, en renvoyant l’erreur « ``<cmdlet name>`` : Le terme ``<cmdlet name>`` n’est pas reconnu comme nom d’applet de commande, fonction, fichier de script ou programme exécutable. »

**Motif de l’erreur :** cette erreur survient quand le moteur PowerShell ne trouve pas l’applet de commande que vous utilisez dans votre runbook. Cela peut être dû au fait que le module contenant l’applet de commande n’est pas présent dans le compte, qu’il existe un conflit de nom avec un nom de runbook ou que l’applet de commande existe dans un autre module et Automation ne peut pas résoudre le nom.

**Conseils de dépannage :** l’une des solutions qui suivent peut corriger ce problème :

- Vérifiez que vous avez correctement saisi le nom de l’applet de commande et vérifiez que le chemin d’accès à l’applet de commande est correct.  

- Assurez-vous que l’applet de commande existe dans votre compte Automation et qu’il n’y a aucun conflit. Pour vérifier si l’applet de commande est présent, ouvrez un runbook en mode édition et recherchez l’applet de commande que vous souhaitez trouver dans la bibliothèque ou exécutez **Get-Command ``<CommandName>``**. Une fois que vous avez vérifié que l’applet de commande est disponible pour le compte, et qu’il n’existe aucun conflit de nom avec d’autres applets de commande ou runbooks, ajoutez-la à la zone de dessin et assurez-vous que vous utilisez un paramètre valide défini dans votre runbook.

- Si vous rencontrez un conflit de noms et si l’applet de commande est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom qualifié complet de l’applet de commande. Vous pouvez par exemple utiliser **nom\_module\\nom\_applet\_de\_commande**.

- Si vous exécutez le runbook localement dans un groupe de Workers hybride, assurez-vous que le module/applet de commande est installé sur l'ordinateur qui héberge le Worker hybride.


### Scénario : Un runbook de longue durée échoue systématiquement avec l’exception : « La tâche ne peut pas continuer, car elle a été exclue à plusieurs reprises du même point de contrôle ».

**Raison de l’erreur :** Il s’agit du comportement par défaut lié à la surveillance de la répartition de charge équilibrée des processus au sein d’Azure Automation, qui suspend automatiquement un runbook s’il s’exécute pendant plus de 3 heures. Toutefois, le message d’erreur ne fournit pas d’options pour la suite des événements. Un runbook peut être interrompu pour plusieurs raisons. Les suspensions sont généralement dues à des erreurs. Par exemple, une exception non interceptée dans un runbook, une panne réseau ou une panne sur le Runbook Worker qui exécute le runbook entraîne la suspension de ce dernier et son démarrage à partir de son dernier point de contrôle au moment de la reprise.

**Conseils de dépannage :** La solution documentée pour éviter ce problème consiste à utiliser des points de contrôle dans un flux de travail. Pour en savoir plus, consultez [Découverte des flux de travail PowerShell](automation-powershell-workflow.md#Checkpoints). Vous trouverez une explication plus approfondie de la répartition de charge équilibrée dans le billet de blog [Using Checkpoints in Runbooks](https://azure.microsoft.com/fr-FR/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).


## Résoudre les erreurs courantes survenant lors de l’importation des modules

### Scénario : le module ne parvient pas à terminer l’importation ou il est impossible d’exécuter des applets de commande après l’importation

**Erreur :** un module ne parvient pas à importer ou réussit l’importation, mais aucune applet de commande n’est extraite.

**Motif de l’erreur :** quelques raisons communes pour lesquelles un module peut échouer dans l’importation vers Azure Automation :

- La structure ne correspond pas à la structure dont Automation a besoin.  

- Le module dépend d’un autre module qui n’a pas été déployé sur votre compte Automation.

- Le module n’a pas de dépendances dans le dossier.

- L’applet de commande **New-AzureRmAutomationModule** est utilisée pour charger le module, et vous ne disposez pas du chemin d’accès de stockage complet ou n’avez pas chargé le module en utilisant une URL accessible publiquement.

**Conseils de dépannage :**

l’une des solutions qui suivent peut corriger ce problème :

- Vérifiez que le module respecte le format suivant : nom\_module.Zip **->** nom\_module ou numéro de version **->** (nom\_module.psm1, nom\_module.psd1)

- Ouvrez le fichier .psd1 et regardez si le module possède des dépendances. Si c’est le cas, téléchargez ces modules dans le compte Automation.

- Assurez-vous que les fichiers .dll référencés sont présents dans le dossier de module.


## Résoudre les erreurs courantes en travaillant avec la Configuration d’état souhaité (DSC)  

### Scénario : le nœud est en état d’échec avec une erreur « Introuvable »

**Erreur :** un rapport associé au nœud indique l’état **Échec** et contient l’erreur « The attempt to get the action from server https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction failed because a valid configuration ``<guid>`` cannot be found. » (« La tentative d’obtention de l’action depuis le serveur a échoué, car aucune configuration valide n’a été trouvée »).

**Motif de l’erreur :** cette erreur se produit généralement quand le nœud est affecté à un nom de configuration (par exemple, ABC) et non à un nom de configuration de nœud (par exemple, ABC.ServeurWeb).

**Conseils de dépannage :**

- Assurez-vous d'affecter le nœud avec « nom de configuration de nœud » et non pas le « nom de configuration ».  

- Vous pouvez affecter une configuration de nœud à un nœud à l'aide du portail Azure ou d’une applet de commande PowerShell.
    - Pour affecter une configuration de nœud à un nœud à l’aide du portail Azure, ouvrez le panneau **Nœuds DSC**, sélectionnez un nœud, puis cliquez sur le bouton **Affecter la configuration de nœud**.  
    - Pour affecter une configuration de nœud à un nœud à l’aide d’une applet de commande PowerShell, utilisez l’applet de commande **Set-AzureRmAutomationDscNode**.


### Scénario : aucune configuration de nœud (fichier mof) ne s’est produite au cours d’une compilation de configuration

**Erreur :** le travail de compilation de votre DSC a été interrompu avec l’erreur : « Compilation completed successfully, but no node configuration .mofs were generated » (« La compilation s’est terminée correctement, mais aucune configuration de nœud .mofs n’a été générée »).

**Motif de l’erreur :** quand l’expression qui suit le mot-clé **Node** dans la configuration DSC est $null, aucune configuration de nœud ne peut être générée.

**Conseils de dépannage :** l’une des solutions qui suivent peut corriger ce problème :

- Vérifiez que l’expression en regard du mot clé **Node** dans la définition de la configuration n’est pas $null.  
- Si vous effectuez une transmission ConfigurationData pendant la compilation de la configuration, vérifiez que vous transmettez les valeurs attendues requises par la configuration depuis [ConfigurationData](automation-dsc-compile.md#configurationdata).


### Scénario : le rapport du nœud DSC se bloque à l’état « en cours »

**Erreur :** l’agent DSC génère le message « No instance found with given property values » (« Aucune instance n’a été trouvée selon les valeurs de propriété données »).

**Motif de l’erreur :** vous avez mis à niveau votre version WMF et endommagé WMI.

**Conseils de dépannage :** pour résoudre ce problème, suivez les instructions du billet de blog [Problèmes connus et limitations DSC](https://msdn.microsoft.com/powershell/wmf/limitation_dsc).


### Scénario : il est impossible d’utiliser des informations d’identification dans une configuration DSC

**Erreur :** le travail de compilation de votre DSC a été interrompu avec l’erreur : « System.InvalidOperationException error processing property 'Credential' of type ``<some resource name>``: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true » (« Erreur System.InvalidOperationException pendant le traitement de la propriété 'Credential' de type <nom de ressource> : La conversion et le stockage en texte brut d’un mot de passe chiffré ne sont autorisés que si PSDscAllowPlainTextPassword est défini sur true »).

**Motif de l’erreur :** Vous avez utilisé des informations d’identification dans une configuration, mais n’avez pas fourni la bonne **ConfigurationData** pour définir **PSDscAllowPlainTextPassword** sur true pour chaque configuration de nœud.

**Conseils de dépannage :**
- Assurez-vous de transmettre la bonne **ConfigurationData** pour définir **PSDscAllowPlainTextPassword** sur true pour chaque configuration de nœud mentionnée dans la configuration. Pour plus d’informations, consultez les [ressources d’Azure Automation DSC](automation-dsc-compile.md#assets).


## Étapes suivantes

Si vous avez suivi les étapes de dépannage ci-dessus et avez besoin d’aide à un moment quelconque de cet article, vous pouvez :

- Obtenir l’aide des experts Azure. Soumettre votre problème sur les forums [MSDN Azure ou Stack Overflow.](https://azure.microsoft.com/support/forums/)

- Signaler un incident au support Azure Accédez au [site de support Azure](https://azure.microsoft.com/support/options/) et cliquez sur **Obtenir un support** dans la section **Support technique et sur la facturation**.

- Publier une demande de script sur le [Centre de scripts](https://azure.microsoft.com/documentation/scripts/), si vous recherchez une solution de runbook Azure Automation ou un module d’intégration.

- Publier vos commentaires ou vos demandes de fonctionnalités pour Azure Automation sur [User Voice](https://feedback.azure.com/forums/34192--general-feedback).

<!-------HONumber=AcomDC_0302_2016-->