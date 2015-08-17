
<properties
	pageTitle="Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch"
	description="La mise à l’échelle automatique s’effectue par l’activation de cette dernière sur un pool et par l’association d’une formule au pool afin de calculer le nombre de nœuds de calcul nécessaires pour traiter l’application."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>


<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/21/2015"
	ms.author="davidmu"/>


# Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch

La mise à l’échelle automatique des nœuds de calcul dans un pool Azure Batch est un ajustement dynamique de la puissance de traitement utilisée par votre application. Cette facilité d’ajustement vous offre un gain de temps et d’argent. Pour plus d’informations sur les nœuds de calcul et sur les pools, voir l’article [Vue d’ensemble technique d’Azure Batch](batch-technical-overview.md).

La mise à l’échelle automatique se produit lorsqu’elle est activée sur un pool et qu’une formule est associée à ce pool. Cette formule permet de déterminer le nombre de nœuds de calcul nécessaires pour traiter l’application. La mise à l’échelle automatique peut être définie au moment de la création d’un pool ou par la suite sur un pool existant. Il est également possible de mettre à jour la formule associée à un pool sur lequel la mise à l’échelle automatique a été activée.

Quand la mise à l’échelle automatique est activée, le nombre de nœuds de calcul disponibles est ajusté toutes les 15 minutes en fonction de la formule. La formule agit sur des échantillons qui sont collectés toutes les 5 secondes, mais il existe un délai de 75 secondes entre le moment où un échantillon est collecté et celui où il devient accessible par la formule. Ces facteurs de temps doivent être pris en compte lors de l’utilisation de la méthode GetSample décrite ci-après.

Il est recommandé d’évaluer systématiquement la formule avant de l’affecter à un pool, et il est important de surveiller l’état des exécutions de mise à l’échelle automatique.

> [AZURE.NOTE]Chaque compte Azure Batch est limité à un nombre maximal de nœuds de calcul utilisables pour le traitement. Le système crée les nœuds à hauteur de cette limite et peut donc ne pas atteindre les nombres cibles spécifiés par une formule.

## Définir la formule

La formule que vous définissez sert à déterminer le nombre de nœuds de calcul disponibles dans le pool pour le prochain intervalle de traitement. La formule est une chaîne dont la taille ne peut pas dépasser 8 Ko et qui peut inclure jusqu’à 100 instructions séparées par des points-virgules.

Les instructions d’une formule sont des expressions au format libre. Elles peuvent inclure des variables définies par le système, des variables définies par l’utilisateur, des valeurs de constante et des opérations prises en charge sur ces variables ou constantes :

	VAR = Function(System defined variables, user-defined variables);

Vous pouvez combiner des variables pour créer des formules complexes :

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### Variables

Une formule peut utiliser des variables définies par le système et des variables définies par l’utilisateur. Vous pouvez spécifier la valeur de ces variables définies par le système pour gérer les nœuds de calcul dans un pool.

<table>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Nombre cible de nœuds de calcul dédiés pour le pool. Cette valeur est modifiable en fonction de l’utilisation réelle pour le traitement des tâches.</td>
  </tr>
  <tr>
    <td>$TVMDeallocationOption</td>
    <td>Action exécutée lorsque des nœuds de calcul sont supprimés d’un pool. Les valeurs possibles sont les suivantes&#160;:
      <br/>

      <ul>
        <li><p><b>requeue</b>&#160;: arrêter immédiatement les tâches et les replacer dans la file d’attente des travaux pour qu’elles soient replanifiées.</p></li>
        <li><p><b>terminate</b>&#160;: arrêter immédiatement les tâches et les supprimer de la file d’attente des travaux.</p></li>
        <li><p><b>taskcompletion</b>&#160;: attendre la fin des tâches en cours d’exécution, puis supprimer le nœud du pool.</p></li>
        <li><p><b>retaineddata</b>&#160;: attendre que toutes les données de tâche locales conservées sur le nœud aient été nettoyées avant de supprimer le nœud du pool.</p></li>
      </ul></td>
   </tr>
</table>

Vous pouvez uniquement lire les valeurs des variables définies par le système ci-après pour effectuer des ajustements basés sur les métriques à partir des nœuds de calcul dans l’échantillon.

<table>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>Pourcentage moyen d’utilisation du processeur</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>Nombre de secondes consommées</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>Nombre moyen de mégaoctets utilisés</td>
  <tr>
    <td>$DiskBytes</td>
    <td>Nombre moyen de gigaoctets utilisés sur les disques locaux</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>Nombre d’octets lus</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>Nombre d’octets écrits</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>Nombre d’opérations de lecture sur disque effectuées</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>Nombre d’opérations d’écriture sur disque effectuées</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>Nombre d’octets entrants</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>Nombre d’octets sortants</td>
  </tr>
  <tr>
    <td>$SampleTVMCount</td>
    <td>Nombre de nœuds de calcul</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>Nombre de tâches à l’état actif</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>Nombre de tâches en cours d’exécution</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>Nombre de tâches ayant abouti</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>Nombre de tâches ayant échoué</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>Nombre actuel de nœuds de calcul dédiés</td>
  </tr>
</table>

### Types

Les types pris en charge dans une formule sont les suivants :

- double
- doubleVec
- string
- timestamp
- timeinterval
	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

### Opérations

Les opérations autorisées sur les types répertoriés ci-dessus sont les suivantes :

<table>
  <tr>
    <th>Opération</th>
    <th>Opérateurs autorisés</th>
  </tr>
  <tr>
    <td>double &lt;opérateur> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;opérateur> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;opérateur> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;opérateur> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;opérateur> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;opérateur> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;opérateur> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;opérateur> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;opérateur> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;opérateur>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;opérateur>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;opérateur> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;opérateur> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;opérateur> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;opérateur> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;opérateur> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>test double only (valeur différente de zéro = vrai, zéro = faux)</td>
    <td>? :</td>
  </tr>
</table>

### Fonctions

Les fonctions prédéfinies disponibles pour la définition d’une formule de mise à l’échelle automatique sont les suivantes :

<table>
  <tr>
    <th>Fonction</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>Valeur moyenne de toutes les valeurs de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>Longueur du vecteur créé à partir de l’élément doubleVecList.</td>
  <tr>
    <td>double lg(double)</td>
    <td>Base logarithmique&#160;2.</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>Base logarithmique&#160;2 au niveau composant. Un élément vec(double) doit être explicitement transmis pour un seul paramètre double&#160;; sinon, le système présume l’utilisation de la version lg(double) double.</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>Logarithme naturel.</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>Base logarithmique&#160;2 au niveau composant. Un élément vec(double) doit être explicitement transmis pour un seul paramètre double&#160;; sinon, le système présume l’utilisation de la version lg(double) double.</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>Base logarithmique&#160;10.</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>Base logarithmique&#160;10 au niveau composant. Un élément vec(double) doit être explicitement transmis pour un seul paramètre double&#160;; sinon, le système présume l’utilisation de la version log(double) double.</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>Valeur maximale de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>Valeur minimale de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>Double norme du vecteur créé à partir de l’élément doubleVecList.
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>Élément de percentile du vecteur&#160;v.</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>Valeur aléatoire comprise entre 0,0 et 1,0.</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>Différence entre les valeurs minimale et maximale de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>Écart type de l’échantillon des valeurs dans l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>Arrêt de l’évaluation de l’expression de mise à l’échelle automatique.</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>Somme de tous les composants de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>Horodateur de l’heure actuelle si aucun paramètre n’est transmis, ou horodateur de la chaîne dateTime dans le cas contraire. Les formats dateTime pris en charge sont W3CDTF et RFC1123.</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>Valeur de l’élément à l’emplacement&#160;i du vecteur&#160;v avec un index de départ de zéro.</td>
  </tr>
  <tr>
    <td>doubleVec vec(doubleVecList)</td>
    <td>Création explicite d’un seul paramètre doubleVec à partir de l’élément doubleVecList.</td>
  </tr>
</table>

Certaines des fonctions décrites dans le tableau peuvent accepter une liste en tant qu’argument. La liste séparée par des virgules constitue n’importe quelle combinaison de paramètres double et doubleVec. Par exemple :

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

La valeur doubleVecList est convertie en un seul paramètre doubleVec avant l’évaluation. Par exemple, si v = [1,2,3\], l’appel d’avg(v) équivaut à appeler avg(1,2,3), et l’appel d’avg(v, 7) équivaut à appeler avg(1,2,3,7).

### Exemples de données

Les variables définies par le système sont des objets qui fournissent des méthodes pour accéder aux données associées. Par exemple, l’expression ci-après présente une requête visant à obtenir les cinq dernières minutes de l’utilisation du processeur :

	$CPUPercent.GetSample(TimeInterval_Minute*5)

Ces méthodes sont ensuite utilisables pour obtenir des échantillons de données.

<table>
  <tr>
    <th>Méthode</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>Renvoie le nombre total d’échantillons dans l’historique des métriques.</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>Renvoie un vecteur d’échantillons de données. Par exemple&#160;:</p>
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>&#160;: spécifie le nombre d’échantillons requis à partir des échantillons les plus récents.</p>
				  <p>Un échantillon correspond à 5&#160;secondes de données de métrique. GetSample(1) renvoie le dernier échantillon disponible, mais pour les métriques comme $CPUPercent, vous ne devez pas utiliser cette méthode, car il est impossible de déterminer le moment où l’échantillon a été collecté. Il peut s’agir d’un événement récent ou plus ancien en raison de problèmes système. Il est préférable d’utiliser un intervalle de temps, comme indiqué ci-dessous.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>&#160;: indique un délai d’exécution pour la collecte des échantillons de données et spécifie en option le pourcentage d’échantillons devant correspondre à la plage demandée.</p>
          <p>$CPUPercent.GetSample(TimeInterval\_Minute*10) doit renvoyer 200&#160;échantillons si tous les échantillons des dix dernières minutes sont présents dans l’historique CPUPercent. Si la dernière minute de l’historique n’est pas présente, seuls 180&#160;échantillons sont renvoyés.</p>
					<p>$CPUPercent.GetSample(TimeInterval\_Minute*10, 80) aboutit, et $CPUPercent.GetSample(TimeInterval_Minute*10,95) échoue.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>&#160;: spécifie un délai d’exécution pour la collecte des données avec une heure de début et une heure de fin.</p></li></ul></td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Renvoie la période des échantillons considérés dans un jeu de données d’échantillon historiques.</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Renvoie l’horodateur du plus ancien échantillon de données disponible pour le métrique.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Renvoie le pourcentage d’échantillons qu’un historique comprend pour un intervalle de temps donné. Par exemple&#160;:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>&#160;: étant donné que la méthode GetSample échoue si le pourcentage d’échantillons renvoyé est inférieur à la valeur samplePercent indiquée, vous pouvez utiliser les méthodes GetSamplePercent pour commencer par vérifier ce point, puis exécuter une autre action si le nombre d’échantillons présents est insuffisant sans interrompre leur évaluation de mise à l’échelle automatique.</p></td>
  </tr>
</table>

### Mesures

Les métriques définissables dans une formule sont les suivants :

<table>
  <tr>
    <th>Mesure</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Ressource</td>
    <td><p>Basée sur l’utilisation du processeur, de la bande passante et de la mémoire, ainsi que sur le nombre de nœuds de calcul. Les variables système ci-dessous et décrites au début de cet article sont utilisées dans les formules pour gérer les nœuds de calcul dans un pool&#160;:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$TVMDeallocationOption</li>
    </ul></p>
    <p>Les variables système ci-après sont utilisées pour effectuer des ajustements en fonction des métriques de nœud&#160;:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
    <p>Cet exemple présente une formule permettant de définir le nombre de nœuds de calcul dans le pool sur 110&#160;% du nombre cible actuel de nœuds si l’utilisation moyenne minimale du processeur des 10&#160;dernières minutes est supérieure à 70&#160;%&#160;:</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval\_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;</b></p>
    <p>Cet exemple présente une formule permettant de définir le nombre de nœuds de calcul dans le pool sur 90&#160;% du nombre cible actuel de nœuds si l’utilisation moyenne du processeur des 60&#160;dernières minutes est inférieure à 20&#160;%&#160;:</p>
    <p><b>totalTVMs = (avg($CPUPercent.GetSample(TimeInterval\_Minute*60)) &lt; 0.2) ? ($CurrentDedicated * 0.9) : totalTVMs;</b></p>
    <p>Cet exemple définit le nombre cible de nœuds de calcul dédiés sur un maximum de 400&#160;:</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>Task</td>
    <td><p>Basée sur l’état des tâches (Active, En attente et Terminée).</p>
    <p>Les variables système ci-après sont utilisées pour effectuer des ajustements en fonction des métriques de tâche&#160;:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>Cet exemple présente une formule qui détecte si 70&#160;% des échantillons ont été enregistrés au cours des 15&#160;dernières minutes. Si ce n’est pas le cas, l’exemple utilise le dernier échantillon. Il essaie d’augmenter le nombre de nœuds de calcul pour le faire correspondre au nombre de tâches actives, avec un maximum de 3. Il définit le nombre de nœuds sur un quart du nombre de tâches actives, car la propriété MaxTasksPerVM du pool est définie sur 4. Il définit également l’option Deallocation sur «&#160;taskcompletion&#160;» pour conserver la machine jusqu’à ce que les tâches soient terminées.</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval\_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval\_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $TVMDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## Évaluer la formule de mise à l’échelle automatique

Il est recommandé d’évaluer systématiquement une formule avant de l’utiliser dans votre application. La formule est évaluée par le biais de l’exécution d’une série de tests sur un pool existant. Pour effectuer cette opération, utilisez l’une des méthodes suivantes :

- [Méthode IPoolManager.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/dn931617.aspx) ou [méthode IPoolManager.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/dn931545.aspx) : ces méthodes .NET nécessitent le nom d’un pool existant et la chaîne qui contient la formule de mise à l’échelle automatique. Les résultats de l’appel sont contenus dans une instance de la [classe AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Évaluation d’une formule de mise à l’échelle automatique](https://msdn.microsoft.com/library/azure/dn820183.aspx) : dans cette opération REST, le nom du pool est spécifié dans l’URI, et la formule de mise à l’échelle automatique est spécifiée dans l’élément autoScaleFormula du corps de la requête. La réponse de l’opération contient les éventuelles informations d’erreur associées à la formule.

## Créer un pool en activant la mise à l’échelle automatique sur ce dernier

Créez un pool de l’une des manières suivantes :

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) : cette applet de commande utilise le paramètre AutoScaleFormula pour spécifier la formule de mise à l’échelle automatique.
- [Méthode IPoolManager.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx) : une fois que cette méthode .NET a été appelée pour créer un pool, la [propriété ICloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx) et la [propriété ICloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx) sont définies sur le pool pour activer la mise à l’échelle automatique.
- [Ajout d’un pool à un compte](https://msdn.microsoft.com/library/azure/dn820174.aspx) : les éléments enableAutoScale et autoScaleFormula sont utilisés dans cette API REST afin de configurer la mise à l’échelle automatique pour le pool lors de la création de ce dernier.

> [AZURE.NOTE]Si vous configurez la mise à l’échelle automatique lorsque vous créez le pool à l’aide des ressources mentionnées ci-dessus, le paramètre targetDedicated relatif au pool n’est pas utilisé.

## Activer la mise à l’échelle automatique après la création d’un pool

Si vous avez déjà configuré un pool avec un nombre de nœuds de calcul spécifié à l’aide du paramètre targetDedicated, vous pouvez mettre à jour ce pool par la suite afin d’y activer la mise à l’échelle automatique. Pour effectuer cette opération, utilisez l’une des méthodes suivantes :

- [Méthode IPoolManager.EnableAutoScale](https://msdn.microsoft.com/library/azure/dn931709.aspx) : cette méthode .NET nécessite le nom du pool existant et la formule de mise à l’échelle automatique.
- [Activation/désactivation de la mise à l’échelle automatique](https://msdn.microsoft.com/library/azure/dn820173.aspx) : cette API REST nécessite le nom du pool existant dans l’URI et la formule de mise à l’échelle automatique dans le corps de la requête.

> [AZURE.NOTE]La valeur spécifiée pour le paramètre targetDedicated au moment de la création du pool est ignorée lorsque la formule de mise à l’échelle automatique est évaluée.

## Obtenir des informations sur les exécutions de mise à l’échelle automatique

Vous devez vérifier régulièrement les résultats des exécutions de mise à l’échelle automatique. Pour effectuer cette opération, utilisez l’une des méthodes suivantes :

- [Propriété ICloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx) : lors de l’utilisation de la bibliothèque .NET, cette propriété d’un pool fournit une instance de la [classe AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx), qui fournit une [propriété AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx), une [propriété AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx) et une [propriété AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx).
- [Obtention d’informations sur un pool](https://msdn.microsoft.com/library/dn820165.aspx) : cette API REST renvoie des informations sur le pool, y compris la dernière exécution de mise à l’échelle automatique.

## Étapes suivantes

1.	Vous pouvez avoir besoin d’accéder au nœud de calcul pour parvenir à évaluer intégralement l’efficacité de votre application. Pour tirer parti de l’accès à distance, vous devez ajouter un compte d’utilisateur au nœud de calcul auquel vous souhaitez accéder, et récupérer un fichier RDP à partir de ce nœud. Ajoutez le compte d’utilisateur de l’une des manières suivantes :

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) : cette applet de commande utilise le nom du pool, le nom du nœud de calcul, ainsi que le nom et le mot de passe du compte en tant que paramètres.
	- [Méthode IVM.CreateUser](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx) : cette méthode .NET crée une instance de l’[interface IUser](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx) sur laquelle le nom et le mot de passe du compte peuvent être définis pour le nœud de calcul.
	- [Ajout d’un compte d’utilisateur à un nœud](https://msdn.microsoft.com/library/dn820137.aspx) : le nom du pool et le nœud de calcul sont spécifiés dans l’URI, et le nom et le mot de passe du compte sont envoyés au nœud dans le corps de la requête de cette API REST.

		Obtenez le fichier RDP de l’une des manières suivantes :

	- [Méthode IVM.GetRDPFile](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx) : cette méthode .NET nécessite le nom du fichier RDP à créer.
	- [Obtention d’un fichier RDP à partir d’un nœud](https://msdn.microsoft.com/library/dn820120.aspx) : cette API REST nécessite le nom du pool et le nom du nœud de calcul. La réponse contient le contenu du fichier RDP.
	- [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) : cette applet de commande obtient le fichier RDP à partir du nœud de calcul spécifié et l’enregistre à l’emplacement de fichier spécifié ou dans un flux de données.
2.	Certaines applications génèrent de grandes quantités de données qui peuvent se révéler difficiles à traiter. L’un des moyens de contourner ce problème consiste à utiliser des [requêtes de liste efficaces](batch-efficient-list-queries.md).

<!---HONumber=August15_HO6-->