
<properties
	pageTitle="Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch | Microsoft Azure"
	description="Activer la mise à l’échelle automatique sur un pool de cloud pour ajuster dynamiquement le nombre de nœuds de calcul dans le pool."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="12/04/2015"
	ms.author="marsma"/>

# Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch

La mise à l’échelle automatique des nœuds de calcul dans un pool Azure Batch est en fait un ajustement dynamique de la puissance de traitement utilisée par votre application. Cette facilité d’ajustement vous offre un gain de temps et d’argent. Pour plus d’informations sur les nœuds de calcul et sur les pools, voir [Concepts de base d’Azure Batch](batch-technical-overview.md).

La mise à l’échelle automatique a lieu lorsqu’elle est activée sur un pool et qu’une formule est associée à ce pool. Cette formule permet de déterminer le nombre de nœuds de calcul nécessaires pour traiter l’application. Intervenant sur les échantillons recueillis à intervalles réguliers, le nombre de nœuds de calcul disponibles du pool est ajusté toutes les 15 minutes, en fonction de la formule associée.

La mise à l’échelle automatique peut être définie lors de la création d’un pool, ou activée par la suite sur un pool existant. La formule peut également être mise à jour sur un pool pour lequel la mise à l’échelle automatique a été activée au préalable. Il est recommandé d’évaluer systématiquement la formule avant de l’affecter à un pool, et il est important de surveiller l’état des exécutions de mise à l’échelle automatique. Nous abordons ces deux sujets plus loin.

> [AZURE.NOTE]Chaque compte Azure Batch est limité à un nombre maximal de nœuds de calcul utilisables pour le traitement. Le système crée les nœuds à hauteur de cette limite et peut donc ne pas atteindre les nombres cibles spécifiés par une formule.

## Mise à l’échelle automatique des ressources de calcul

La formule de mise à l’échelle que vous définissez détermine le nombre de nœuds de calcul disponibles dans le pool pour le prochain intervalle de traitement. Une formule de mise à l’échelle automatique est une simple valeur de chaîne affectée à un élément de pool [autoScaleFormula](https://msdn.microsoft.com/library/azure/dn820173.aspx) dans un corps de demande (API REST) ou une propriété [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) (API .NET). Cette chaîne de formule ne peut pas dépasser 8 Ko et peut inclure jusqu’à 100 instructions séparées par des points-virgules pouvant atteindre des sauts de ligne et des commentaires.

Les instructions d’une formule sont des expressions au format libre. Elles peuvent inclure des variables définies par le système, des variables définies par l’utilisateur, des valeurs de constante et des opérations prises en charge sur ces variables ou constantes.

	VAR = Expression(system-defined variables, user-defined variables);

Les formules complexes sont créées par l’utilisation de différentes instructions et variables :

	VAR₀ = Expression₀(system-defined variables);
	VAR₁ = Expression₁(system-defined variables, VAR₀);

> [AZURE.NOTE]Une formule de mise à l’échelle automatique est composée de variables, de types, d’opérations et de fonctions API [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx). Celles-ci sont utilisées dans les chaînes de formule lors du travail avec la bibliothèque [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx).

### Variables

Une formule peut utiliser aussi bien des variables définies par le système que des variables définies par l’utilisateur.

*Obtenez* et *définissez* la valeur des **variables** définies par le système pour gérer les nœuds de calcul dans un pool.

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
    <td>$NodeDeallocationOption</td>
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

*Obtenez* les valeurs des **variables définies par le système** ci-après pour effectuer des ajustements basés sur les métriques à partir des nœuds de calcul dans l’échantillon. Ces variables sont en lecture seule.

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
    <td>$SampleNodeCount</td>
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

Ces **types** sont pris en charge dans une formule.

- double
- doubleVec
- string
- Horodatage : il s’agit d’une structure composée qui inclut les éléments suivants :
	- year
	- mois (1-12)
	- jour (1-31)
	- jour de la semaine (sous forme de chiffre, par exemple, 1 pour lundi).
	- heure (au format 24 heures, par exemple, 13 signifie 1 PM).
	- minute (00-59)
	- seconde (00-59)
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

Les **opérations** autorisées sur les types répertoriés ci-dessus sont les suivantes :

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

Les **fonctions** prédéfinies disponibles pour la définition d’une formule de mise à l’échelle automatique sont les suivantes :

<table>
  <tr>
    <th>Fonction</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>Valeur moyenne de toutes les valeurs de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>Longueur du vecteur créé à partir de l’élément doubleVecList.</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>Base logarithmique&#160;2.</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>Base logarithmique&#160;2 au niveau composant. Un élément vec(double) doit être explicitement transmis pour un seul paramètre double&#160;; sinon, le système présume l’utilisation de la version lg(double) double.</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>Logarithme naturel.</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>Base logarithmique&#160;2 au niveau composant. Un élément vec(double) doit être explicitement transmis pour un seul paramètre double&#160;; sinon, le système présume l’utilisation de la version lg(double) double.</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>Base logarithmique&#160;10.</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>Base logarithmique&#160;10 au niveau composant. Un élément vec(double) doit être explicitement transmis pour un seul paramètre double&#160;; sinon, le système présume l’utilisation de la version log(double) double.</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>Valeur maximale de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>Valeur minimale de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>Double norme du vecteur créé à partir de l’élément doubleVecList.
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>Élément de percentile du vecteur&#160;v.</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>Valeur aléatoire comprise entre 0,0 et 1,0.</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>Différence entre les valeurs minimale et maximale de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>Écart type de l’échantillon des valeurs dans l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>Arrêt de l’évaluation de l’expression de mise à l’échelle automatique.</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>Somme de tous les composants de l’élément doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>Horodateur de l’heure actuelle si aucun paramètre n’est transmis, ou horodateur de la chaîne dateTime dans le cas contraire. Les formats dateTime pris en charge sont W3CDTF et RFC1123.</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>Valeur de l’élément à l’emplacement&#160;i du vecteur&#160;v avec un index de départ de zéro.</td>
  </tr>
</table>

Certaines des fonctions décrites dans le tableau ci-dessus peuvent accepter une liste en tant qu’argument. La liste séparée par des virgules se compose de n’importe quelle combinaison *double* et de *doubleVec*. Par exemple :

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

La valeur *doubleVecList* est convertie en un seul paramètre *doubleVec* avant l’évaluation. Par exemple, si `v = [1,2,3]`, l’appel de `avg(v)` équivaut à appeler `avg(1,2,3)` et l’appel de `avg(v, 7)` équivaut à appeler `avg(1,2,3,7)`.

### Obtenir des échantillons de données

Les variables définies par le système décrites plus haut sont des objets qui fournissent des méthodes pour accéder aux données associées. Par exemple, l’expression ci-après présente une requête visant à obtenir les cinq dernières minutes de l’utilisation du processeur :

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

Ces méthodes sont ensuite utilisables pour l’obtention d’échantillons de données.

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
    <td><p>Renvoie un vecteur d’échantillons de données.
	<p>Un échantillon correspond à 30&#160;secondes de données de métrique. En d’autres termes, les échantillons sont collectés toutes les 30&#160;secondes, mais, comme indiqué ci-dessous, il existe un délai entre le moment où un échantillon est collecté et celui où il devient accessible par la formule. Par conséquent, tous les échantillons pour une période donnée ne sont pas forcément disponibles pour évaluation par une formule.
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>&#160;: spécifie le nombre d’échantillons à obtenir à partir des échantillons collectés les plus récents.</p>
				  <p>GetSample(1) renvoie le dernier échantillon disponible. Pour les métriques comme $CPUPercent, toutefois, cette fonction ne doit pas être utilisée car il est impossible de savoir <em>quand</em> l’échantillon a été collecté&#160;: il peut être récent, ou, en raison de problèmes sur le système, il peut être beaucoup plus ancien. Dans ce cas de figure, il est préférable d’utiliser un intervalle de temps, comme indiqué ci-dessous.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>&#160;: indique un délai d’exécution pour la collecte des échantillons de données et spécifie en option le pourcentage d’échantillons devant être disponibles pendant la période demandée.</p>
          <p><em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em> doit renvoyer 20&#160;échantillons si tous les échantillons des dix dernières minutes sont présents dans l’historique CPUPercent. Cependant, si la dernière minute de l’historique n’est pas disponible, seuls 18&#160;échantillons seraient renvoyés, auquel cas&#160;:<br/>
		  &#160;&#160;&#160;&#160;la commande <em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)</em> échouerait car seulement 90&#160;% des échantillons sont disponibles, et<br/>
		  &#160;&#160;&#160;&#160;la commande <em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)</em> aboutirait.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>&#160;: spécifie un délai d’exécution pour la collecte des données avec une heure de début et une heure de fin.</p></li></ul>
		  <p>Comme indiqué ci-dessus, il existe un délai entre le moment où un échantillon est collecté et le moment où il est disponible pour une formule. Cet aspect doit être pris en compte lors de l’utilisation de la méthode <em>GetSample</em> (voir <em>GetSamplePercent</em> ci-dessous).</td>
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
    <p><b>doubleVec GetSamplePercent ((timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime])</b>
	<p>Étant donné que la méthode GetSample échoue si le pourcentage d’échantillons renvoyé est inférieur à la valeur samplePercent indiquée, vous pouvez utiliser les méthodes GetSamplePercent pour vérifier ce point dans un premier temps, puis exécuter une autre action si le nombre d’échantillons présents est insuffisant, sans interrompre l’évaluation de mise à l’échelle automatique.</p></td>
  </tr>
</table>

### Mesures

Vous pouvez utiliser les ressources et les **mesures** de tâche au moment de définir une formule, et ces mesures peuvent être utilisées pour gérer les nœuds de calcul dans un pool.

<table>
  <tr>
    <th>Mesure</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Ressource</td>
    <td><p>Les mesures de ressources sont basées sur l’utilisation du processeur, de la bande passante et de la mémoire, et sur le nombre de nœuds de calculs. Les variables définies par le système (décrites ci-dessous dans les **variables**) sont utilisées dans les formules pour gérer les nœuds de calcul dans un pool&#160;:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Les variables système ci-après sont utilisées pour effectuer des ajustements en fonction des mesures de ressources de nœud&#160;:</p>
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
  </tr>
  <tr>
    <td>Task</td>
    <td><p>Basée sur l’état des tâches (Active, En attente et Terminée).</p>
    <p>Les variables système ci-après sont utilisées pour effectuer des ajustements en fonction des mesures de tâche&#160;:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p></td>
  </tr>
</table>

## Créer une formule de mise à l’échelle

La construction d’une formule de mise à l’échelle s’effectue par la formulation d’instructions à l’aide des composants ci-dessus et par la combinaison de ces instructions dans une formule complète. Par exemple, nous construisons une formule en définissant dans un premier temps les exigences de formule qui vont :

1. augmenter le nombre de nœuds de calcul cible dans un pool si l’utilisation du processeur est intensive
2. va faire baisser le nombre cible de nœuds de calcul dans un pool si l’utilisation du processeur est faible
3. limite toujours le nombre maximal de nœuds à 400

Pour *augmenter* le nombre de nœuds en cas d’utilisation intensive du processeur, nous définissons l’instruction qui remplit une variable définie par l’utilisateur ($TotalNodes) en utilisant une valeur équivalente à 110 % du nombre de nœuds cibles actuels si l’utilisation moyenne du processeur minimale au cours des 10 dernières minutes a été supérieure à 70 % :

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;

L’instruction suivante définit la même variable à 90 % du nombre de nœuds cible actuel si l’utilisation moyenne du processeur de 60 dernières minutes était *inférieure à* 20 %, ce qui réduit le nombre cible lors de l’utilisation faible d’un processeur. Notez que cette instruction fait également référence à la variable définie par l’utilisateur *$TotalNodes* à partir de l’instruction ci-dessus.

	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;

Cet exemple définit le nombre cible de nœuds de calcul dédiés sur un **maximum** de 400 :

	$TargetDedicated = min(400, $TotalNodes)

Voici la formule complète :

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
	$TargetDedicated = min(400, $TotalNodes)

## Créer un pool en activant la mise à l’échelle automatique sur ce dernier

Pour activer la mise à l’échelle automatique lors de la création d’un pool, utilisez l’une des techniques suivantes :

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) : cette applet de commande Azure PowerShell utilise le paramètre AutoScaleFormula pour spécifier la formule de mise à l’échelle automatique.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) : une fois que cette méthode .NET a été appelée pour créer un pool, vous pouvez définir les propriétés [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) et [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) pour activer la mise à l’échelle automatique.
- [Ajout d’un pool à un compte](https://msdn.microsoft.com/library/azure/dn820174.aspx) : les éléments enableAutoScale et autoScaleFormula sont utilisés dans cette requête API REST afin de configurer la mise à l’échelle automatique pour le pool lors de la création de ce dernier.

> [AZURE.NOTE]Si vous configurez la mise à l’échelle automatique à l’aide de l’une des techniques mentionnées ci-dessus, le paramètre *targetDedicated* relatif au pool n’est pas (et ne doit pas être) utilisé au moment de la création. Notez également que si vous souhaitez redimensionner manuellement un pool de mise à l’échelle (par exemple, avec [BatchClient.PoolOperations.ResizePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx)), vous devez dans un premier temps désactiver la mise à l’échelle automatique dans le pool, puis redimensionner le pool.

L’extrait de code suivant illustre la création d’une mise à l’échelle automatique [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) à l’aide de la bibliothèque [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), dont la formule définit le nombre de nœuds cibles à 5 le lundi et 1 tous les jours de la semaine. Dans l’extrait de code, « myBatchClient » est une instance correctement initialisée de [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) :

		CloudPool pool myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
		pool.AutoScaleEnabled = true;
		pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
		pool.Commit();

## Activer la mise à l’échelle automatique après la création d’un pool

Si vous avez déjà configuré un pool avec un nombre de nœuds de calcul spécifié à l’aide du paramètre *targetDedicated*, vous pouvez mettre à jour ce pool par la suite afin d’y activer la mise à l’échelle automatique. Pour effectuer cette opération, utilisez l’une des méthodes suivantes :

- [BatchClient.PoolOperations.EnableAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx) : cette méthode .NET nécessite l’ID d’un pool existant et la formule de la mise à l’échelle automatique à appliquer au pool.
- [Activation de la mise à l’échelle automatique](https://msdn.microsoft.com/library/azure/dn820173.aspx) : cette API REST nécessite le nom du pool existant dans l’URI et la formule de mise à l’échelle automatique dans le corps de la requête.

> [AZURE.NOTE]La valeur spécifiée pour le paramètre *targetDedicated* au moment de la création du pool est ignorée lorsque la formule de mise à l’échelle automatique est évaluée.

Cet extrait de code montre l’activation de la mise à l’échelle automatique sur un pool existant à l’aide de la bibliothèque [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx). Notez que l’activation et la mise à jour de la formule sur un pool existant utilisent la même méthode. Par conséquent, cette technique *met à jour* la formule sur le pool spécifié si la mise à l’échelle automatique a déjà été activée. Cet extrait de code suppose que « myBatchClient » est une instance correctement initialisée de [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx), et que « mypool » est l’ID d’un objet [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) existant.

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Évaluer la formule de mise à l’échelle automatique

Il est recommandé d’évaluer systématiquement une formule avant de l’utiliser dans votre application. La formule est évaluée par le biais de l’exécution d’une « série de tests » sur un pool existant. Pour cela, utilisez :

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx) : ces méthodes .NET nécessitent le nom d’un pool existant et la chaîne qui contient la formule de mise à l’échelle automatique. Les résultats de l’appel sont contenus dans une instance de la classe [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Évaluation d’une formule de mise à l’échelle automatique](https://msdn.microsoft.com/library/azure/dn820183.aspx) : dans cette API REST, l’ID de pool est spécifié dans l’URI, et la formule de mise à l’échelle automatique est spécifiée dans l’élément *autoScaleFormula* du corps de la requête. La réponse de l’opération contient les éventuelles informations d’erreur associées à la formule.

> [AZURE.NOTE]Pour évaluer une formule de mise à l’échelle automatique, vous devez d’abord activer mise à l’échelle automatique sur le pool à l’aide d’une formule valide.

Dans cet extrait de code utilisant la bibliothèque [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), nous évaluons une formule avant de l’appliquer à l’objet [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx).

		// First obtain a reference to the existing pool
		CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

		// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
		if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
		{
			// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
			string myFormula = @"
				$CurTime=time();
				$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
				$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
				$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
				$TargetDedicated=$IsWorkingWeekdayHour?20:10;
			";

			// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
			// the pool.
			AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

			if (eval.AutoScaleRun.Error == null)
			{
				// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
				// variable as evaluated by the the autoscaling formula.
				Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

				// Apply the formula to the pool since it evaluated successfully
				client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
			}
			else
			{
				// Evaluation failed, output the message associated with the error
				Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
			}
		}

L’évaluation réussie de la formule dans cet extrait de code entraîne une sortie similaire à ce qui suit :

		AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0

## Obtenir des informations sur les exécutions de mise à l’échelle automatique

Une vérification à intervalle régulier des résultats de l’exécution de mise à l’échelle automatique doit être exécutée automatique sur une formule fonctionnant comme prévu. Pour effectuer cette opération, utilisez l’une des méthodes suivantes :

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) : lors de l’utilisation de la bibliothèque .NET, cette propriété d’un pool fournit une instance de la classe [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) qui indique les propriétés de la dernière exécution de mise à l’échelle automatique :
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Obtention d’informations sur un pool](https://msdn.microsoft.com/library/dn820165.aspx) : cette requête API REST renvoie des informations sur le pool, et inclut la dernière exécution de mise à l’échelle automatique.

## formules exemple

Examinons quelques exemples présentant certaines façons d’utiliser des formules pour adapter automatiquement les ressources dans un pool.

### Exemple 1

Peut-être vous souhaitez ajuster la taille du pool en fonction du jour de la semaine et de l’heure de la journée, augmentant ou en diminuant le nombre de nœuds dans le pool en conséquence :

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

Cette formule obtient dans un premier temps l’heure actuelle. S’il s’agit d’un jour de la semaine (1 à 5) et les heures de travail (8 heures à 18 heures), la taille du pool cible est définie à 20 nœuds. Autrement, la taille cible du pool est définie sur 10.

### Exemple 2

Dans cet exemple, la taille du pool est ajustée en fonction du nombre de tâches présentes dans la file d’attente. Notez que les commentaires et les sauts de ligne sont acceptés dans les chaînes de formule.

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Exemple 3

Autre exemple adaptant la taille du pool au nombre de tâches, cette formule prend également en compte la valeur [MaxTasksPerComputeNode](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx) qui a été définie pour le pool. Cela est particulièrement utile dans les situations où l’exécution parallèle de tâche sur des nœuds de calcul est souhaitée.

```
// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Exemple 4

Cet exemple montre une formule de mise à l’échelle automatique qui définit la taille du pool à un certain nombre de nœuds pour une période de départ, puis ajuste la taille du pool en fonction du nombre de tâches en cours d’exécution et actives après expiration de la période initiale.

```
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"

	$TargetDedicated = {1};
	lifespan         = time() - time(""{0}"");
	span             = TimeInterval_Minute * 60;
	startup          = TimeInterval_Minute * 10;
	ratio            = 50;

	$TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
	", now, 4);
```

La formule de l’extrait de code ci-dessus présente les caractéristiques suivantes :

- Définit la taille initiale du pool à 4 nœuds
- N’ajuste pas la taille du pool dans les 10 premières minutes de cycle de vie du pool
- Après 10 minutes, obtient la valeur maximale du nombre de tâches en cours d’exécution et actives pendant les 60 dernières minutes
  - Si les deux valeurs sont 0 (ce qui indique qu’aucune tâche n’était en cours d’exécution ou active au cours des 60 dernières minutes), la taille du pool est définie sur 0
  - Si l’une des valeurs est supérieure à zéro, aucune modification n’est apportée

## Étapes suivantes

1. Pour parvenir à évaluer intégralement l’efficacité de votre application, il se peut que vous deviez accéder au nœud de calcul . Pour tirer parti de l’accès à distance, vous devez ajouter un compte d’utilisateur au nœud de calcul auquel vous souhaitez accéder, et extraire un fichier RDP de ce nœud.
    - Ajoutez le compte d’utilisateur de l’une des manières suivantes :
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) : cette applet de commande PowerShell utilise le nom du pool, le nom du nœud de calcul, ainsi que le nom et le mot de passe du compte en tant que paramètres.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx) : cette méthode .NET crée une instance de classe [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) sur laquelle vous pouvez définir le nom de compte et le mot de passe pour le nœud de calcul et [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx) est ensuite appelée sur l’instance afin de créer l’utilisateur sur ce nœud.
        * [Ajout d’un compte d’utilisateur à un nœud](https://msdn.microsoft.com/library/dn820137.aspx) : le nom du pool et le nœud de calcul sont spécifiés dans l’URI, et le nom et le mot de passe du compte sont envoyés au nœud dans le corps de cette requête API REST.
    - Obtenez le fichier RDP de l’une des manières suivantes :
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx) : cette méthode .NET nécessite l’ID du pool, l’ID du nœud et le nom du protocole RDP du fichier à créer.
        * [Obtention d’un fichier de protocole de bureau distant à partir d’un nœud](https://msdn.microsoft.com/library/dn820120.aspx) : cette requête API REST requiert le nom du pool et le nom du nœud de calcul. La réponse contient le contenu du fichier RDP.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) : cet applet de commande PowerShell obtient le fichier RDP à partir du nœud de calcul spécifié et l’enregistre à l’emplacement de fichier spécifié ou dans un flux de données.
2.	Certaines applications génèrent de grandes quantités de données qui peuvent se révéler difficiles à traiter. L’un des moyens de contourner ce problème consiste à utiliser des [requêtes de liste efficaces](batch-efficient-list-queries.md).

<!---HONumber=AcomDC_1210_2015-->