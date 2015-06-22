<properties 
   pageTitle="Concepts de Runbook Azure Automation"
   description="Décrit les concepts de base que vous devez comprendre pour créer des Runbooks dans Azure Automation."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Concepts de Runbook Azure Automation

Les Runbooks d'Azure Automation sont implémentés en tant que workflows Windows PowerShell. Cette section fournit une brève présentation des principales fonctionnalités de workflow communes aux Runbooks Automation. Plus d'informations sur les workflows sont disponibles dans [Présentation du workflow Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).


## Workflows Windows PowerShell

Un workflow est une séquence d'étapes liées et programmées qui permet d'effectuer des tâches longues ou nécessitant la coordination de plusieurs phases entre plusieurs appareils ou nœuds gérés. Les avantages d'un workflow par rapport à un script normal incluent la possibilité d'exécuter simultanément une action sur plusieurs appareils et de récupérer automatiquement après une défaillance. Un workflow Windows PowerShell est un script Windows PowerShell qui tire parti de Windows Workflow Foundation. Le workflow est écrit avec la syntaxe Windows PowerShell et lancé par Windows PowerShell, mais il est traité par Windows Workflow Foundation.

### Structure de base

Un workflow Windows PowerShell commence par le mot clé **Workflow** suivi du corps du script entre accolades. Le nom du workflow suit le mot clé **Workflow**, comme illustré dans la syntaxe suivante. Le nom du workflow correspond au nom du Runbook Automation.

    Workflow Test-Runbook
    {
       <Commands>
    }

Pour ajouter des paramètres au workflow, utilisez le mot clé **Param**, comme illustré dans la syntaxe suivante. Le portail de gestion invite l'utilisateur à fournir des valeurs pour ces paramètres lorsqu'il démarre le Runbook. Cet exemple utilise l'attribut facultatif Parameter qui spécifie si le paramètre est obligatoire ou non.

    Workflow Test-Runbook
    {
      Param
      (
       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>,

       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>
      )
      <Commands>
    }

### Dénomination

Le nom du workflow doit être conforme au format verbe-nom standard de Windows PowerShell. Vous pouvez consulter [Verbes approuvés pour les commandes Windows PowerShell](http://msdn.microsoft.com/library/windows/desktop/ms714428(v=vs.85).aspx) pour obtenir la liste des verbes approuvés à utiliser. Le nom du workflow doit correspondre au nom du Runbook Automation. Si le Runbook est importé, le nom de fichier doit correspondre au nom du workflow et se terminer par .ps1.

### Limitations

Pour obtenir la liste complète des limitations et des différences de syntaxe entre les workflows Windows PowerShell et Windows PowerShell, consultez [Différences de syntaxe entre les workflows de script et les scripts](http://technet.microsoft.com/library/jj574140.aspx).

## Activités

Une activité est une tâche spécifique dans un workflow. Tout comme un script se compose d'une ou de plusieurs commandes, un workflow se compose d'une ou de plusieurs activités exécutées en séquence. Le workflow Windows PowerShell convertit automatiquement la plupart des applets de commande Windows PowerShell en activités lors de son exécution. Lorsque vous spécifiez une de ces applets de commande dans votre Runbook, l'activité correspondante est de fait exécutée par Windows Workflow Foundation. Pour ces applets de commande sans activité correspondante, le workflow Windows PowerShell exécute automatiquement l'applet de commande au sein d'une activité [InlineScript](#inlinescript). Il existe un ensemble d'applets de commande qui sont exclues et ne peuvent pas être utilisées dans un workflow, à moins que vous ne les incluiez explicitement dans un bloc InlineScript. Pour plus d'informations sur ces concepts, consultez [Utilisation des activités dans les workflows de script](http://technet.microsoft.com/library/jj574194.aspx).

Les activités de workflow partagent un ensemble de paramètres communs pour configurer leur opération. Pour plus d'informations sur les paramètres communs de workflow, consultez [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

## Modules d'intégration

Un *module d'intégration* est un package qui contient un module Windows PowerShell et qui peut être importé dans Azure Automation. Les applets de commande des modules d'intégration qui sont importés dans Azure Automation sont automatiquement disponibles pour tous les Runbooks du même compte Automation. Azure Automation étant basé sur Windows PowerShell 4.0, il gère le chargement automatique des modules, ce qui signifie que les applets de commande des modules installés peuvent être utilisées sans avoir à être importées dans le script avec [Import-Module](http://technet.microsoft.com/library/hh849725.aspx).

## Exécution en parallèle

L'un des avantages des workflows Windows PowerShell est la possibilité d'exécuter un ensemble de commandes en parallèle, et non séquentiellement comme avec un script classique. Cela est particulièrement utile dans les Runbooks dans la mesure où ils peuvent effectuer plusieurs actions longues à s'exécuter. Par exemple, un Runbook peut approvisionner un ensemble de machines virtuelles. Au lieu d'effectuer chaque processus de déploiement en séquence avec un autre, les actions peuvent s'effectuer simultanément, ce qui accroît l'efficacité globale. Le Runbook ne se poursuit qu'une fois qu'elles sont toutes terminées.

Vous pouvez utiliser le mot clé **Parallel** pour créer un bloc de script avec plusieurs commandes qui s'exécutent simultanément. Le script utilise la syntaxe ci-dessous. Dans ce cas, Activity1 et Activity2 démarrent en même temps. Activity3 démarre uniquement quand Activity1 et Activity2 sont toutes deux terminées.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Vous pouvez utiliser la construction **ForEach-Parallel** pour traiter simultanément les commandes de chaque élément d'une collection. Les éléments de la collection sont traités en parallèle, tandis que les commandes du bloc de script sont exécutées séquentiellement. Le script utilise la syntaxe ci-dessous. Dans ce cas, Activity1 démarre en même temps pour tous les éléments de la collection. Pour chaque élément, Activity2 démarre une fois Activity1 terminée. Activity3 démarre uniquement quand Activity1 et Activity2 sont toutes deux terminées pour tous les éléments.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Le mot clé **Sequence** est utilisé pour exécuter les commandes de manière séquentielle dans un bloc de script **Parallel**. Le bloc de script **Sequence** s'exécute en parallèle avec d'autres commandes, mais les commandes du bloc sont exécutées séquentiellement. Le script utilise la syntaxe ci-dessous. Dans ce cas, Activity1, Activity2 et Activity3 démarrent en même temps. Activity4 démarre uniquement quand Activity3 est terminée. Activity5 démarre une fois que toutes les autres activités sont terminées.

    Parallel
    {
      <Activity1>
      <Activity2>

      Sequence 
      { 
        <Activity3>  
        <Activity4>
      }

    }
    <Activity5>

## Points de contrôle

Un *point de contrôle* est un instantané de l'état actuel du workflow qui inclut la valeur actuelle des variables et toute sortie générée à ce stade. Le dernier point de contrôle à s'exécuter dans un Runbook est enregistré dans la base de données Automation, afin que le workflow puisse reprendre en cas de problème, par exemple une panne d'ordinateur pendant l'exécution. Sans point de contrôle, le workflow démarrerait depuis le début. Les données du point de contrôle sont supprimées une fois la tâche du Runbook terminée.

Vous pouvez définir un point de contrôle dans un workflow avec l'activité **Checkpoint-Workflow**. Lorsque vous incluez cette activité dans un Runbook, un point de contrôle est immédiatement créé. Si le Runbook est interrompu par une exception, lors de la reprise de la tâche, il redémarre à partir du point du dernier point de contrôle défini.

Dans l'exemple de code suivant, une exception se produit après qu'Activity2 a provoqué la suspension du Runbook. Lorsque la tâche reprend, elle commence par exécuter Activity2, juste après le dernier point de contrôle défini.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Vous devez définir les points de contrôle d'un Runbook après les activités qui peuvent être sujettes à une exception et qui ne doivent pas être réexécutées à la reprise du Runbook. Par exemple, votre Runbook peut créer une machine virtuelle. Vous pouvez définir un point de contrôle avant et après les commandes de création de la machine virtuelle. En cas d'échec de la création, les commandes sont réexécutées à la reprise du Runbook. Si la création réussit, mais que le Runbook échoue par la suite, la machine virtuelle n'est pas recréée à la reprise du Runbook.

Pour plus d'informations sur les points de contrôle, consultez [Ajout de points de contrôle à un workflow de script](http://technet.microsoft.com/library/jj574114.aspx).

## Suspension d'un workflow

Vous pouvez forcer la suspension d'un Runbook par lui-même avec l'activité **Suspend-Workflow**. Cette activité entraîne la définition d'un point de contrôle et la suspension immédiate du workflow. La suspension du workflow est utile pour les Runbooks qui peuvent nécessiter l'exécution d'une étape manuelle avant celle d'un autre ensemble d'activités.

Pour plus d'informations sur la suspension d'un workflow, consultez [Suspension d'un workflow par lui-même](http://technet.microsoft.com/library/jj574175.aspx).

## InlineScript

L'activité **InlineScript** exécute un bloc de commandes du script traditionnel PowerShell au lieu du workflow PowerShell et retourne sa sortie au workflow. Alors que les commandes d'un workflow sont envoyées à Windows Workflow Foundation pour être traitées, les commandes d'un bloc InlineScript sont traitées par Windows PowerShell. L'activité utilise les paramètres communs de workflow standard, notamment **PSCredential** qui permet de spécifier que le bloc de code est exécuté à l'aide d'autres informations d'identification.

InlineScript utilise la syntaxe ci-dessous.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Même si les activités InlineScript peuvent être critiques dans certains Runbooks, elles ne prennent pas en charge les constructions de workflow et doivent être utilisées uniquement lorsque cela est nécessaire pour les raisons suivantes :

- Vous ne pouvez pas utiliser de points de contrôle à partir d'un bloc InlineScript. Si une défaillance se produit dans le bloc, l'exécution doit reprendre depuis le début du bloc.
- InlineScript affecte l'extensibilité du Runbook puisque l'activité maintient la session Windows PowerShell pendant toute la durée du bloc InlineScript.
- Les activités, telles que Get-AutomationVariable et Get-AutomationPSCredential, ne sont pas disponibles dans un bloc InlineScript.  

Si vous n'avez pas besoin d'utiliser une activité InlineScript, vous devez réduire sa portée. Par exemple, si votre Runbook effectue une itération sur une collection lors de l'application de la même opération pour chaque élément, la boucle doit se produire en dehors d'InlineScript. Vous bénéficiez ainsi des avantages suivants :

- Vous pouvez [contrôler](#checkpoints) le workflow après chaque itération. Si la tâche est suspendue ou interrompue, puis reprise, la boucle est à même de reprendre.
- Vous pouvez utiliser **ForEach – Parallel** pour gérer simultanément les éléments de la collection.

Gardez à l'esprit les recommandations suivantes si vous utilisez une activité InlineScript dans votre Runbook :

- Vous pouvez passer des valeurs dans le script, mais à l'aide du modificateur de portée **$Using**. Par exemple, une variable appelée $abc qui a été définie en dehors d'un script InlineScript devient $using:abc à l'intérieur du script InlineScript.

- Pour retourner la sortie d'un bloc InlineScript, attribuez le résultat à une variable et sortez les données à retourner dans le flux de sortie. L'exemple suivant affecte la chaîne « Bonjour » à une variable appelée $output.

	<pre><code>$output = InlineScript {Write-Output «&#160;Bonjour&#160;»}</code></pre>

- Évitez de définir les workflows au sein de la portée d'InlineScript. Même si certains workflows peuvent sembler fonctionner correctement, il ne s'agit pas d'un scénario testé. Par conséquent, vous pouvez rencontrer des messages d'erreur déroutants ou un comportement inattendu.

Pour plus d'informations sur l'utilisation d'InlineScript, consultez [Exécution des commandes Windows PowerShell dans un workflow](http://technet.microsoft.com/library/jj574197.aspx) et [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## Articles connexes

- [Création ou importation d'un Runbook](http://technet.microsoft.com/library/dn919921.aspx)

<!---HONumber=58--> 