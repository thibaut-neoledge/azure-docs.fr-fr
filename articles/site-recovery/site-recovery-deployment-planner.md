---
title: "Azure Site Recovery Deployment Planner pour le déploiement de VMware vers Azure| Microsoft Docs"
description: "Il s’agit du guide de l’utilisateur d’Azure Site Recovery Deployment Planner."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2575621d72b7db2b090ba923324697b7fa7b8308
ms.lasthandoff: 03/15/2017


---
#<a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
Il s’agit du guide de l’utilisateur d’Azure Site Recovery Deployment Planner portant sur les déploiements de production de VMware vers Azure.


##<a name="overview"></a>Vue d'ensemble

Avant de protéger les machines virtuelles VMware à l’aide d’Azure Site Recovery, vous devez allouer suffisamment de bande passante en fonction de votre taux de modifications des données par jour pour remplir l’objectif de point de récupération (RPO) souhaité. Vous devez déployer le nombre approprié de serveurs de configuration et de serveurs de processus en local. Vous devez également créer le type et le nombre appropriés de comptes de stockage Azure cibles, standard ou Premium, en tenant compte de la croissance sur vos serveurs de production sources due à une utilisation accrue au fil du temps. Le type de stockage est décidé par machine virtuelle en fonction des caractéristiques des charges de travail (E/S par seconde de lecture/écriture, activité des données) et des limites d’Azure Site Recovery.  

La préversion publique d’Azure Site Recovery Deployment Planner est un outil de ligne de commande actuellement disponible uniquement pour le scénario de déploiement VMware vers Azure. Vous pouvez profiler à distance vos machines virtuelles VMware à l’aide de cet outil (sans conséquences sur la production) pour comprendre les besoins de stockage Azure et de bande passante afin d’assurer la réussite de la réplication et du test de basculement.  Vous pouvez exécuter l’outil sans installer de composants Azure Site Recovery localement, même si pour obtenir des résultats de débit précis, il est recommandé d’exécuter l’outil sur un serveur Windows Server répondant à la configuration requise minimale du serveur de configuration Azure Site Recovery que vous devrez finalement déployer dans les premières étapes du déploiement de production.

L’outil fournit les informations suivantes :

**Évaluation de la compatibilité**<br>
* Évaluation de l’éligibilité de la machine virtuelle en fonction du nombre de disques, de la taille du disque, des E/S par seconde et de l’activité.

**Besoin en bande passante réseau et évaluation du RPO**<br>
* Bande passante réseau estimée requise pour la réplication différentielle.<br>
* Débit pouvant être obtenu par Azure Site Recovery dans le scénario « local vers Azure ».<br>
* Nombre de machines virtuelles à traiter par lot en fonction de la bande passante estimée pour effectuer une réplication initiale pendant une durée donnée.<br>

**Exigences de l’infrastructure Microsoft Azure**<br>
* Spécification du type de stockage (stockage standard ou Premium) pour chaque machine virtuelle.<br>
* Nombre total de comptes de stockage standard et Premium à configurer pour la réplication.<br>
* Suggestions d’affectation de noms pour les comptes de stockage en fonction des conseils liés au Stockage Azure.<br>
* Positionnement du compte de stockage de chaque machine virtuelle.<br>
* Nombre de cœurs Microsoft Azure à configurer avant le test de basculement/basculement sur l’abonnement.<br>
* Taille de machine virtuelle Microsoft Azure recommandée pour chaque machine virtuelle locale.<br>

**Exigences de l’infrastructure locale**<br>
* Nombre requis de serveurs de configuration et de serveurs de processus à déployer en local.<br>

>[!IMPORTANT]
>
>Tous ces calculs de l’outil sont effectués en supposant un facteur de croissance de 30 % dans les caractéristiques des charges de travail, en raison d’une utilisation possiblement accrue au fil du temps et en prenant le 95e centile de tous les indicateurs de performance du profilage (E/S par seconde de lecture/écriture, activité, etc.) Ces deux paramètres, facteur de croissance et calcul de centile, sont configurables. En savoir plus sur le [facteur de croissance](site-recovery-deployment-planner.md#growth-factor) et la [valeur de centile utilisée pour le calcul](site-recovery-deployment-planner.md#percentile-value-used-for-the-calculation).
>


## <a name="requirements"></a>Configuration requise
L’outil comporte deux phases principales : le profilage et la génération de rapport. Une troisième option permet également de calculer le débit. Vous trouverez ci-dessous la configuration requise pour le serveur à partir duquel le profilage et la mesure du débit sont initiés.

| Prérequis | Description|
|---|---|
|Profilage et mesure du débit| <br>Système d’exploitation : Microsoft Windows Server 2012 R2 <br>Dans l’idéal, correspond au moins à la [taille](https://aka.ms/asr-v2a-on-prem-components) du serveur de configuration suivante<br>Configuration de la machine : 8 processeurs virtuels, 16 Go de RAM, disque dur de 300 Go<br [Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)<br>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)<br>[Microsoft Visual C++ Redistributable pour Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)<br> Accès Internet à Microsoft Azure à partir de ce serveur<br> Compte de stockage Microsoft Azure<Br>Accès administrateur sur le serveur<br>Espace disque disponible minimum de 100 Go (en partant de 1 000 machines virtuelles avec chacune une moyenne de 3 disques profilés pendant 30 jours)|
| Génération de rapport| Tout PC Windows/serveur Windows Server doté de Microsoft Excel 2013 et supérieur |
| Autorisations utilisateur | Autorisation de lecture seule pour le compte d’utilisateur utilisé pour accéder au serveur VMware vCenter/vSphere lors du profilage.|


> [!NOTE]
>
> L’outil ne peut profiler que les machines virtuelles dotées de disques VMDK et RDM. Il ne peut pas profiler les machines virtuelles équipées de disques iSCSI ou NFS. Si Azure Site Recovery prend en charge les disques iSCSI et NFS pour les serveurs VMware, comme l’outil Deployment Planner ne se trouve pas dans l’invité et n’effectue le profilage qu’à l’aide des compteurs de performance vCenter, il n’a pas de visibilité sur ces types de disque.
>


##<a name="download"></a>Télécharger
[Téléchargez](https://aka.ms/asr-deployment-planner) la dernière version de la préversion publique d’Azure Site Recovery Deployment Planner.  L’outil se présente au format zip.  Sa version actuelle ne prend en charge que le scénario VMware vers Azure.

Copiez le fichier zip sur le serveur Windows Server à partir duquel vous voulez exécuter l’outil. Bien que vous puissiez exécuter l’outil à partir de tout serveur Windows Server 2012 R2 bénéficiant d’un accès réseau pour se connecter au serveur VMware vCenter ou à l’hôte ESXi VMware vSphere qui contient les machines virtuelles à profiler, il est recommandé d’exécuter l’outil sur un serveur dont la configuration matérielle est conforme aux [Recommandations de taille pour le serveur de configuration](https://aka.ms/asr-v2a-on-prem-components).  Si vous avez déjà déployé les composants Azure Site Recovery en local, vous devez exécuter l’outil à partir du serveur de configuration. Il est recommandé de disposer de la même configuration matérielle que celle du serveur de configuration (qui dispose d’un serveur de processus intégré) sur le serveur sur lequel vous exécutez l’outil afin que le débit atteint indiqué par l’outil corresponde au débit effectif qu’Azure Site Recovery peut atteindre au cours de la réplication ; le calcul du débit dépend de la bande passante réseau disponible sur le serveur et de la configuration matérielle (processeur, stockage, etc.) du serveur. Si vous exécutez l’outil à partir de n’importe quel autre serveur, le débit est calculé à partir de ce serveur vers Microsoft Azure, et la configuration matérielle du serveur peut être différente de celle du serveur de configuration. Le débit atteint indiqué par l’outil n’est donc pas exact.

Extrayez le dossier zip. Vous pouvez observer plusieurs fichiers et sous-dossiers. Le fichier exécutable s’appelle ASRDeploymentPlanner.exe dans le dossier parent.

Exemple : copiez le fichier .zip sur le lecteur E:\ et extrayez-le.
E:\ASR Deployment Planner-Preview_v1.1.zip

E:\ASR Deployment Planner-Preview_v1.1\ ASR Deployment Planner-Preview_v1.1\ ASRDeploymentPlanner.exe

##<a name="capabilities"></a>Fonctionnalités
L’outil de ligne de commande (ASRDeploymentPlanner.exe) peut être exécuté dans l’un des trois modes suivants :

1.    Profilage  
2.    Génération de rapport
3.    GetThroughput

Vous devez d’abord exécuter l’outil en mode profilage pour collecter l’activité des données et les E/S par seconde des machines virtuelles.  Exécutez ensuite l’outil pour générer le rapport afin de déterminer les besoins en bande passante réseau et en stockage.

##<a name="profiling"></a>Profilage
En mode profilage, l’outil Deployment Planner se connecte au serveur vCenter ou aux hôtes vSphere ESXi pour collecter les données de performance sur la machine virtuelle.

* Le profilage n’affecte pas les performances des machines virtuelles de production, car aucune connexion directe n’est établie avec la machine virtuelle de production. Toutes les données de performance sont collectées à partir du serveur vCenter/de l’hôte ESXi vSphere.
* Le serveur vCenter/hôte vSphere ESXi est interrogé toutes les 15 minutes, pour s’assurer que les effets du profilage sur le serveur sont négligeables. Toutefois, cela ne compromet pas la précision du profilage, car l’outil stocke les données du compteur de performances toutes les minutes.

####<a name="create-a-list-of-virtual-machines-to-profile"></a>Créer une liste de machines virtuelles à profiler
Tout d’abord, vous devez établir la liste des machines virtuelles à profiler. Vous pouvez obtenir tous les noms des machines virtuelles sur un hôte VMware vCenter ou VMware vSphere ESXi à l’aide des commandes VMware vSphere PowerCLI indiquées ci-dessous. Vous pouvez également répertorier uniquement les noms conviviaux/adresses IP des machines virtuelles que vous cherchez à profiler manuellement dans un fichier.

1.    Ouvrez une session sur la machine virtuelle sur laquelle VMware vSphere PowerCLI est installé.
2.    Ouvrez la console VMware vSphere PowerCLI.
3.    Assurez-vous que la stratégie d’exécution n’est pas désactivée pour le script. Si elle est désactivée, lancez la console VMware vSphere PowerCLI en mode administrateur et exécutez la commande suivante pour l’activer :

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4.    Exécutez les deux commandes suivantes pour obtenir tous les noms des machines virtuelles sur un hôte VMware vCenter ou VMware vSphere ESXi et les enregistrer dans un fichier .txt.
Remplacez &lsaquo;server name&rsaquo;, &lsaquo;user name&rsaquo;, &lsaquo;password&rsaquo; et &lsaquo;outputfile.txt&rsaquo; par vos entrées.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>


5.    Ouvrez le fichier de sortie dans le Bloc-notes. Copiez les noms de toutes les machines virtuelles que vous voulez profiler dans un autre fichier (par exemple ProfileVMList.txt), en indiquant un nom de machine virtuelle par ligne. Ce fichier sera utilisé comme entrée pour le paramètre -VMListFile de l’outil de ligne de commande.

    ![Deployment Planner](./media/site-recovery-deployment-planner/profile-vm-list.png)


####<a name="start-profiling"></a>Démarrer le profilage
Après avoir établi la liste des machines virtuelles à profiler, vous pouvez exécuter l’outil en mode profilage. Voici la liste des paramètres obligatoires et facultatifs de l’outil à exécuter en mode profilage. Les paramètres indiqués entre crochets ([]) sont facultatifs.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Nom du paramètre | Description |
|---|---|
| -Operation |      StartProfiling |
| -Server | Nom de domaine complet ou adresse IP du serveur vCenter/de l’hôte ESXi dont les machines virtuelles doivent être profilées.|
| -User | Nom d’utilisateur pour se connecter au serveur vCenter/à l’hôte ESXi. L’utilisateur doit disposer au moins d’un accès en lecture seule.|
| -VMListFile |    Fichier contenant la liste des machines virtuelles à profiler. Le chemin d’accès du fichier peut être absolu ou relatif. Ce fichier doit contenir un nom/une adresse IP de machine virtuelle par ligne. Le nom de la machine virtuelle spécifié dans le fichier doit être identique au nom de la machine virtuelle sur le serveur vCenter ou l’hôte ESXi. <br> Par exemple, le fichier VMList.txt contient les machines virtuelles suivantes :<br>virtual_machine_A <br>10.150.29.110<br>virtual_machine_B |
| -NoOfDaysToProfile | Nombre de jours pendant lesquels le profilage doit être exécuté. Il est recommandé d’exécuter le profilage pendant plus de 15 jours pour vous assurer que le modèle de charge de travail de votre environnement sur la période spécifiée est observé et utilisé pour fournir une recommandation précise. |
| [-Directory] |    UNC ou chemin d’accès du répertoire local pour stocker les données de profilage générées pendant cette opération. Si aucun répertoire n’est spécifié, le répertoire ProfiledData figurant dans le chemin d’accès actuel est utilisé comme répertoire par défaut. |
| [-Password ] | Mot de passe pour se connecter au serveur vCenter/à l’hôte ESXi. Si aucun mot de passe n’est spécifié maintenant, vous êtes invité à l’indiquer à l’exécution de la commande.|
|  [-StorageAccountName]  | Nom du compte de stockage Azure pour rechercher le débit réalisable pour la réplication des données locales vers Azure. L’outil charge les données de test sur ce compte de stockage pour calculer le débit.|
| [-StorageAccountKey] | Clé du compte de stockage Azure utilisée pour accéder au compte de stockage. Accédez au portail Azure > Comptes de stockage > [Nom du compte de stockage] > Paramètres > Clés d’accès > Key1 (ou Clé d’accès principale pour le compte de stockage classique). |

Il est recommandé de profiler vos machines virtuelles pendant au moins 15 à 30 jours. Pendant la période de profilage, ASRDeploymentPlanner.exe continue de s’exécuter. Les entrées du temps de profilage de l’outil sont indiquées en jours. Si vous souhaitez procéder au profilage pendant quelques heures ou quelques minutes dans le cadre d’un test rapide de l’outil, dans la préversion publique, vous devez convertir le temps en nombre de jours équivalent.  Par exemple, pour un profilage de 30 minutes, l’entrée doit être égale à 30 / (60 * 24) = 0,021 jours.  Le temps de profilage minimum autorisé est de 30 minutes.

Pendant le profilage, vous pouvez éventuellement transmettre un nom et une clé du compte de stockage Azure pour déterminer le débit qu’Azure Site Recovery peut atteindre au moment de la réplication du serveur de configuration/serveur de processus vers Azure. Si la clé et le nom du compte de stockage Azure ne sont pas transmis au cours du profilage, l’outil ne calcule pas le débit réalisable.


Vous pouvez exécuter plusieurs instances de l’outil pour différents ensembles de machines virtuelles. Veillez à ce que les noms des machines virtuelles ne soient pas répétés dans les ensembles de profilage. Exemple : vous avez profilé dix machines virtuelles (VM1 - VM10) et après quelques jours vous voulez profiler cinq autres machines virtuelles (VM11 - VM15) ; vous pouvez exécuter l’outil à partir d’une autre console de ligne de commande pour le second ensemble de machines virtuelles (VM11 - VM15). Vous devez néanmoins vous assurer que le second ensemble de machines virtuelles ne comporte pas de noms de machine virtuelle de la première instance de profilage ou utiliser un autre répertoire de sortie pour la seconde exécution. Si deux instances de l’outil sont utilisées pour profiler les mêmes machines virtuelles et que vous utilisez le même répertoire de sortie, le rapport généré sera incorrect.

La configuration des machines virtuelles est capturée une fois au début de l’opération de profilage et stockée dans un fichier appelé VMDetailList.xml. Ces informations sont utilisées au moment de la génération de rapport. Les modifications de la configuration des machines virtuelles (par exemple, augmentation du nombre de cœurs, de disques, de cartes réseau, etc.) intervenant entre le début du profilage et sa fin ne sont pas capturées. Si vous vous trouvez dans une situation où la configuration des machines virtuelles est modifiée en cours de profilage, la solution de contournement dans la préversion publique consiste à obtenir les toutes dernières informations des machines virtuelles lorsque vous générez le rapport.   

* Sauvegardez VMdetailList.xml et supprimez le fichier de son emplacement actuel.
* Transmettez les arguments -User et -Password au moment de la génération du rapport.

La commande de profilage génère plusieurs fichiers dans le répertoire de profilage ; veillez à ne pas les supprimer, faute de quoi la génération de rapport serait affectée.

#####<a name="example-1-to-profile-virtual-machines-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exemple 1 : pour profiler des machines virtuelles pendant 30 jours et déterminer le débit pour le scénario « local vers Azure »
ASRDeploymentPlanner.exe **-Operation** StartProfiling -Directory « E:\vCenter1_ProfiledData » **-Server** vCenter1.contoso.com **-VMListFile** « E:\vCenter1_ProfiledData\ProfileVMList1.txt » **-NoOfDaysToProfile** 30 **-User** vCenterUser1 **-StorageAccountName** asrspfarm1 **-StorageAccountKey** Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

#####<a name="example-2-to-profile-virtual-machines-for-15-days"></a>Exemple 2 : pour profiler des machines virtuelles pendant 15 jours
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** « E:\vCenter1_ProfiledData » **-Server** vCenter1.contoso.com **-VMListFile** « E:\vCenter1_ProfiledData\ProfileVMList1.txt » **-NoOfDaysToProfile** 15 -User vCenterUser1

#####<a name="example-3-to-profile-virtual-machines-for-1-hour-for-a-quick-test-of-the-tool"></a>Exemple 3 : pour profiler des machines virtuelles pendant 1 heure pour tester rapidement l’outil
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** « E:\vCenter1_ProfiledData » **-Server** vCenter1.contoso.com **-VMListFile** « E:\vCenter1_ProfiledData\ProfileVMList1.txt » **-NoOfDaysToProfile** 0.04 **-User** vCenterUser1


>[!NOTE]
>
> * Si le serveur à partir duquel l’outil est exécuté est redémarré ou se bloque, ou si vous quittez l’outil à l’aide de Ctrl+C, les données profilées sont conservées. Pour cette raison, il existe un risque de manquer les 15 dernières minutes de données profilées. Vous devez réexécuter l’outil en mode profilage une fois que le serveur a démarré la sauvegarde.
>
> * Lorsque le nom et la clé du compte de stockage Azure sont transmis, l’outil mesure le débit au cours de la dernière étape du profilage. Si l’outil s’arrête avant l’issue du profilage, le débit n’est pas calculé. Vous pouvez toujours exécuter l’opération GetThroughput depuis la console de ligne de commande pour déterminer le débit avant de générer le rapport, sinon le rapport généré ne contiendra pas les informations sur le débit atteint.
>

##<a name="generate-report"></a>Générer le rapport
L’outil génère un fichier XLSM (fichier Microsoft Excel prenant en charge les macros) comme sortie du rapport qui récapitule toutes les recommandations de déploiement : le rapport est intitulé DeploymentPlannerReport_<Unique Numeric Identifier>.xlsm et placé dans le répertoire spécifié.

À l’issue du profilage, vous pouvez exécuter l’outil en mode génération de rapport. Voici la liste des paramètres obligatoires et facultatifs de l’outil à exécuter en mode génération de rapport. Les paramètres indiqués entre crochets ([]) sont facultatifs.

ASRDeploymentPlanner.exe -Operation GenerateReport /?

|Nom du paramètre | Description |
|-|-|
| -Operation | GenerateReport |
| -Server |  Nom de domaine complet ou adresse IP du serveur vCenter/vSphere (utilisez le même nom ou la même adresse IP que vous avez utilisés lors du profilage) sur lequel se trouvent les machines virtuelles profilées dont le rapport doit être généré. Notez que si vous avez utilisé un serveur vCenter au moment du profilage, vous ne pouvez pas utiliser un serveur vSphere pour la génération de rapport, et vice versa.|
| -VMListFile | Fichier contenant la liste des machines virtuelles profilées pour lesquelles le rapport doit être généré. Le chemin d’accès du fichier peut être absolu ou relatif. Ce fichier doit contenir un nom/une adresse IP de machine virtuelle par ligne. Les noms de machines virtuelles spécifiés dans le fichier doivent être identiques à ceux des machines virtuelles sur le serveur vCenter ou l’hôte ESXi, et ils correspondent à ce qui a été utilisé au moment du profilage.|
| [-Directory] | UNC ou chemin d’accès du répertoire local où les données profilées (fichiers générés lors du profilage) sont stockées. Ces données sont requises pour générer le rapport. Si ce paramètre n’est pas spécifié, le répertoire ProfiledData est utilisé. |
| [-GoalToCompleteIR] |    Nombre d’heures pendant lesquelles la réplication initiale des machines virtuelles profilées doit être effectuée. Le rapport généré indique le nombre de machines virtuelles pour lesquelles la réplication initiale peut être effectuée dans le délai spécifié. Valeur par défaut : 72 heures. |
| [-User] | Nom d’utilisateur pour se connecter au serveur vCenter/vSphere. Il est utilisé pour extraire les dernières informations de configuration des machines virtuelles, comme le nombre de disques, le nombre de cœurs, le nombre de cartes réseau, etc. à utiliser dans le rapport. S’il n’est pas fourni, les informations de configuration collectées au début du profilage sont utilisées. |
| [-Password] | Mot de passe pour se connecter au serveur vCenter/à l’hôte ESXi. Si aucun mot de passe n’est spécifié en tant que paramètre, vous êtes invité à l’indiquer ultérieurement à l’exécution de la commande. |
| [-DesiredRPO] | RPO souhaité indiqué en minutes. Valeur par défaut : 15 minutes.|
| [-Bandwidth] | Bande passante en Mbits/s. Ce paramètre permet de calculer le RPO qui peut être atteint pour la bande passante spécifiée. |
| [-StartDate]  | Date et heure de début sous la forme MM-JJ-AAAA:HH:MM (au format 24 heures). Le paramètre StartDate doit être spécifié avec le paramètre EndDate. Si ce paramètre est spécifié, le rapport est généré pour les données profilées collectées entre les dates StartDate et EndDate. |
| [-EndDate] | Date et heure de fin sous la forme MM-JJ-AAAA:HH:MM (au format 24 heures). Le paramètre EndDate doit être spécifié avec le paramètre StartDate. Si ce paramètre est spécifié, le rapport est généré pour les données profilées collectées entre les dates StartDate et EndDate. |
| [-GrowthFactor] |Facteur de croissance en pourcentage. Valeur par défaut : 30 %.  |


##### <a name="example-1-to-generate-report-with-default-values-when-profiled-data-is-on-the-local-drive"></a>Exemple 1 : pour générer un rapport contenant des valeurs par défaut lorsque les données profilées sont situées sur le lecteur local
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** « E:\vCenter1_ProfiledData » **-VMListFile** « E:\vCenter1_ProfiledData\ProfileVMList1.txt »


##### <a name="example-2-to-generate-report-when-profiled-data-is-on-a-remote-server-user-should-have-readwrite-access-on-the-remote-directory"></a>Exemple 2 : pour générer un rapport lorsque les données profilées sont situées sur un serveur distant L’utilisateur doit disposer d’un accès en lecture/écriture sur le répertoire distant.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** « \\\\PS1-W2K12R2\vCenter1_ProfiledData » **-VMListFile** « \\\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt »

##### <a name="example-3-generate-report-with-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exemple 3 : pour générer un rapport contenant la bande passante spécifique et les objectifs pour terminer le RI dans le temps indiqué
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** « E:\vCenter1_ProfiledData » **-VMListFile** « E:\vCenter1_ProfiledData\ProfileVMList1.txt » **-Bandwidth** 100 **-GoalToCompleteIR** 24

##### <a name="example-4-generate-report-with-5-growth-factor-instead-of-the-default-30"></a>Exemple 4 : pour générer un rapport avec un facteur de croissance de 5 % au lieu de la valeur par défaut de 30 %
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** « E:\vCenter1_ProfiledData » **-VMListFile** « E:\vCenter1_ProfiledData\ProfileVMList1.txt » **-GrowthFactor** 5

##### <a name="example-5-generate-report-with-a-subset-of-profiled-data-say-you-have-30-days-of-profiled-data-and-want-to-generate-the-report-for-only-20-days"></a>Exemple 5 : pour générer un rapport contenant un sous-ensemble de données profilées Supposons que vous disposiez de 30 jours de données profilées et que vous souhaitiez générer le rapport pour une période de 20 jours seulement.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** « E:\vCenter1_ProfiledData » **-VMListFile** « E:\vCenter1_ProfiledData\ProfileVMList1.txt » **-StartDate** 01-10-2017:12:30 -**EndDate** 01-19-2017:12:30

##### <a name="example-6-generate-report-for-5-minutes-rpo"></a>Exemple 6 : pour générer un rapport pour un RPO de 5 minutes
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** « E:\vCenter1_ProfiledData » **-VMListFile** « E:\vCenter1_ProfiledData\ProfileVMList1.txt » **-DesiredRPO** 5

### <a name="percentile-value-used-for-the-calculation"></a>Valeur de centile utilisée pour le calcul
**Quelle valeur de centile par défaut des indicateurs de performance collectés pendant le profilage est-elle utilisée au moment de la génération de rapport ?**

L’outil est configuré par défaut sur les valeurs du 95e centile des E/S par seconde de lecture/écriture, des E/S par seconde d’écriture et de l’activité des données collectées pendant le profilage de toutes les machines virtuelles. Cela garantit que le pic du 100e centile que vos machines virtuelles peuvent observer en raison d’événements temporaires, comme un travail de sauvegarde s’exécutant une fois par jour, l’indexation de base de données régulière ou l’activité de génération de rapport d’analyse, ou tout autre point similaire d’un événement de courte durée qui se produit pendant la période de profilage, n’est pas utilisé pour déterminer vos besoins de bande passante source et de stockage Azure cible. L’utilisation des valeurs du 95e centile permet de donner une image exacte des caractéristiques des charges de travail réelles et vous offre les meilleures performances lorsque ces charges de travail s’exécutent sur Microsoft Azure. Nous ne nous attendons pas à ce que vous modifiez souvent ce nombre, mais si vous souhaitez aller encore plus bas et utiliser le 90e centile par exemple, vous pouvez mettre à jour ce fichier de configuration ASRDeploymentPlanner.exe.config dans le dossier par défaut et l’enregistrer pour générer un nouveau rapport sur les données profilées existantes.

        &lsaquo;add key="WriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="ReadWriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="DataChurnPercentile" value="95" /&rsaquo;

### <a name="growth-factor"></a>Facteur de croissance
**Pourquoi penser au facteur de croissance lors de la planification du déploiement ?**

Il est essentiel de tenir compte de la croissance dans vos caractéristiques de charge de travail en supposant une augmentation potentielle de l’utilisation au fil du temps. C’est parce qu’une fois la protection établie, si vos caractéristiques de charge de travail changent, il n’existe actuellement aucun moyen de passer à un autre compte de stockage Azure pour la protection sans la désactiver et la réactiver. Par exemple, si aujourd’hui, une machine virtuelle intègre un compte de réplication de stockage standard, en trois mois, en raison d’une augmentation du nombre d’utilisateurs de l’application exécutée sur la machine virtuelle, si l’activité sur la machine virtuelle augmente et nécessite de passer au stockage Premium afin que la réplication Azure Site Recovery puisse suivre la nouvelle activité plus élevée, vous devrez désactiver et réactiver la protection sur un compte de stockage Premium. Il est donc vivement recommandé de planifier une croissance pendant la planification du déploiement et une valeur par défaut de 30 %. Vous connaissez mieux le modèle d’utilisation de vos applications et les projections de croissance et vous pouvez modifier ce nombre en conséquence lors de la génération d’un rapport. De fait, vous pouvez générer plusieurs rapports avec différents facteurs de croissance avec les mêmes données profilées et observer quelles recommandations de stockage Azure cible et de bande passante source fonctionnent le mieux pour vous.

Le rapport Microsoft Excel généré contient les feuilles suivantes

* [Input](site-recovery-deployment-planner.md#input)
* [Recommendations](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Recommedations-Bandwidth Input](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->Storage Placement](site-recovery-deployment-planner.md#vm-storage-placement)
* [Compatible VMs](site-recovery-deployment-planner.md#compatible-vms)
* [Incompatible VMs](site-recovery-deployment-planner.md#incompatible-vms)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)


##<a name="get-throughput"></a>GetThroughput
Pour estimer le débit qu’Azure Site Recovery peut atteindre pendant la réplication de données locales vers Azure, exécutez l’outil en mode GetThroughput. L’outil calcule le débit à partir du serveur sur lequel l’outil est exécuté (dans l’idéal, un serveur basé sur le guide de dimensionnement du serveur de configuration).  Si vous avez déjà déployé les composants d’infrastructure Azure Site Recovery en local, exécutez l’outil sur le serveur de configuration.

Ouvrez une console de ligne de commande et accédez au dossier de l’outil de planification du déploiement ASR.  Exécutez ASRDeploymentPlanner.exe avec les paramètres ci-dessous. Les paramètres indiqués entre crochets ([]) sont facultatifs.

ASRDeploymentPlanner.exe -Operation GetThroughput /?

|Nom du paramètre | Description |
|-|-|
| -operation | GetThroughput |
| [-Directory] | UNC ou chemin d’accès du répertoire local où les données profilées (fichiers générés lors du profilage) sont stockées. Ces données sont requises pour générer le rapport. Si ce paramètre n’est pas spécifié, le répertoire ProfiledData est utilisé.  |
| -StorageAccountName | Nom du compte de stockage Azure pour déterminer la bande passante utilisée pour la réplication des données locales vers Azure. L’outil charge les données de test sur ce compte de stockage pour trouver la bande passante consommée. |
| -StorageAccountKey | Clé du compte de stockage Azure utilisée pour accéder au compte de stockage. Accédez au portail Azure > Comptes de stockage > [Nom du compte de stockage] > Paramètres > Clés d’accès > Key1 (ou Clé d’accès principale pour le compte de stockage classique). |
| -VMListFile | Fichier contenant la liste des machines virtuelles à profiler pour calculer la bande passante consommée. Le chemin d’accès du fichier peut être absolu ou relatif. Ce fichier doit contenir un nom/une adresse IP de machine virtuelle par ligne. Les noms de machines virtuelles spécifiés dans le fichier doivent être identiques à ceux des machines virtuelles sur le serveur vCenter ou l’hôte ESXi.<br>Par exemple, le fichier VMList.txt contient les machines virtuelles suivantes :<br>virtual_machine_A <br>10.150.29.110<br>virtual_machine_B|

L’outil crée plusieurs fichiers asrvhdfile<#>.vhd de 64 Mo (où # représente le numéro) dans le répertoire spécifié.  Il charge ces fichiers sur le compte de stockage Azure pour déterminer le débit. Une fois le débit mesuré, il supprime tous ces fichiers du compte de stockage Azure et du serveur local. Si l’exécution de l’outil est arrêtée à mi-parcours pour une raison quelconque lors du calcul du débit, il ne supprime pas les fichiers du stockage Azure ni du serveur local, et vous devez les supprimer manuellement.

Le débit est mesuré à un moment donné et il s’agit du débit maximal qu’Azure Site Recovery peut atteindre lors d’une réplication, sous réserve que tous les autres facteurs restent identiques. Par exemple, si une application commence à consommer davantage de bande passante sur le même réseau, le débit réel varie pendant la réplication. Si vous exécutez la commande GetThroughput à partir d’un serveur de configuration, l’outil ne reconnaît pas les machines virtuelles protégées ni la réplication en cours. Le débit mesuré obtenu sera différent si l’opération GetThroughput est exécutée lorsque les machines virtuelles protégées connaissent une activité élevée des données ou lorsque cette activité est faible.  Il est recommandé d’exécuter l’outil à différents moments pendant le profilage pour comprendre le débit pouvant être atteint à des moments différents. Dans le rapport, l’outil affiche le dernier débit mesuré.


##### <a name="example"></a>Exemple
ASRDeploymentPlanner.exe **-Operation** GetThroughput **-Directory** E:\vCenter1_ProfiledData **-VMListFile** E:\vCenter1_ProfiledData\ProfileVMList1.txt **-StorageAccountName** asrspfarm1 **-StorageAccountKey** by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

>[!NOTE]
>
> * Exécutez l’outil sur un serveur doté des mêmes caractéristiques de stockage et de processeur que le serveur de configuration.
>
> * Pour la réplication, configurez la bande passante qui est recommandée pour atteindre un RPO 100 % du temps. Même après la configuration de la bande passante appropriée, si vous ne constatez pas d’augmentation du débit atteint signalé par l’outil, vérifiez les points suivants :
>
> a. Vérifiez si un réseau Qualité de Service (QoS) limite le débit Azure Site Recovery.
>
> b. Vérifiez si votre coffre Azure Site Recovery se trouve dans la région Microsoft Azure physique prise en charge la plus proche pour réduire la latence du réseau.
>
> c. Vérifiez les caractéristiques du stockage local et cherchez à améliorer le matériel (par exemple, disque dur à disque SSD, etc.)
>
> d. Modifiez les paramètres Azure Site Recovery sur le serveur de processus pour [augmenter la quantité de bande passante réseau utilisée pour la réplication](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
>

##<a name="recommendations-with-desired-rpo-as-input"></a>Recommandations liées au RPO souhaité en tant qu’entrée

###<a name="profiled-data"></a>Données profilées

![Deployment Planner](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Profiled data period** est la durée d’exécution du profilage. Par défaut, l’outil prend toutes les données profilées pour le calcul, sauf si le rapport est généré pour une période spécifique à l’aide des options StartDate et EndDate pendant la génération du rapport.

**Server Name** est le nom ou l’adresse IP de l’hôte VMware vCenter ou ESXi dont le rapport des machines virtuelles est généré.

**Desired RPO** est l’objectif RPO de votre déploiement. Par défaut, la bande passante réseau requise est calculée pour les valeurs RPO de 15, 30 et 60 minutes. En fonction de la sélection, les valeurs concernées sont mises à jour sur la feuille. Si vous avez utilisé le paramètre DesiredRPOinMin lors de la génération du rapport, cette valeur est affichée dans cette liste déroulante «  Desired RPO ».

###<a name="profiling-overview"></a>Vue d’ensemble du profilage

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total Profiled Virtual Machines** est le nombre total de machines virtuelles dont les données profilées sont disponibles. Si le fichier VMListFile contient des noms de machines virtuelles qui n’ont pas été profilées, ces machines virtuelles ne sont pas prises en compte dans la génération de rapport et exclues du nombre total de machines virtuelles profilées.

**Compatible Virtual Machines** correspond au nombre de machines virtuelles pouvant être protégées dans Azure à l’aide d’Azure Site Recovery. Il s’agit du nombre total de machines virtuelles compatibles pour lesquelles la bande passante réseau requise, le nombre de comptes de stockage Azure, le nombre de cœurs Microsoft Azure et le nombre de serveurs de configuration et de serveurs de processus supplémentaires sont calculés. Les détails de chaque machine virtuelle compatible sont disponibles dans la feuille « Compatible VMs » du rapport.

**Incompatible Virtual Machines** correspond au nombre de machines virtuelles qui ne sont pas compatibles pour la protection assurée avec Azure Site Recovery. Les raisons de l’incompatibilité sont indiquées dans la section Machines virtuelles incompatibles indiquée ci-dessous. Si le fichier VMListFile contient des noms de machines virtuelles qui n’ont pas été profilées, ces machines virtuelles sont exclues du nombre de machines virtuelles incompatibles. Ces machines virtuelles sont répertoriées comme « Data not found » à la fin de la feuille « Incompatible VMs ».

**Desired RPO** est votre RPO souhaité, indiqué en minutes. Le rapport est généré pour trois valeurs RPO (15, 30 et 60 minutes), 15 minutes étant la valeur par défaut. La recommandation de la bande passante dans le rapport sera modifiée en fonction de votre sélection dans la liste déroulante « Desired RPO » dans l’angle supérieur droit de la feuille. Si vous avez généré le rapport à l’aide du paramètre -DesiredRPO avec une valeur personnalisée, cette valeur personnalisée s’affiche par défaut dans la liste déroulante « Desired RPO ».

###<a name="required-network-bandwidth-mbps"></a>Bande passante réseau requise (Mbits/s)

![Deployment Planner](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**To meet RPO 100% of the time :** il s’agit de la bande passante recommandée en Mbits/s à allouer pour atteindre votre RPO souhaité 100 % du temps. Cette quantité de bande passante doit être dédiée à la réplication différentielle à l’état stable de toutes vos machines virtuelles compatibles afin d’éviter toute violation de RPO.

**To meet RPO 90% of the time :** en raison de la tarification haut débit ou pour toute autre raison si vous ne pouvez pas configurer la bande passante nécessaire pour atteindre votre RPO souhaité 100 % du temps, vous pouvez choisir de configurer une quantité de bande passante inférieure pouvant satisfaire au RPO souhaité 90 % du temps. Pour comprendre les implications de la configuration de cette bande passante réduite, le rapport fournit une analyse par simulation du nombre et de la durée de violations de RPO à attendre.

**Achieved Throughput :** il s’agit du débit du serveur sur lequel vous avez exécuté la commande GetThroughput pour la région Microsoft Azure où se trouve le compte de stockage Azure. Il indique le débit approximatif qui peut être obtenu si vous protégez les machines virtuelles compatibles à l’aide d’Azure Site Recovery, sous réserve que les caractéristiques de réseau et de stockage du serveur de configuration/serveur de processus demeurent identiques à celles du serveur à partir duquel vous avez exécuté l’outil.    

Pour la réplication, vous devez configurer la bande passante qui est recommandée pour atteindre le RPO 100 % du temps. Même après la configuration de la bande passante appropriée, si vous ne constatez pas d’augmentation du débit atteint signalé par l’outil, vérifiez les points suivants :

a.    Vérifiez si un réseau Qualité de Service (QoS) limite le débit Azure Site Recovery.

b.    Vérifiez si votre coffre Azure Site Recovery se trouve dans la région Microsoft Azure physique prise en charge la plus proche pour réduire la latence du réseau.

c.    Vérifiez les caractéristiques du stockage local et cherchez à améliorer le matériel (par exemple, disque dur à disque SSD, etc.)

d. Modifiez les paramètres Azure Site Recovery dans le serveur de processus pour [augmenter la quantité de bande passante réseau utilisée pour la réplication](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Dans les cas où vous exécutez l’outil sur un serveur de configuration/serveur de processus qui possède déjà des machines virtuelles protégées, exécutez l’outil plusieurs fois, car le débit atteint change en fonction du volume de l’activité en cours de traitement à ce moment particulier.

Pour tous les déploiements d’Azure Site Recovery d’entreprise, l’utilisation d’[ExpressRoute](https://aka.ms/expressroute) est recommandée.

###<a name="required-azure-storage-accounts"></a>Comptes de stockage Azure requis
Ce graphique indique le nombre total de comptes de stockage Azure (standard et Premium) nécessaires pour protéger toutes les machines virtuelles compatibles.  Cliquez sur [Recommended VM placement plan](site-recovery-deployment-planner.md#vm-storage-placement) pour connaître le compte de stockage qu’il convient d’utiliser pour chaque machine virtuelle.  

![Deployment Planner](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

###<a name="required-number-of-azure-cores"></a>Required Number of Azure Cores
Il s’agit du nombre total de cœurs à configurer avant le basculement ou le test de basculement de l’ensemble des machines virtuelles compatibles. Si un nombre insuffisant de cœurs est disponible dans l’abonnement, Azure Site Recovery ne peut pas créer de machines virtuelles au moment du basculement ou du test de basculement.

![Deployment Planner](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

###<a name="required-on-premises-infrastructure"></a>Required On-premises Infrastructure
Il s’agit du nombre total de serveurs de processus supplémentaires et de serveurs de configuration à configurer pour protéger toutes les machines virtuelles compatibles. En fonction des [limites](https://aka.ms/asr-v2a-on-prem-components) prises en charge dans la configuration la plus importante, l’activité quotidienne ou le nombre maximal de machines virtuelles protégées (en partant d’une moyenne de trois disques par machine virtuelle), l’outil recommande des serveurs supplémentaires peu importe que le serveur de configuration ou le serveur de processus supplémentaire soit atteint en premier. Les détails de l’activité totale quotidienne et du nombre total de disques protégés se trouvent dans la feuille [Input](site-recovery-deployment-planner.md#input).

![Deployment Planner](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

###<a name="what-if-analysis"></a>Analyse de simulation
Cette analyse indique le nombre de violations susceptibles de se produire pendant la période de profilage lorsque vous configurez une bande passante plus faible pour que le RPO souhaité ne soit rempli que 90 % du temps. Une ou plusieurs violations de l’objectif RPO peuvent se produire sur un jour donné ; le graphique affiche le pic du RPO de la journée.
En vous appuyant sur cette analyse, vous pouvez décider si le nombre de violations de RPO tous les jours et d’accès RPO maximal par jour est acceptable avec la bande passante inférieure spécifiée. S’il est acceptable, vous pouvez allouer la bande passante inférieure pour la réplication. Sinon allouez la bande passante supérieure, comme suggéré pour atteindre le RPO souhaité 100 % du temps.

![Deployment Planner](./media/site-recovery-deployment-planner/what-if-analysis.png)

###<a name="recommended-vm-batch-size-for-initial-replication"></a>Recommended VM batch size for initial replication
Cette section recommande le nombre de machines virtuelles qui peuvent être protégées en parallèle pour exécuter une réplication initiale en 72 heures (valeur configurable : utilisez le paramètre GoalToCompleteIR comme heure de génération de rapport pour changer cette durée) avec la bande passante suggérée configurée pour remplir le RPO souhaité 100 % du temps.  Le graphique affiche une plage de valeurs de bande passante et un nombre de tailles de lot de machines virtuelles pour effectuer une réplication initiale en 72 heures en fonction de la taille moyenne de machine virtuelle détectée sur toutes les machines virtuelles compatibles.  

Dans la préversion publique, le rapport ne spécifie pas les machines virtuelles qui doivent être incluses dans un lot. Vous pouvez utiliser la taille de disque indiquée sur la feuille « Compatible VMs » pour déterminer la taille de chaque machine virtuelle et sélectionner vos machines virtuelles pour un lot ou les sélectionner en fonction des caractéristiques des charges de travail connues.  La durée d’exécution d’une réplication change proportionnellement en fonction de la taille de disque réelle des machines virtuelles, de l’espace disque utilisé et du débit réseau disponible.

![Deployment Planner](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

###<a name="growth-factor-and-percentile-values-used"></a>Growth factor and percentile values used
Cette section figurant en bas de la feuille indique la valeur de centile utilisé pour tous les compteurs de performances des machines virtuelles profilées (95e centile par défaut) et le facteur de croissance en % utilisé dans tous les calculs (30 % par défaut).

![Deployment Planner](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

##<a name="recommendations-with-available-bandwidth-as-input"></a>Recommandations relatives à la bande passante disponible comme entrée

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Vous pouvez vous trouver dans une situation dans laquelle vous ne pouvez pas configurer une bande passante supérieure à x Mbits/s pour la réplication Azure Site Recovery. L’outil vous permet d’entrer la bande passante disponible (à l’aide du paramètre -Bandwidth lors de la génération de rapport) et d’obtenir le RPO réalisable indiqué en minutes. Avec cette valeur de RPO réalisable, vous pouvez choisir de configurer une bande passante supplémentaire ou de vous satisfaire d’une solution de récupération d’urgence avec ce RPO.

![Deployment Planner](./media/site-recovery-deployment-planner/achievable-rpos.png)

##<a name="input"></a>Entrée
La page Input fournit une vue d’ensemble de l’environnement VMware profilé.

![Deployment Planner](./media/site-recovery-deployment-planner/Input.png)

**Start Date et End Date** sont les dates de début et de fin du profilage des données prises en compte pour la génération de rapport. Par défaut, la date de début est la date à laquelle le profilage démarre, et la date de fin est la date à laquelle le profilage s’arrête.  Il peut s’agir des valeurs StartDate et EndDate si le rapport est généré avec ces paramètres. Start Date et End Date : ce sont les dates de début et de fin du profilage des données prises en compte pour la génération de rapport. Par défaut, la date de début est la date à laquelle le profilage démarre, et la date de fin est la date à laquelle le profilage s’arrête.  Il peut s’agir des valeurs StartDate et EndDate si le rapport est généré avec ces paramètres.

**Total number of profiling days** est le nombre total de jours de profilage compris entre les dates de début et de fin pendant lesquelles le rapport est généré. Le nombre total de jours de profilage est le nombre total de jours de profilage compris entre les dates de début et de fin pendant lesquelles le rapport est généré.

**Number of compatible virtual machines** est le nombre total de machines virtuelles compatibles pour lesquelles la bande passante réseau requise, le nombre requis de comptes de stockage Azure, de cœurs Microsoft Azure et de serveurs de processus supplémentaires et de serveurs de configuration sont calculés.
Le nombre total de disques sur toutes les machines virtuelles compatibles est le nombre total de disques sur toutes les machines virtuelles compatibles. Ce nombre est utilisé comme nombre d’entrées pour déterminer le nombre de serveurs de processus supplémentaires et de serveurs de configuration à utiliser dans le déploiement.

**Average number of disks per compatible virtual machine** correspond au nombre moyen de disques calculé sur toutes les machines virtuelles compatibles.

**Average disk size (GB)** est la taille de disque moyenne calculée sur toutes les machines virtuelles compatibles.

**Desired RPO (minutes)** est le RPO par défaut ou la valeur transmise pour le paramètre DesiredRPO au moment de la génération de rapport pour estimer la bande passante requise.

**Desired bandwidth (Mbps)** est la valeur que vous avez transmise pour le paramètre Bandwidth au moment de la génération de rapport pour estimer le RPO réalisable.

**Observed typical data churn per day (GB)** correspond à l’activité moyenne des données observée tous les jours de profilage. Ce nombre est utilisé comme nombre d’entrées pour déterminer le nombre de serveurs de processus supplémentaires et de serveurs de configuration à utiliser dans le déploiement.


##<a name="vm-storage-placement"></a>VM-Storage placement

![Deployment Planner](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Storage Type** désigne le compte de stockage Azure Standard ou Premium utilisé pour répliquer toutes les machines virtuelles correspondantes, mentionnées dans la colonne VMs to Place.

**Suggested Prefix** représente le préfixe suggéré à trois caractères qui permet de nommer le compte de stockage Azure. Vous pouvez toujours utiliser votre propre préfixe, mais l’outil suggère de suivre la [convention d’affectation de noms des comptes de stockage Azure](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** indique à quoi votre nom de votre compte de stockage Azure doit ressembler après que vous avez inclus le préfixe proposé. Remplacez le nom écrit entre chevrons (<>) par votre entrée personnalisée.

**Log Storage Account :** tous les journaux de réplication sont stockés dans un compte de stockage Azure standard. Pour les machines virtuelles répliquées vers un compte de stockage Azure Premium, vous devez configurer un compte de stockage Azure standard supplémentaire pour le stockage des journaux. Un seul et même compte de stockage standard des journaux peut être utilisé par plusieurs comptes de stockage de réplication Premium. Les machines virtuelles répliquées vers les comptes de stockage standard utilisent un même compte de stockage pour les journaux.

**Suggested Log Account Name** indique à quoi votre nom de votre compte de stockage Azure des journaux doit ressembler après que vous avez inclus le préfixe proposé. Remplacez le nom écrit entre chevrons (<>) par votre entrée personnalisée.

**Placement Summary** fournit un résumé de la charge totale des machines virtuelles sur le compte de stockage Azure au moment de la réplication et du test de basculement/basculement. Ce résumé inclut le nombre total de machines virtuelles mappées au compte de stockage Azure, le total des E/S par seconde de lecture/écriture sur toutes les machines virtuelles placées dans ce compte de stockage Azure, le total des E/S par seconde d’écriture (réplication), la taille totale configurée sur tous les disques ainsi que le nombre total de disques.

**Virtual Machines to Place** répertorie toutes les machines virtuelles qui doivent être placées dans le compte de stockage Azure donné pour optimiser les performances et l’utilisation.

## <a name="compatible-vms"></a>Machines virtuelles compatibles
![Deployment Planner](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Name** représente le nom de la machine virtuelle ou l’adresse IP utilisés dans le paramètre VMListFile au moment de la génération de rapport. Cette colonne répertorie également les disques (VMDK) attachés aux machines virtuelles. Les machines virtuelles sur un serveur vCenter avec des noms ou des adresses IP en double sont mentionnées avec le nom d’hôte ESXi pour pouvoir les distinguer. L’hôte ESXi répertorié est l’hôte sur lequel la machine virtuelle était placée lorsque l’outil l’a découvert pour la première fois pendant le profilage.

**VM Compatibility** possède deux valeurs – Yes / Yes*. La valeur Yes* est destinée aux cas dans lesquels la machine virtuelle est une solution adaptée pour le [stockage Azure Premium](https://aka.ms/premium-storage-workload) avec l’activité élevée profilée/le disque IOPS s’inscrivant dans la catégorie P20 ou P30, mais la taille du disque provoque le mappage à la catégorie P10 ou P20. Stockage Azure décide du type de disque de stockage Premium auquel mapper un disque en fonction de sa taille. Exemple : un disque inférieur à 128 Go est de type P10, un disque compris entre 128 et 512 Go est de type P20, et un disque compris entre 512 Go et 1 023 Go est de type P30. Si les caractéristiques de charge de travail d’un disque l’inscrivent dans la catégorie P20 ou P30, mais que sa taille entraîne son mappage à un type de disque de stockage Premium inférieur, l’outil marque cette machine virtuelle de la valeur Yes* et vous recommande de modifier la taille du disque source pour l’inscrire dans le type de disque de stockage Premium recommandé ou de changer le type de disque cible après le basculement.
Le stockage est de type standard ou Premium.

**Suggested Prefix** représente le préfixe de compte de stockage Azure à trois caractères.

**Storage Account** est le nom utilisant le préfixe suggéré.

**R/W IOPS (with Growth Factor)** correspond aux opérations d’E/S par seconde de la charge de travail de pointe sur le disque (95e centile par défaut), y compris le facteur de croissance futur (30 % par défaut). Notez que les opérations d’E/S par seconde de lecture/écriture de la machine virtuelle ne correspondent pas toujours à la somme des E/S par seconde de lecture/écriture des disques individuels de la machine virtuelle, car les opérations d’E/S par seconde de lecture/écriture de pointe représentent le pic de la somme des E/S par seconde de lecture/écriture de ses disques individuels à chaque minute de la période de profilage.

**Data Churn in Mbps (with Growth Factor)** correspond au taux d’activité de pointe sur le disque (95e centile par défaut), y compris le facteur de croissance futur (30 % par défaut). Notez que le taux total d’activité des données de la machine virtuelle ne correspond pas toujours à la somme des taux d’activité des données des disques individuels de la machine virtuelle, car le taux d’activité de pointe de la machine virtuelle représente le pic de la somme du taux d’activité de ses disques individuels à chaque minute de la période de profilage.

**Azure VM Size** est la taille de machine virtuelle de Calcul Azure mappée idéale pour cette machine virtuelle locale. Ce mappage est effectué en fonction de la mémoire de la machine virtuelle locale, du nombre de disques/cœurs/cartes réseau et d’E/S par seconde de lecture/écriture. La recommandation est toujours la taille de machine virtuelle Azure la plus petite correspondant à toutes ces caractéristiques de machine virtuelle locale.

**Number of Disks** correspond au nombre total de disques (VMDK) de la machine virtuelle.

**Disk size (GB)** est la taille approvisionnée totale de tous les disques de la machine virtuelle. L’outil affiche également la taille des disques individuels de la machine virtuelle.

**Cores** est le nombre de cœurs de processeur de la machine virtuelle.

**Memory (MB)** correspond à la RAM de la machine virtuelle.

**NICs** est le nombre de cartes réseau de la machine virtuelle.

##<a name="incompatible-vms"></a>Machines virtuelles incompatibles

![Deployment Planner](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Name** représente le nom de la machine virtuelle ou l’adresse IP utilisés dans le paramètre VMListFile au moment de la génération de rapport. Cette colonne répertorie également les disques (VMDK) attachés aux machines virtuelles. Les machines virtuelles sur un serveur vCenter avec des noms ou des adresses IP en double sont mentionnées avec le nom d’hôte ESXi pour pouvoir les distinguer. L’hôte ESXi répertorié est l’hôte sur lequel la machine virtuelle était placée lorsque l’outil l’a découvert pour la première fois pendant le profilage.

**VM Compatibility** indique pourquoi la machine virtuelle spécifiée est incompatible avec une utilisation avec Azure Site Recovery. Les raisons sont présentées par disque incompatible de machine virtuelle. Il peut s’agir des raisons indiquées ci-après en fonction des [limites](https://aka.ms/azure-storage-scalbility-performance) de stockage Azure publiées.

* Taille de disque > 1 023 Go : pour le moment, Stockage Azure ne prend pas en charge des tailles de disque > 1 To.
* La taille de machine virtuelle totale (réplication+TFO) dépasse la limite de taille de compte de stockage Azure prise en charge (35 To) : cela se produit généralement s’il y a un seul disque dans la machine virtuelle dont les caractéristiques de performance dépassent les limites Microsoft Azure/Azure Site Recovery prises en charge maximales pour le stockage standard qui transmet la machine virtuelle dans la zone de stockage Premium. Néanmoins, la taille maximale prise en charge d’un compte de stockage Azure Premium est de 35 To, et une seule et même machine virtuelle protégée ne peut pas être protégée sur plusieurs comptes de stockage. Notez également que lorsqu’un TFO (test de basculement) est exécuté sur une machine virtuelle protégée, il s’exécute dans le compte de stockage où la réplication est en cours de traitement : nous devons donc configurer 2 fois la taille du disque pour que la réplication avance et que le test de basculement aboutisse en parallèle.
* Les E/S par seconde source excèdent la limite IOPS prise en charge par Stockage Azure qui est de 5 000 par disque.
* Les E/S par seconde source excèdent la limite IOPS prise en charge par Stockage Azure qui est de 80 000 par machine virtuelle.
* L’activité moyenne des données dépasse la limite d’activité des données prise en charge par Azure Site Recovery, qui est de 10 Mbits/s pour la taille d’E/S moyenne de disque.
* L’activité totale des données sur tous les disques de la machine virtuelle dépasse la limite d’activité des données maximale prise en charge par Azure Site Recovery, qui est de 54 Mbits/s par machine virtuelle.
* Les E/S par seconde de lecture moyennes effectives dépassent la limite IOPS prise en charge par Azure Site Recovery, qui est de 840 par disque.
* Le stockage calculé des captures instantanées dépasse la limite de stockage des captures instantanées prise en charge, qui est de 10 To.

**R/W IOPS (with Growth Factor)** correspond aux opérations d’E/S par seconde de la charge de travail de pointe sur le disque (95e centile par défaut), y compris le facteur de croissance futur (30 % par défaut). Notez que les opérations d’E/S par seconde de lecture/écriture de la machine virtuelle ne correspondent pas toujours à la somme des E/S par seconde de lecture/écriture des disques individuels de la machine virtuelle, car les opérations d’E/S par seconde de lecture/écriture de pointe représentent le pic de la somme des E/S par seconde de lecture/écriture de ses disques individuels à chaque minute de la période de profilage.

**Data Churn in Mbps (with Growth Factor)** correspond au taux d’activité de pointe sur le disque (95e centile par défaut), y compris le facteur de croissance futur (30 % par défaut). Notez que le taux total d’activité des données de la machine virtuelle ne correspond pas toujours à la somme des taux d’activité des données des disques individuels de la machine virtuelle, car le taux d’activité de pointe de la machine virtuelle représente le pic de la somme du taux d’activité de ses disques individuels à chaque minute de la période de profilage.

**Number of Disks** correspond au nombre total de disques (VMDK) de la machine virtuelle.

**Disk size (GB)** est la taille approvisionnée totale de tous les disques de la machine virtuelle. L’outil affiche également la taille des disques individuels de la machine virtuelle.

**Cores** est le nombre de cœurs de processeur de la machine virtuelle.

**Memory (MB)** correspond à la RAM de la machine virtuelle.

**NICs** est le nombre de cartes réseau de la machine virtuelle.


##<a name="azure-site-recovery-limits"></a>Limites Azure Site Recovery

**Stockage de réplication cible** | **Taille d’E/S moyenne de disque source** |**Activité des données moyenne de disque source** | **Total de l’activité des données de disque source par jour**
---|---|---|---
Stockage Standard | 8 Ko    | 2 Mo/s | 168 Go par disque
Disque P10 Premium | 8 Ko    | 2 Mo/s | 168 Go par disque
Disque P10 Premium | 16 Ko | 4 Mo/s |    336 Go par disque
Disque P10 Premium | 32 Ko ou plus | 8 Mo/s | 672 Go par disque
Disque P20/P30 Premium | 8 Ko    | 5 Mo/s | 421 Go par disque
Disque P20/P30 Premium | 16 Ko ou plus |10 Mo/s    | 842 Go par disque


Il s’agit de moyennes en partant sur un chevauchement d’E/S de 30 %. Azure Site Recovery est capable de gérer un débit plus élevé en fonction du ratio de chevauchement, de tailles d’écriture plus grandes et du comportement d’E/S des charges de travail réelles. Les nombres indiqués ci-dessus partent de l’hypothèse d’un backlog de 5 minutes environ. Autrement dit, une fois que les données sont chargées, elles sont traitées et un point de récupération est créé en 5 minutes.

Les limites publiées ci-dessus sont basées sur nos tests, mais ne peuvent pas couvrir toutes les combinaisons d’E/S d’application possibles. Les résultats réels varient en fonction de la combinaison d’E/S de votre application. Pour de meilleurs résultats, même après la planification du déploiement, il est toujours recommandé d’effectuer des tests d’application approfondis à l’aide du test de basculement pour obtenir une image réelle des performances.

## <a name="how-to-update-the-deployment-planner"></a>Comment mettre à jour Deployment Planner ?
[Téléchargez](site-recovery-deployment-planner.md#download) la dernière version d’Azure Site Recovery Deployment Planner. Copiez le fichier zip sur le serveur sur lequel vous souhaitez l’exécuter. Extrayez le fichier zip.
Si vous disposez déjà de la précédente version de Deployment Planner et que le profilage est en cours, vous n’avez pas besoin d’arrêter le profilage, sauf si la nouvelle version possède le correctif de profilage. Si la publication contient des correctifs inclus dans le composant de profilage, il est recommandé d’arrêter le profilage à l’aide de l’ancienne version, puis de le relancer en utilisant la nouvelle version. Lorsque vous commencez le profilage à l’aide de la nouvelle version, vous devez transmettre le même chemin de répertoire de sortie afin que l’outil ajoute les données de profil aux fichiers existants et que le jeu complet de données profilées soit utilisé pour la génération de rapports. Si vous transmettez un répertoire de sortie différent, des fichiers seront créés et les anciennes données profilées ne pourront pas être utilisées lors de la génération de rapports.<br> Chaque mise à jour est une mise à jour cumulative avec un fichier zip. Vous n’avez pas besoin de copier les fichiers de la nouvelle version dans le dossier de la version précédente pour pouvoir les utiliser. Vous pouvez utiliser un nouveau dossier.


##<a name="version-history"></a>Historique des versions
### <a name="11"></a>1.1
Mise à jour du 9 mars 2017 <br>

Les problèmes suivants ont été résolus :<br>

* Impossibilité de profiler de machines virtuelles si le serveur vCenter possède au moins deux machines virtuelles ayant le même nom/la même adresse IP sur différents hôtes ESXi.<br>
* La copie et la recherche ont été désactivées pour les feuilles « Compatible VMs » et « Incompatible VMs ».


### <a name="10"></a>1.0 
Mise à jour du 23 février 2017 

La préversion publique d’Azure Site Recovery 1.0 rencontre les problèmes connus suivants qui seront traités dans les prochaines mises à jour.

* L’outil fonctionne uniquement pour le scénario VMware vers Azure, mais pas pour les déploiements Hyper-V vers Azure. Pour ce dernier scénario, utilisez [l’outil de planification de la capacité Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* L’opération GetThroughput n’est pas prise en charge dans les régions Microsoft Azure US Government et Chine.
* L’outil ne peut pas profiler de machines virtuelles si le serveur vCenter possède au moins deux machines virtuelles ayant le même nom/la même adresse IP sur différents hôtes ESXi. Dans cette version, l’outil ignore le profilage des noms/adresses IP des machines virtuelles en double dans le fichier VMListFile. La solution de contournement consiste à profiler des machines virtuelles avec un hôte ESXi au lieu d’un serveur vCenter. Vous devez exécuter une instance par hôte ESXi.

