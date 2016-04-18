



Selon votre environnement et vos choix, le script peut créer toute l’infrastructure de clusters, y compris le réseau virtuel Azure, les comptes de stockage, les services cloud, le contrôleur de domaine, les bases de données SQL locales ou distantes, le nœud principal, les nœuds du répartiteur, les nœuds de calcul et les nœuds de service cloud Azure (« nœuds d’extension » ou PaaS). Sinon, le script peut utiliser l’infrastructure Azure existante, puis créer le nœud principal de cluster HPC, les nœuds du répartiteur, les nœuds de calcul et les nœuds d’extension Azure.


Pour des informations générales sur la planification d’un cluster HPC Pack, consultez [Évaluation du produit et planification](https://technet.microsoft.com/library/jj899596.aspx) et [Mise en route](https://technet.microsoft.com/library/jj899590.aspx) dans la bibliothèque TechNet HPC Pack.



## Configuration requise

* **Abonnement Azure** : vous pouvez utiliser un abonnement dans le service Azure Global ou Azure Chine. Vos limites d’abonnement affecteront le nombre et le type de nœuds de cluster que vous pouvez déployer. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes du service](../articles/azure-subscription-service-limits.md).


* **Ordinateur client Windows avec Azure PowerShell 0.8.7 ou ultérieur installé et configuré**. Consultez l’article [Installation et configuration d’Azure PowerShell](../articles/powershell-install-configure.md). Le script s’exécute en mode Azure Service Management (asm).


* **Script de déploiement du HPC Pack IaaS** : téléchargez et décompressez la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vérifiez la version du script en exécutant `New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur la version 4.4.0 du script.

* **Fichier de configuration de script** : vous devez créer un fichier XML qui sera utilisé par le script pour configurer le cluster HPC. Pour obtenir plus d’informations et des exemples, consultez les sections appropriées, plus loin dans cet article.


## Syntaxe

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]Vous devez exécuter chacun des scripts en tant qu’administrateur.

### Paramètres

* **ConfigFile** : spécifie le chemin d’accès du fichier de configuration pour décrire le cluster HPC. Pour plus d’informations, consultez [Fichier de configuration](#Configuration-file) dans cette rubrique, ou le fichier Manual.rtf, dans le dossier contenant le script.

* **AdminUserName** : spécifie le nom d’utilisateur. Si la forêt de domaines est créée par le script, cela devient le nom d’utilisateur de l’administrateur local pour toutes les machines virtuelles, ainsi que le nom d’administrateur de domaine. Si la forêt de domaines existe déjà, cela indique l’utilisateur du domaine comme nom d’utilisateur d’administrateur local pour installer HPC Pack.

* **AdminPassword** : spécifie le mot de passe de l’administrateur. S’il n’a pas été spécifié dans la ligne de commande, le script vous invite à entrer le mot de passe.

* **HPCImageName** (facultatif) : spécifie le nom d’image de la machine virtuelle HPC Pack à utiliser pour le déploiement du cluster HPC. Ce doit être une image HPC Pack fournie par Microsoft à partir d’Azure Marketplace. S’il n’a pas été spécifié (recommandé dans la plupart des cas), le script choisit la dernière image HPC Pack publiée.

    >[AZURE.NOTE] Le déploiement échouera si vous ne spécifiez pas d’image HPC Pack valide.

* **LogFile** (facultatif) : spécifie le chemin du fichier journal de déploiement. S’il n’a pas été spécifié, le script crée un fichier journal dans le répertoire temp de l’ordinateur qui exécute le script.

* **Force** (facultatif) : supprime toutes les invites de confirmation.

* **NoCleanOnFailure** (facultatif) : spécifie que les machines virtuelles Azure qui n’ont pas été correctement déployées ne soient pas supprimées. Vous devez supprimer manuellement ces machines virtuelles avant de réexécuter le script pour poursuivre le déploiement, sinon le déploiement peut échouer.

* **PSSessionSkipCACheck** (facultatif) : pour chaque service cloud avec des machines virtuelles déployées par ce script, un certificat auto-signé est automatiquement généré par Azure, et toutes les machines virtuelles du service cloud utilisent ce certificat en tant que certificat Windows Remote Management (WinRM) par défaut. Pour déployer des fonctionnalités HPC dans ces machines virtuelles Azure, le script par défaut installe temporairement ces certificats dans le magasin Ordinateur local\\Autorités de certification racines de confiance de l’ordinateur client pour supprimer l’erreur de sécurité d’autorité de certification non approuvée pendant l’exécution du script ; les certificats sont supprimés quand le script se termine. Si ce paramètre est spécifié, les certificats ne sont pas installés sur l’ordinateur client et l’avertissement de sécurité est supprimé.

    >[AZURE.IMPORTANT] Ce paramètre n’est pas recommandé pour les déploiements de production.

### Exemple

L’exemple suivant crée un cluster HPC Pack à l’aide du fichier de configuration *MyConfigFile.xml* et spécifie les informations d’identification d’administration pour l’installation du cluster.

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### Considérations supplémentaires

* Le script utilise l’image de machine virtuelle HPC Pack dans Azure Marketplace pour créer le nœud principal du cluster. La dernière image est basée sur Windows Server 2012 R2 Datacenter avec HPC Pack 2012 R2 Update 3.

* Le script peut éventuellement activer la soumission de travaux via le portail web HPC Pack ou l’API REST du HPC Pack.

* Le script peut éventuellement exécuter des scripts de pré- et post-configuration personnalisés sur le nœud principal si vous souhaitez installer des logiciels supplémentaires ou configurer d’autres paramètres.


## Fichier de configuration

Le fichier de configuration pour le script de déploiement est un fichier XML. Le fichier de schéma HPCIaaSClusterConfig.xsd se trouve dans le dossier de script de déploiement du HPC Pack IaaS. **IaaSClusterConfig** est l’élément racine du fichier de configuration qui contient les éléments enfants décrits en détail dans le fichier Manual.rtf dans le dossier de script de déploiement. Pour consulter des fichiers d’exemple pour les différents scénarios, consultez [Exemples de fichiers de configuration](#Example-configuration-files) dans cet article.

<!---HONumber=AcomDC_0406_2016-->