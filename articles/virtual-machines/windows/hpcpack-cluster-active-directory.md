---
title: "Cluster HPC Pack avec Azure Active Directory | Microsoft Docs"
description: "Apprenez à intégrer un cluster HPC Pack 2016 dans Azure avec Azure Active Directory"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c5a06a9c810349b1bcce01c7f73563941a5af0ed
ms.lasthandoff: 03/31/2017


---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Gérer un cluster HPC Pack dans Azure avec Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) prend en charge l’intégration avec [Azure Active Directory](../../active-directory/index.md) (Azure AD) pour les administrateurs qui déploient un cluster HPC Pack dans Azure.



Effectuez la procédure décrite dans cet article pour les tâches de haut niveau suivantes : 
* Intégrer manuellement votre cluster HPC Pack avec votre locataire Azure AD
* Gérer et planifier des travaux dans votre cluster HPC Pack dans Azure 

L’intégration d’une solution de cluster HPC Pack avec Azure AD suit la procédure standard permettant d’intégrer d’autres applications et services. Cet article suppose que vous êtes familiarisé avec la gestion des utilisateurs de base dans Azure AD. Pour plus d’informations et du contexte, voir la [documentation Azure Active Directory](../../active-directory/index.md) et les sections suivantes.

## <a name="benefits-of-integration"></a>Avantages de l’intégration


Azure Active Directory (Azure AD) est le service de gestion des annuaires et des identités basé sur le cloud mutualisé qui fournit un accès via l’authentification unique (SSO) aux solutions cloud.

L’intégration d’un cluster HPC Pack avec Azure AD peut vous aider à atteindre les objectifs suivants :

* Supprimer le contrôleur de domaine Active Directory traditionnel du cluster HPC Pack. Cela peut aider à réduire les coûts de maintenance du cluster si ce n’est pas nécessaire pour votre entreprise, ainsi qu’à accélérer le déploiement.
* Exploiter les avantages suivants fournis par Azure AD :
    *   Authentification unique 
    *   Utilisation d’une identité AD locale pour le cluster HPC Pack dans Azure 

    ![Environnement Azure Active Directory](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Composants requis
* **Cluster HPC Pack 2016 déployé dans des machines virtuelles** : pour connaître les étapes, consultez [Déployer un cluster HPC Pack 2016 dans Azure](hpcpack-2016-cluster.md). Vous avez besoin du nom DNS du nœud principal et des informations d’identification d’un administrateur de cluster pour effectuer les étapes décrites dans cet article.

  > [!NOTE]
  > L’intégration d’Azure Active Directory n’est pas prise en charge dans les versions de HPC Pack antérieures au HPC Pack 2016.



* **Ordinateur client** : vous avez besoin d’un ordinateur client Windows ou Windows Server pour exécuter des utilitaires clients HPC Pack. Si vous souhaitez uniquement utiliser le portail web de HPC Pack ou l’API REST pour envoyer des travaux, vous pouvez utiliser l’ordinateur client de votre choix.

* **Utilitaires clients HPC Pack** : installez-les sur l’ordinateur client à l’aide du package d’installation gratuit disponible dans le Centre de téléchargement Microsoft.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Étape 1 : Inscrire le serveur de cluster HPC avec votre locataire Azure AD
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Cliquez sur **Active Directory** dans le menu de gauche, puis sur le répertoire souhaité dans votre abonnement. Vous devez être autorisé à accéder aux ressources du répertoire.
3. Cliquez sur **Utilisateurs** et vérifiez que des comptes utilisateur ont déjà été créés ou configurés.
4. Cliquez sur **Applications** > **Ajouter**, puis cliquez sur **Ajouter une application développée par mon organisation**. Entrez les informations suivantes dans l’Assistant :
    * **Nom** : HPCPackClusterServer.
    * **Type** : sélectionnez **Application web et/ou API web**.
    * **URL de connexion** : entrez l’URL de base de l’exemple, qui est par défaut `https://hpcserver`.
    * **URI ID d’application** - `https://<Directory_name>/<application_name>`. Remplacez `<Directory_name`> par le nom complet de votre locataire Azure AD, par exemple `hpclocal.onmicrosoft.com`, puis remplacez `<application_name>` par le nom que vous avez précédemment choisi.

5. Après avoir ajouté l’application, cliquez sur **Configurer**. Configurez les propriétés suivantes :
    * Sélectionnez **Oui** pour **Application mutualisée**.
    * Sélectionnez **Oui** pour **Affectation de l'utilisateur requise pour accéder à l'application**.

6. Cliquez sur **Enregistrer**. Une fois l’enregistrement terminé, cliquez sur **Gérer le manifeste**. Cette action permet de télécharger le fichier JSON du manifeste de votre application. Modifiez le manifeste téléchargé en recherchant le paramètre `appRoles` et en ajoutant le rôle d’application suivant :
    ```json
    "appRoles": [
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "displayName": "HpcAdminMirror",
        "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "description": "HpcAdminMirror",
        "value": "HpcAdminMirror"
        },
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "description": "HpcUsers",
        "displayName": "HpcUsers",
        "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "value": "HpcUsers"
        }
    ],
    ```
7. Enregistrez le fichier . Dans le portail, cliquez sur **Gérer le manifeste** > **Télécharger le manifeste**. Vous pouvez ensuite télécharger le manifeste modifié.
8. Cliquez sur **Utilisateurs**, sélectionnez un utilisateur, puis cliquez sur **Affecter**. Affectez l’un des rôles disponibles (HpcUsers ou HpcAdminMirror) à l’utilisateur. Répétez cette étape avec des utilisateurs supplémentaires dans le répertoire. Pour plus d’informations générales sur les utilisateurs de cluster, consultez [Gestion des utilisateurs de clusters](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).

   > [!NOTE] 
   > Pour gérer les utilisateurs, nous vous recommandons d’utiliser le panneau de la version préliminaire d’Azure Active Directory dans le [portail Azure](https://portal.azure.com).
   >


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Étape 2 : Inscrire le client de cluster HPC avec votre locataire Azure AD

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Cliquez sur **Active Directory** dans le menu de gauche, puis sur le répertoire souhaité dans votre abonnement. Vous devez être autorisé à accéder aux ressources du répertoire.
3. Cliquez sur **Applications** > **Ajouter**, puis cliquez sur **Ajouter une application développée par mon organisation**. Entrez les informations suivantes dans l’Assistant :

    * **Nom** : HPCPackClusterClient.
    * **Type** : sélectionnez **Application cliente native**
    * **URI de redirection** - `http://hpcclient`

4. Après avoir ajouté l’application, cliquez sur **Configurer**. Copiez l’**ID client** et enregistrez-le. Vous en aurez besoin plus tard lors de la configuration de votre application.

5. Dans **Autorisations pour d’autres applications**, cliquez sur **Ajouter une application**. Recherchez et ajoutez l’application HpcPackClusterServer (créée à l’étape 1).

6. Dans la liste déroulante **Autorisations déléguées**, sélectionnez **Accéder à HpcClusterServer**. Cliquez ensuite sur **Enregistrer**.


## <a name="step-3-configure-the-hpc-cluster"></a>Étape 3 : Configurer le cluster HPC

1. Connectez-vous au nœud principal HPC Pack 2016 dans Azure.

2. Démarrez PowerShell HPC.

3. Exécutez la commande suivante :

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    où

    * `AADTenant` spécifie le nom de locataire Azure AD, tel que `hpclocal.onmicrosoft.com`.
    * `AADClientAppId` spécifie l’ID client de l’application créée à l’étape 2.

4. Redémarrez le service HpcSchedulerStateful.

    Dans un cluster avec plusieurs nœuds principaux, vous pouvez exécuter les commandes PowerShell suivantes sur le nœud principal pour basculer le réplica principal pour le service HpcSchedulerStateful :

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName “fabric:/HpcApplication/SchedulerStatefulService”

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Étape 4 : Gérer et envoyer des travaux à partir du client

Pour installer les utilitaires clients HPC Pack sur votre ordinateur, téléchargez les fichiers d’installation (installation complète) de HPC Pack 2016 à partir du Centre de téléchargement Microsoft. Au début de l’installation, choisissez l’option d’installation des **utilitaires du client HPC Pack**.

Pour préparer l’ordinateur client, installez le certificat utilisé au cours de l’[installation du cluster HPC](hpcpack-2016-cluster.md) sur l’ordinateur client. Utilisez les procédures de gestion de certificats Windows standard pour installer le certificat public dans le magasin **Certificats - Utilisateur actuel** > **Autorités de certification racines de confiance**. 

Vous pouvez maintenant exécuter les commandes HPC Pack ou utiliser l’interface utilisateur graphique du Gestionnaire de travaux HPC Pack pour soumettre et gérer des travaux de cluster en utilisant le compte Azure AD. Pour les options d’envoi de travaux, consultez [Envoyer des travaux HPC vers un cluster HPC Pack déployé dans Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Lorsque vous essayez de vous connecter au cluster HPC Pack dans Azure pour la première fois, une fenêtre contextuelle s’affiche. Entrez vos informations d’identification Azure AD pour vous connecter. Le jeton est ensuite mis en cache. Les connexions ultérieures au cluster dans Azure utilisent le jeton en cache, sauf en cas de modification de l’authentification ou d’effacement du cache.
>
  
Par exemple, après avoir effectué les étapes précédentes, vous pouvez interroger les travaux à partir d’un client local comme suit :

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Applets de commande utiles pour l’envoi de travaux avec l’intégration Azure AD 

### <a name="manage-the-local-token-cache"></a>Gérer le cache local de jetons

HPC Pack 2016 fournit deux nouvelles applets de commande HPC PowerShell pour gérer le cache local de jetons. Ces applets de commande sont utiles pour envoyer des travaux en mode non interactif. Voir l’exemple suivant :

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Définir les informations d’identification pour envoyer des travaux à l’aide du compte Azure AD 

Parfois, vous pouvez être amené à exécuter le travail sous l’utilisateur du cluster HPC (pour un cluster HPC joint à un domaine, exécuter en tant qu’utilisateur d’un domaine ; pour un cluster HPC non joint à un domaine, exécuter en tant qu’utilisateur local sur le nœud principal).

1. Exécutez les commandes suivantes pour définir les informations d’identification :

    ```powershell
    $localUser = “<username>”

    $localUserPassword=”<password>”

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Envoyez ensuite le travail comme suit. Le travail et la tâche s’exécutent sous $localUser sur les nœuds de calcul.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Si `–Credential` n’est pas spécifié avec `Submit-HpcJob`, le travail ou la tâche s’exécute sous un utilisateur local mappé en tant que compte Azure AD. Le cluster HPC crée un utilisateur local avec le même nom que le compte Azure AD pour exécuter la tâche.
    
3. Définissez les données étendues pour le compte Azure AD. Ceci est utile lors de l’exécution d’un travail MPI sur des nœuds Linux à l’aide du compte Azure AD.

   * Définir les données étendues pour le compte Azure AD

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Définir les données étendues et exécuter en tant qu’utilisateur du cluster HPC
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```


