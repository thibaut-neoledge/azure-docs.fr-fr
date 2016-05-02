<properties
 pageTitle="Envoyer des travaux à un cluster HPC Pack dans Azure | Microsoft Azure"
 description="Apprendre à configurer un ordinateur local pour envoyer des travaux vers un cluster HPC Pack dans Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="04/14/2016"
 ms.author="danlep"/>

# Envoyer des travaux HPC à partir d'un ordinateur local vers un cluster HPC Pack déployé dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configurez un ordinateur client local sous Windows pour exécuter les outils de soumission de travaux HPC Pack qui communiquent avec un cluster HPC Pack dans Azure via HTTPS. Cela permet aux différents utilisateurs de cluster d'envoyer facilement des travaux à un cluster HPC Pack cloud sans avoir à se connecter directement à la machine virtuelle du nœud principal ni d'accéder à un abonnement Azure pour lancer les outils de soumission de travaux.

![Envoyer un travail vers un cluster dans Azure][jobsubmit]

## Composants requis

* **Nœud principal HPC Pack déployé dans une machine virtuelle Azure** : nous vous recommandons d’utiliser des outils automatisés, tels qu’un [modèle de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) ou un [script Azure PowerShell](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour déployer le nœud principal et le cluster. Vous aurez besoin du nom DNS du nœud principal et des informations d’identification d’un administrateur de cluster pour effectuer les étapes décrites dans cet article.

* **Support d’installation du HPC Pack** : le package d’installation gratuit de la dernière version du HPC Pack (HPC Pack 2012 R2) est disponible dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Veillez à télécharger la même version du HPC Pack qui est installée sur la machine virtuelle du nœud principal.

* **Ordinateur client** : vous aurez besoin d’un ordinateur client Windows ou Windows Server qui peut exécuter des utilitaires clients HPC Pack (voir [Configuration requise](https://technet.microsoft.com/library/dn535781.aspx)). Si vous souhaitez uniquement utiliser le portail web de HPC Pack ou l’API REST pour envoyer des travaux, vous pouvez utiliser l’ordinateur client de votre choix.


## Étape 1 : Installer et configurer les composants web sur le nœud principal

Pour activer une interface REST afin d’envoyer des travaux au cluster via HTTPS, installez et configurez les composants web HPC Pack sur le nœud principal HPC Pack, s’ils ne sont pas déjà configurés. Tout d’abord, installez les composants web en exécutant le fichier d’installation HpcWebComponents.msi. Ensuite, configurez les composants en exécutant le script HPC PowerShell **Set-hpcwebcomponents.ps1**.

Pour obtenir des procédures détaillées, consultez [Installer les composants web de Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Certains modèles de démarrage rapide Azure pour HPC Pack installent et configurent automatiquement les composants web. Si vous utilisez le [script de déploiement du HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour créer le cluster, vous pouvez éventuellement installer et configurer les composants web dans le cadre du déploiement.

**Pour installer les composants web**

1. Connectez-vous à la machine virtuelle du nœud principal en utilisant les informations d’identification d’un administrateur de cluster.

2. À partir du dossier d’installation de HPC Pack, exécutez HpcWebComponents.msi sur le nœud principal.

3. Suivez les étapes de l’assistant pour installer les composants web

**Pour configurer les composants web**

1. Sur le nœud principal, démarrez HPC PowerShell en tant qu’administrateur.

2. Pour modifier le répertoire et choisir l’emplacement du script de configuration, tapez la commande suivante :

    ```
    cd $env:CCP_HOME\bin
    ```
3. Pour configurer l’interface REST et démarrer le service web HPC, tapez la commande suivante :

    ```
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Lorsque vous êtes invité à sélectionner un certificat, choisissez le certificat qui correspond au nom DNS public du nœud principal. Par exemple, si vous utilisez le script de déploiement HPC Pack IaaS pour créer le cluster, le nom du certificat est au format CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net. Si vous utilisez un modèle de démarrage rapide Azure, le nom du certificat est au format CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.azure.

    >[AZURE.NOTE] Vous devez sélectionner ce certificat pour envoyer des travaux ultérieurement au nœud principal à partir d’un ordinateur local. Ne sélectionnez pas ou ne configurez pas un certificat qui correspond au nom d’ordinateur du nœud principal dans le domaine Active Directory (par exemple, CN=*MyHPCHeadNode.HpcAzure.local*).

5. Pour configurer le portail web de soumission de travaux, tapez la commande suivante :

    ```
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Une fois le script terminé, arrêtez et redémarrez le service de planification de travaux HPC en tapant ce qui suit :

    ```
    net stop hpcscheduler
    net start hpcscheduler
    ```

## Étape 2 : Installer les utilitaires clients HPC Pack sur un ordinateur local

Si vous souhaitez installer les utilitaires du client HPC Pack, téléchargez les fichiers d'installation (installation complète) de HPC Pack à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024) sur l'ordinateur client. Au début de l’installation, choisissez l’option d’installation des utilitaires du client HPC Pack.

Pour utiliser les outils clients du HPC Pack pour envoyer des travaux à la machine virtuelle du nœud principal, vous devez également exporter un certificat à partir du nœud principal et l’installer sur l’ordinateur client. Vous aurez besoin du certificat au format .CER.

**Pour exporter le certificat à partir du nœud principal**

1. Sur le nœud principal, ajoutez le composant logiciel enfichable Certificats à une Console de gestion Microsoft pour le compte de l’ordinateur local. Pour savoir comment ajouter le composant logiciel enfichable, consultez [Ajouter le composant logiciel enfichable Certificats à une console MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. Dans l’arborescence de la console, développez **Certificats – Ordinateur Local**, puis **Personnel**, et cliquez sur **Certificats**.

3. Localisez le certificat que vous avez configuré pour les composants web HPC Pack à l'[Étape 1 : Installer et configurer les composants web sur le nœud principal](#step-1:-install-and-configure-the-web-components-on-the-head-node) (par exemple, CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net).

4. Cliquez avec le bouton droit sur le certificat, cliquez sur **Toutes les tâches**, puis sur **Exporter**.

5. Dans l’Assistant Exportation de certificat, cliquez sur **Suivant**, puis assurez-vous que l’option **Non, ne pas exporter la clé privée** est cochée.

6. Suivez les étapes restantes de l’Assistant pour exporter le certificat au format binaire codé DER X.509 (.CER).


**Pour importer le certificat sur l’ordinateur client**


1. Copiez le certificat que vous avez exporté à partir du nœud principal dans un dossier de l’ordinateur client.

2. Sur l’ordinateur client, exécutez certmgr.msc.

3. Dans le Gestionnaire de certificats, développez **Certificats – Utilisateur actuel**, puis **Autorités de certification racines de confiance**, cliquez avec le bouton droit sur **Certificats**, cliquez sur **Toutes les tâches**, puis sur **Importer**.

4. Dans l'Assistant Importation de certificat, cliquez sur **Suivant** et suivez les étapes pour importer le certificat que vous avez exporté à partir du nœud principal vers le magasin racine des autorités de certification approuvées.



>[AZURE.SECURITY] Un avertissement de sécurité peut s’afficher lorsque l’autorité de certification sur le nœud principal n’est pas reconnue par l’ordinateur client. À des fins de test, vous pouvez ignorer cet avertissement et terminer l’importation du certificat.

## Étape 3 : Exécuter des travaux test sur le cluster

Pour vérifier votre configuration, essayez d’exécuter des travaux sur le cluster dans Azure à l’aide de l’ordinateur local qui exécute les utilitaires clients HPC Pack. Par exemple, vous pouvez utiliser les commandes de ligne de commande ou les outils d’interface utilisateur graphique HPC Pack pour envoyer des travaux au cluster. Vous pouvez également utiliser un portail web pour envoyer des travaux.


**Pour exécuter des commandes d’envoi de travail sur l’ordinateur client**


1. Sur l'ordinateur client, démarrez une invite de commandes.

2. Tapez un exemple de commande. Par exemple, pour répertorier tous les travaux sur le cluster, tapez une commande semblable à l'une des options suivantes, selon le nom DNS complet du nœud principal :

    ```
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all

    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Utilisez le nom DNS complet du nœud principal, et non l’adresse IP, dans l’URL du planificateur. Si vous spécifiez l’adresse IP, vous verrez une erreur du type : « Le certificat de serveur doit utiliser une chaîne de confiance valide ou être placé dans le magasin racine de confiance ».

3. Quand vous y êtes invité, tapez le nom d'utilisateur (au format &lt;NomDomaine&gt;\\&lt;NomUtilisateur&gt;) et le mot de passe de l'administrateur de cluster HPC ou d'un autre utilisateur de cluster que vous avez configuré. Vous pouvez choisir de stocker les informations d’identification localement pour effectuer d’autres opérations.

    Une liste de travaux s’affiche.


**Pour utiliser le Gestionnaire de travaux HPC sur l’ordinateur client**

1. Si vous n’avez pas précédemment stocké les informations d’identification de domaine pour un utilisateur de cluster sur l’ordinateur client lorsque vous avez envoyé le travail, vous pouvez ajouter ces informations dans le Gestionnaire d’informations d’identification.

    a. Dans le panneau de configuration de l’ordinateur client, démarrez le Gestionnaire d’informations d’identification.

    b. Cliquez sur **Informations d’identification Windows**, puis sur **Ajouter des informations d’identification génériques**.

    c. Spécifiez l'adresse Internet (par exemple https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler ou https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com/HpcScheduler)) et fournissez le nom d'utilisateur (au format &lt;NomDomaine&gt;\\&lt;NomUtilisateur&gt;) et le mot de passe de l'administrateur du cluster HPC ou d'un autre utilisateur du cluster que vous avez configuré.

2. Sur l’ordinateur client, démarrez le Gestionnaire de travaux HPC.

3. Dans la boîte de dialogue **Sélectionner le nœud principal**, tapez l'URL du nœud principal dans Azure (par exemple, https://&lt;HeadNodeDnsName&gt;.cloudapp.net ou https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com)).

    Le Gestionnaire de travaux HPC s’ouvre et affiche une liste de travaux sur le nœud principal.

**Pour utiliser le portail web exécuté sur le nœud principal**

1. Ouvrez un navigateur web sur l'ordinateur client et tapez une des options suivantes, selon le nom DNS complet du nœud principal :

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal

    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Dans la boîte de dialogue de sécurité qui s’affiche, tapez les informations d’identification de domaine de l’administrateur de cluster HPC. (Vous pouvez également ajouter d’autres utilisateurs de cluster dans des rôles différents. Consultez [Gestion des utilisateurs du cluster](https://technet.microsoft.com/library/ff919335.aspx).)

    Le portail web s’ouvre sur l’affichage de liste de travaux.

3. Pour envoyer un exemple de travail qui retourne la chaîne « Hello World » à partir du cluster, cliquez sur **Nouveau travail** dans le cadre de gauche.

4. Dans la page **Nouveau travail**, sous **À partir des pages d’envoi**, cliquez sur **HelloWorld**. La page d’envoi de travail s’affiche.

5. Cliquez sur **Envoyer**. Si vous y êtes invité, fournissez les informations d’identification de domaine de l’administrateur de cluster HPC. Le travail est envoyé et l’ID du travail s’affiche dans la page **Mes travaux**.

6. Pour afficher les résultats du travail que vous avez envoyé, cliquez sur l’ID du travail, puis sur **Afficher les tâches** pour afficher la sortie de commande (sous **Sortie**).

## Étapes suivantes

* Vous pouvez également envoyer des travaux au cluster Azure avec l’[API REST du HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Si vous voulez envoyer des travaux de cluster à partir d'un client Linux, consultez l'exemple Python dans le [Kit de développement logiciel (SDK) et l'exemple de code HPC Pack 2012 R2](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png

<!---HONumber=AcomDC_0420_2016-->