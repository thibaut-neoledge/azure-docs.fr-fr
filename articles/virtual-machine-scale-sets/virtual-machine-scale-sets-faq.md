---
title: FAQ sur les groupes de machines virtuelles identiques Azure | Microsoft Docs
description: "Obtenez des réponses aux questions fréquemment posées sur les groupes de machines virtuelles identiques"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/08/2017
ms.author: negat
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8d0adfb995ecfb56ff33814e462a88f3d1102d58
ms.lasthandoff: 03/15/2017


---

# <a name="azure-virtual-machine-scale-sets-faq"></a>FAQ sur les groupes de machines virtuelles identiques Azure

Cet article contient des réponses aux questions fréquemment posées sur les groupes identiques.

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quelles sont les meilleures pratiques pour la mise à l’échelle automatique d’Azure ?

Oui. Consultez https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices

### <a name="where-do-i-find-the-metric-names-for-autoscaling-using-host-based-metrics"></a>Où trouver les noms des mesures pour la mise à l’échelle automatique à l’aide de mesures basées sur les hôtes ?

https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/

### <a name="are-there-any-examples-of-autoscaling-based-on-a-service-bus-topic-and-queue-length"></a>Existe-t-il des exemples de mise à l’échelle automatique basée sur une rubrique Service Bus et une longueur de file d’attente ?

Oui. Consultez l'article :

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/

Pour une file d’attente Service Bus :

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Pour les files d’attente de stockage :

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Remplacez les valeurs de l’exemple par les URI de la ressource appropriée.


### <a name="should-we-autoscale-with-host-based-metrics-or-use-a-diagnostics-extension"></a>Devons-nous utiliser la mise à l’échelle automatique avec des mesures basées sur les hôtes ou utiliser une extension de diagnostics ?

Vous pouvez créer un paramètre de mise à l’échelle automatique sur une machine virtuelle pour utiliser les mesures au niveau de l’hôte, ou utiliser les mesures basées sur le système d’exploitation invité.

Consultez la liste des mesures prises en charge : https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics. Voici un exemple complet de groupes identiques (dans ce cas, nous avons utilisé la mesure du processeur au niveau de l’hôte et une mesure de comptage de messages) :

https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets

### <a name="how-can-i-set-alert-rules-on-a-scale-set"></a>Comment puis-je définir des règles d’alerte sur un groupe identique ?

Vous pouvez créer des alertes sur des mesures pour des groupes identiques via PS ou l’interface CLI. Consultez l'article :

https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts

La valeur TargetResourceId du groupe identique ressemble à : /subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Vous pouvez choisir n’importe quel compteur de performances de machine virtuelle en tant que mesure pour l’alerte :

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os

### <a name="how-can-i-set-up-autoscale-on-a-scale-set-using-powershell"></a>Comment puis-je installer la mise à l’échelle automatique sur un groupe identique à l’aide de PowerShell ?

Consultez https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/




## <a name="certificates"></a>Certificats

### <a name="how-do-you-securely-ship-a-certificate-into-the-vm--is-there-an-example-of-provisioning-a-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration--the-common-certificate-rotation-operation-would-amount-to-a-configuration-update-operation"></a>Comment expédier en toute sécurité un certificat sur la machine virtuelle ?  Existe-t-il un exemple de configuration d’un groupe identique pour exécuter un site Web où le protocole SSL pour le site Web est livré en toute sécurité à partir d’une configuration de certificat ?  L’opération de rotation de certificat courante correspondrait à une opération de mise à jour de la configuration.

Nous prenons en charge l’installation de certificats clients directement dans le magasin de certificats Windows à partir des coffres de clés.

Dans le contexte des groupes identiques...

https://msdn.microsoft.com/library/mt589035.aspx

```json
        "secrets": [ {
              "sourceVault": {
                      "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
          },
          "vaultCertificates": [ {
                      "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                  "certificateStore": "certificateStoreName"
          } ]
        } ]
```

Prend en charge les machines virtuelles Linux et Windows.

### <a name="self-signed-certificate-example"></a>Exemple de certificat auto-signé :

#### <a name="create-a-self-signed-cert-in-a-keyvault"></a>Créer un certificat auto-signé dans un coffre de clés

Pour créer un certificat auto-signé dans un coffre de clés, vous pouvez utiliser les instructions de cet article Service Fabric : https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/

Les commandes PowerShell :

```powershell
Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
```

La commande précédente vous donne l’entrée du modèle Resource Manager.

#### <a name="change-resource-manager-template"></a>Modifier le modèle Resource Manager

Ajoutez cette propriété à « virtualMachineProfile » comme composant de la ressource du groupe identique :

```json 
"osProfile": {
            "computerNamePrefix": "[variables('namingInfix')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                  {
                    "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                    "certificateStore": "My"
                  }
                ]
              }
            ]
          }
```
 

### <a name="is-there-a-way-to-specify-an-ssh-key-pair-that-i-want-to-use-for-ssh-authentication-with-a-linux-scale-set-from-a-resource-manager-template"></a>Existe-t-il un moyen de spécifier une paire de clés SSH à utiliser pour l’authentification SSH avec un groupe identique Linux à partir d’un modèle Resource Manager ?  

L’API REST pour osProfile ressemble au cas de machine virtuelle ordinaire :
 
https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration
 
Incluez une valeur `osProfile` dans votre modèle comme dans l’exemple suivant :

```json 
"osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUserName')]",
          "linuxConfiguration": {
            "disablePasswordAuthentication": "true",
            "ssh": {
              "publicKeys": [
                {
                  "path": "[variables('sshKeyPath')]",
                  "keyData": "[parameters('sshKeyData')]"
                }
              ]
            }
          }
        }
```
 
Ce bloc JSON est utilisé dans le modèle de démarrage rapide suivant :
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
Examinez également le profil de système d’exploitation sur ce modèle :
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json

### <a name="how-do-i-remove-deprecated-certificates"></a>Comment supprimer des certificats obsolètes ? 

Vous devez supprimer l’ancien certificat dans la liste des certificats du coffre, mais laissez tous les certificats que vous souhaitez conserver sur votre machine. Cela ne supprime pas le certificat sur toutes vos machines virtuelles, mais cela n’ajoute pas non plus le certificat aux nouvelles machines virtuelles créées dans le groupe identique. Pour supprimer le certificat sur des machines virtuelles existantes, vous devez écrire une extension de script personnalisée qui supprime les certificats de votre magasin de certificats manuellement.
 
### <a name="how-do-i-take-an-existing-ssh-public-key-and-inject-it-into-the-scale-set-ssh-layer-during-provisioning--i-would-like-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-utilize-them-in-my-resource-manager-template"></a>Comment prendre une clé publique SSH existante et l’injecter dans la couche SSH du groupe identique lors de la configuration ?  J’aimerais stocker les valeurs de la clé publique SSH dans Azure Key Vault et les utiliser ensuite dans mon modèle Resource Manager.

Si vous configurez les machines virtuelles seulement avec une clé SSH publique, il est inutile de placer les clés publiques dans le coffre de clés, car les clés publiques ne sont pas secrètes.
 
Vous pouvez fournir les clés publiques SSH en texte brut lorsque vous créez une machine virtuelle Linux.
Vous trouverez un exemple ici :

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
Plus précisément :

```json
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          }
```
 
Nom d’élément linuxConfiguration | Requis | Type | Description
--- | --- | --- | --- |  ---
ssh | Non | Collection | Spécifie la configuration de la clé SSH pour un système d’exploitation Linux.
path | Oui | String | Spécifie le chemin d’accès du fichier Linux où les clés SSH ou le certificat doivent être placés.
keyData | Oui | String | Spécifie une clé SSH publique encodée en base64.
 
### <a name="when-i-run-update-azurermvmss-after-more-than-one-certificate-from-the-same-keyvault-i-get-the-following-error"></a>Lorsque j’exécute Update-AzureRmVmss sur plusieurs certificats du même coffre de clés, j’obtiens l’erreur suivante :
 
Update-AzureRmVmss : List secrets (Afficher la liste des secrets) contient des instances répétées de /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, ce qui n’est pas autorisé. Pourquoi ne puis-je pas ajouter deux certificats à partir du même coffre de clés ?
 
Ce comportement peut se produire si vous tentez d’ajouter le même coffre deux fois au lieu d’un nouveau vaultCertificate pour la valeur sourceVault existante. Add-AzureRmVmssSecret ne fonctionne pas correctement pour l’ajout d’autres secrets.
 
Si vous souhaitez ajouter plus de secrets à partir du même coffre de clés, vous devez mettre à jour la liste $vmss.properties.osProfile.secrets[0].vaultCertificates
 
Vous pouvez voir la structure d’entrée attendue ici : https://msdn.microsoft.com/library/azure/mt589035.aspx
 
Vous devez rechercher la clé secrète dans l’objet de groupe identique qui possède le même coffre de clés contenant. Ensuite, vous devez ajouter votre référence de certificat (l’URL, ainsi que le nom du magasin secret) dans la liste associée au coffre.

Remarque : la suppression de certificats sur des machines virtuelles via les API de groupe identique n’est actuellement pas prise en charge.
 
L’ancien certificat n’apparaîtra pas sur les nouvelles machines virtuelles, mais celles sur lesquelles le certificat était déjà déployé auront toujours l’ancien certificat.
 
### <a name="is-there-a-way-to-get-certificates-pushed-to-the-scale-set-without-providing-the-password-when-the-certificate-is-in-secretstore-currently"></a>Existe-t-il un moyen de transmettre des certificats au groupe identique sans fournir le mot de passe lorsque le certificat est actuellement dans SecretStore ?

Il est inutile de coder en dur les mots de passe dans les scripts. Vous pouvez les récupérer de manière dynamique avec les autorisations que le script de déploiement dont vous disposez utilise pour s’exécuter. Si vous avez un script qui déplace un certificat à partir du magasin des secrets vers le coffre de clés, la commande « get certificate » du magasin des secrets génère également le mot de passe du fichier pfx.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-of-a-scale-set-work-why-do-you-need-sourcevault-when-you-have-to-specify-the-absolute-uri-to-a-certificate-with-certificateurl"></a>Comment fonctionne la propriété des secrets de virtualMachineProfile.osProfile d’un groupe identique ? Pourquoi est-il nécessaire d’avoir un sourceVault lorsqu’il faut spécifier l’URI absolu d’un certificat avec certificateUrl ? 

Une référence de certificat Win RM doit être présente dans la propriété des secrets du profil du système d’exploitation. 

L’objectif consistant à indiquer le coffre source est d’être en mesure d’appliquer des stratégies ACL qui existent dans CSM. Si le coffre source n’est pas spécifié, les utilisateurs ne disposant pas des autorisations nécessaires pour déployer/accéder aux secrets d’un coffre de clés peuvent y parvenir via CRP. Les listes ACL existent même pour les ressources qui n’existent pas.

Si vous avez fourni un ID sourceVault incorrect mais une URL de coffre de clés valide, nous signalons une erreur lorsque vous interrogez l’opération
 
### <a name="if-i-add-secrets-to-an-existing-scale-set-does-it-inject-them-in-existing-instances-or-only-new-ones"></a>Si j’ajoute des clés secrètes à un groupe identique existant, sont-elles injectées par celui-ci dans les instances existantes ou uniquement dans les nouvelles instances ? 

Les certificats sont ajoutés à toutes les machines virtuelles, mêmes les préexistantes. Si la propriété upgradePolicy de votre groupe identique est définie sur « Manuel », le certificat est ajouté à la machine virtuelle lorsque vous effectuez une mise à jour manuelle sur la machine virtuelle.
 
### <a name="where-do-certificates-go-for-linux-vms"></a>Où vont les certificats pour les machines virtuelles Linux ?

Consultez https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/
  
### <a name="how-do-you-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Comment ajouter un nouveau certificat de coffre à un nouvel objet de certificat ?

Si vous souhaitez ajouter un certificat de coffre à une clé secrète existante, qui doit être le seul objet de clé secrète, vous pouvez procéder comme dans l’exemple PowerShell suivant :
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Que se passe-t-il pour les certificats en cas de réinitialisation d’une machine virtuelle ?

Si vous réinitialisez une machine virtuelle, les certificats disparaissent, car cette opération supprime le disque du système d’exploitation dans son intégralité. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Que se passe-t-il si vous supprimez un certificat dans le coffre de clés ?

Si la clé secrète est supprimée dans le coffre de clés et si vous cessez de libérer toutes vos machines virtuelles, puis les démarrez à nouveau, vous rencontrerez une erreur. Cet échec est dû au fait que CRP a besoin de récupérer les clés secrètes dans le coffre de clés, mais cela n’est pas possible. Dans ce scénario, vous pouvez supprimer les certificats à partir du modèle de groupe identique. 

Le composant CRP ne conserve pas les clés secrètes du client. Si vous arrêtez de libérer toutes les machines virtuelles dans le groupe identique, alors le cache est supprimé. Dans ce scénario, les clés secrètes sont récupérées à partir du coffre de clés.

Ce problème ne se produit pas lors de la montée en charge, car il existe une copie en cache de la clé secrète dans la structure (au moins dans le modèle client de structure unique).
 
### <a name="why-do-we-have-to-specify-the-exact-location-for-the-certificate-url-as-referenced-here-per-httpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Pourquoi faut-il spécifier l’emplacement exact de l’URL du certificat, comme indiqué ici : https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/, 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
 
Selon la documentation KeyVault, l’API REST get-secret doit retourner la dernière version de la clé secrète si la version n’est pas spécifiée :
 
Méthode | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Remplacez {secret-name} par le nom et {secret-version} par la version de la clé secrète que vous souhaitez récupérer. La version de la clé secrète peut être exclue, auquel cas la version actuelle est récupérée.
  
### <a name="why-does-certificate-version-have-to-be-specified-when-using-key-vault"></a>Pourquoi la version du certificat doit-elle être spécifiée lors de l’utilisation du coffre de clés ?

La raison de cette exigence consiste à indiquer clairement à l’utilisateur quel certificat est déployé sur ses machines virtuelles.

Si vous créez une machine virtuelle, puis mettez à jour votre clé secrète dans le coffre de clés, ce nouveau certificat ne sera pas téléchargé sur vos machines virtuelles. Mais vos machines virtuelles auront l’air d’y faire référence et les nouvelles machines virtuelles obtiendront la nouvelle clé secrète. Pour éviter cette confusion, vous devez référencer une version de clé secrète explicite.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-is-for-deployment-of-these-certs-to-a-scale-set"></a>Mon équipe utilise plusieurs certificats qui nous sont distribués en tant que clés publiques .cer. Quelle est l’approche recommandée pour le déploiement de ces certificats sur un groupe identique ?

Vous pouvez générer un fichier pfx qui contient uniquement des fichiers .cer, avec X509ContentType = Pfx. Par exemple, chargez le fichier .cer en tant qu’objet x509Certificate2 dans C# ou PowerShell et en appelant cette méthode : https://msdn.microsoft.com/library/24ww6yzk(v=vs.110).aspx

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-that-most-other-resource-providers-provide"></a>Je ne vois pas d’option permettant aux utilisateurs de transmettre des certificats en tant que chaînes base64 que la plupart des autres fournisseurs de ressources proposent.

Vous pouvez extraire l’URL dernière version au sein d’un modèle Resource Manager pour émuler le comportement que vous décrivez. Vous pouvez inclure la propriété JSON suivante dans votre modèle Resource Manager :

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-we-have-to-wrap-certs-in-json-objects-in-keyvaults"></a>Devons-nous encapsuler les certificats dans des objets JSON dans les coffres de clés ?

Il s’agit d’une exigence pour le groupe identique/la machine virtuelle. Nous prenons également en charge le type de contenu application/x-pkcs12. Les instructions sont disponibles ici : http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/
 
Nous ne prenons actuellement pas en charge les fichiers .cer. Vous devez exporter vos fichiers .cer dans des conteneurs pfx.





## <a name="compliance"></a>Conformité

### <a name="are-scale-sets-pci-compliant"></a>Les groupes identiques sont-ils compatibles avec PCI ?

Les groupes identiques constituent une fine couche d’API située sur le fournisseur de ressources de calcul et l’ensemble fait partie de la zone « Plateforme de calcul » dans l’arborescence des services Azure.

Par conséquent, du point de vue de la conformité, les groupes identiques sont une composante essentielle de la plateforme de calcul Azure. Donc, ils partagent l’équipe, les outils, les processus, la méthodologie de déploiement, les contrôles de sécurité, JIT, la surveillance, l’alerte, etc. identiques à ceux du fournisseur de ressources de calcul (CRP) lui-même.  Les groupes identiques sont compatibles avec PCI, car le fournisseur de ressources de calcul fait partie de l’attestation PCI DSS actuelle :

Pour plus d’informations, consultez : [https://www.microsoft.com/TrustCenter/Compliance/PCI](https://www.microsoft.com/TrustCenter/Compliance/PCI).






## <a name="extensions"></a>Extensions

### <a name="how-do-you-delete-a-scale-set-extension"></a>Comment supprimer une extension de groupe identique ?

Voici un exemple à l’aide de PowerShell :

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
extensionName se trouve dans `$vmss`.
   
### <a name="is-there-a-scale-set-template-example-that-integrates-with-oms"></a>Existe-t-il un exemple de modèle de groupe identique qui s’intègre à OMS ?

Consultez le deuxième exemple ici :

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric
   
### <a name="extensions-seem-to-run-in-parallel-on-scale-sets-causing-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this-behavior"></a>Les extensions semblent s’exécuter en parallèle sur les groupes identiques, causant l’échec de mon extension de script personnalisé. Que puis-je faire pour résoudre ce problème ?

Consultez https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/ 
 
 
### <a name="how-do-i-reset-the-password-for-scale-set-vms"></a>Comment réinitialiser le mot de passe pour les machines virtuelles du groupe identique ?

Utiliser les extensions d’accès de machine virtuelle

Voici un exemple à l’aide de PowerShell :

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-scale-set"></a>Comment ajouter une extension pour toutes les machines virtuelles de mon groupe identique ?

- Si la stratégie de mise à jour est définie sur automatique, le redéploiement du modèle avec les nouvelles propriétés d’extension met à jour chaque machine virtuelle.
- Si la stratégie de mise à jour est définie sur manuelle, vous devez mettre à jour l’extension, puis effectuer l’opération manualUpdate sur toutes les instances.
  
### <a name="if-the-extensions-associated-with-an-existing-scale-set-are-updated-would-they-affect-already-existing-vms-that-is-would-the-vms-show-up-as-not-matching-the-scale-set-model-or-would-they-be-ignored-when-an-existing-machine-is-service-healed--reimaged--etc-would-the-scripts-that-are-currently-configured-on-the-scale-set-be-executed-or-would-the-ones-that-were-configured-when-the-machine-was-first-created-be-used"></a>Si les extensions associées à un groupe identique existant sont mises à jour, cela affecte-t-il les machines virtuelles existantes ? (Autrement dit, les machines virtuelles vont-elles s’afficher comme ne correspondant pas au modèle de groupe identique) ? Ou bien, sont-elles ignorées ? Lorsqu’une machine existante est corrigée par service, réinitialisée, etc. les scripts qui sont actuellement configurés sur le groupe identique sont-ils exécutés ou les scripts configurés lors de la création de la machine sont-ils utilisés ?

- Si la définition de l’extension dans le modèle de groupe identique est mise à jour, cela met à jour les machines virtuelles si upgradePolicy est défini sur automatique, et les machines virtuelles sont indiquées comme ne correspondant pas au modèle si upgradePolicy est défini sur manuel. 

- Si une machine virtuelle existante est corrigée par service, cela s’apparente à un redémarrage et les extensions ne sont pas exécutées à nouveau. Si elle est réinitialisée, cela s’apparente au remplacement du disque du système d’exploitation avec l’image source et les dernières spécialisations du modèle, telles que les extensions, s’exécutent.
 
### <a name="how-do-i-get-a-scale-set-to-join-an-ad-domain"></a>Comment joindre un groupe identique à un domaine Active Directory ?

Vous pouvez définir une extension similaire à celle-ci à l’aide de JsonADDomainExtension par exemple :
```json
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
                                    "settings": {
                                        "Name": "[parameters('domainName')]",
                                        "OUPath": "[variables('ouPath')]",
                                        "User": "[variables('domainAndUsername')]",
                                        "Restart": "true",
                                        "Options": "[variables('domainJoinOptions')]"
                                    },
                                    "protectedsettings": {
                                        "Password": "[parameters('domainJoinPassword')]"
                                    }
                                }
                            }
                        ]
                    }
```
 
### <a name="my-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-instance-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Mon extension de groupe identique tente d’installer quelque chose nécessitant un redémarrage, par exemple : « commandToExecute » : « powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools »

Vous pourriez utiliser l’extension DSC. Si le système d’exploitation est 2012 R2, alors Azure extrait le programme d’installation WMF5.0, redémarre et poursuit la configuration. 
 
### <a name="how-can-i-enable-antimalware-on-my-scale-set"></a>Comment puis-je activer un logiciel anti-programme malveillant sur mon groupe identique ?

Voici un exemple PowerShell :

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-hosted-on-a-private-storage-account-i-have-no-problems-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signaturesas-it-fails-with-the-error-missing-mandatory-parameters-for-valid-shared-access-signature-i-know-that-linksas-works-fine-from-my-local-browser"></a>J’ai besoin d’exécuter un script personnalisé hébergé sur un compte de stockage privé. Je n’ai aucun problème lorsque le stockage est public, mais lorsque j’essaie d’utiliser une signature d’accès partagé(SAP) cela échoue avec l’erreur : « Paramètres obligatoires manquants pour la signature d’accès partagé valide ». Je sais que ce lien + SAP fonctionnent correctement à partir de mon navigateur local.

Vous devez configurer les paramètres protégés avec le nom et la clé du compte de stockage pour que ce scénario fonctionne. Consultez https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings







## <a name="networking"></a>Mise en réseau
 
### <a name="how-do-i-do-vip-swap-for-scale-sets-in-the-same-subscription-and-same-region"></a>Comment effectuer un échange d’adresses IP virtuelles pour les groupes identiques dans le même abonnement et la même région ?

Consultez : https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/ 
 
  
### <a name="what-is-the-resourceguid-property-on-a-nic-for-its-not-documented-here"></a>Qu’est-ce que la propriété resourceGuid sur une carte d’interface réseau ? Ce sujet n’est pas traité ici.

Il s’agit d’un ID unique. Les couches inférieures se connecteront à l’aide de cet ID à un moment donné dans le futur. 
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-for-static-private-ip-address-allocation"></a>Comment spécifier une plage d’adresses IP privées, pour l’allocation d’adresse IP privée statique ?

Les adresses IP sont sélectionnés à partir d’un sous-réseau que vous spécifiez. 

La méthode d’allocation des adresses IP de groupe identique est toujours « Dynamique ». Cependant, cela ne signifie pas que ces adresses IP peuvent changer. Cela signifie simplement que vous ne spécifiez pas l’adresse IP dans la requête PUT. En d’autres termes, vous spécifiez l’ensemble statique à l’aide du sous-réseau. 
    
### <a name="how-do-i-deploy-a-scale-set-into-an-existing-vnet"></a>Comment déployer un groupe identique dans un réseau virtuel existant ? 

Consultez https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet 




## <a name="scale"></a>Mettre à l'échelle

### <a name="why-would-you-ever-create-a-scale-set-with-fewer-than-2-vms"></a>Pourquoi créer un groupe identique avec moins de 2 machines virtuelles ?

L’une des raisons est d’utiliser les propriétés élastiques d’un groupe identique. Par exemple, vous pouvez déployer un groupe identique avec zéro machine virtuelle afin de définir votre infrastructure sans payer les frais de fonctionnement de la machine virtuelle. Ensuite, lorsque vous êtes prêt à déployer des machines virtuelles, vous augmentez la « capacité » du groupe identique en fonction du nombre d’instances de production.

Une autre raison est lorsque vous utilisez votre groupe identique et que vous ne vous préoccupez pas de la disponibilité de la même façon que lors de l’utilisation d’un groupe à haute disponibilité avec des machines virtuelles discrètes. Les groupes identiques permettent d’utiliser des unités de calcul indifférenciées qui sont fongibles. Cette uniformité est un atout pour les groupes identiques par rapport aux groupes à haute disponibilité. De nombreuses charges de travail sans état ne s’intéressent pas aux unités individuelles et peuvent descendre en puissance à une unité de calcul si la charge de travail baisse, puis passer de nouveau à plusieurs unités lorsque la charge de travail augmente.

### <a name="how-do-you-change-the-number-of-vms-in-a-scale-set"></a>Comment modifier le nombre de machines virtuelles dans un groupe identique ?

Consultez : https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/

### <a name="how-can-you-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Comment définir des alertes personnalisées lorsque certains seuils sont atteints ?

Vous avez une certaine flexibilité en matière de gestion des alertes ; par exemple, vous pouvez définir des webhooks personnalisés comme dans cet exemple issu d’un modèle Resource Manager :
```json
   {
         "type": "Microsoft.Insights/autoscaleSettings",
           "apiVersion": "[variables('insightsApi')]",
                 "name": "autoscale",
                   "location": "[parameters('resourceLocation')]",
                     "dependsOn": [
                         "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
                 ],
                 "properties": {
                         "name": "autoscale",
                     "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                     "enabled": true,
                     "notifications": [{
                               "operation": "Scale",
                              "email": {
                                     "sendToSubscriptionAdministrator": true,
                                     "sendToSubscriptionCoAdministrators": true,
                                     "customEmails": [
                                        "youremail@address.com"
                                     ]},
                              "webhooks": [{
                                    "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                                    "properties": {
                                        "key1": "custommetric",
                                        "key2": "scalevmss"
                                    }
                                    }
                              ]}],
```

Dans cet exemple, une alerte est envoyée à Pagerduty lorsqu’un seuil est atteint.



## <a name="troubleshooting"></a>résolution des problèmes

### <a name="how-do-i-enable-boot-diagnostics"></a>Comment activer les diagnostics de démarrage ?

Créez un compte de stockage et placez ce bloc JSON dans la propriété virtualMachineProfile de votre groupe identique et mettez celui-ci à jour :
```json
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
```

Ensuite, lorsqu’une nouvelle machine virtuelle est créée, la propriété InstanceView de la machine virtuelle affiche les détails de la capture d’écran, etc. Exemple :
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```

 

## <a name="updates"></a>Mises à jour

### <a name="how-to-i-update-my-scale-set-to-a-new-image-and-manage-patching"></a>Comment mettre à jour mon groupe identique sur une nouvelle image et gérer la mise à jour corrective ?

Consultez : https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set

### <a name="can-you-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Est-il possible d’utiliser l’opération de réinitialisation pour réinitialiser une machine virtuelle sans modifier l’image ? (Autrement dit, réinitialiser une machine virtuelle sur les paramètres d’usine plutôt qu’une nouvelle image) ?

Oui. Consultez : https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set

Toutefois, si votre groupe identique référence une image de plateforme avec la version = « latest » (la plus récente), votre machine virtuelle peut se mettre à jour vers une image de système d’exploitation ultérieure lorsque vous appelez la réinitialisation.







## <a name="vm-properties"></a>Propriétés de machine virtuelle

### <a name="how-do-i-get-property-information-for-each-vm-without-having-to-make-multiple-calls-for-example-getting-the-fault-domain-for-each-vm-in-my-100-scale-set"></a>Comment obtenir des informations sur les propriétés de chaque machine virtuelle sans avoir à effectuer plusieurs appels ? Par exemple : obtenir le domaine par défaut pour chaque machine virtuelle de mon groupe identique 100 ?

Vous pouvez appeler ListVMInstanceViews en exécutant une API REST `GET` sur l’URI de ressource suivant :

`/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView`

### <a name="are-there-ways-to-pass-different-extension-arguments-to-different-vms-in-a-scale-set"></a>Est-il possible de transférer des arguments d’extension différents à des machines virtuelles différentes dans un groupe identique ?

Non, mais les extensions peuvent agir en fonction des propriétés uniques de la machine virtuelle où elles s’exécutent, comme le nom de la machine. En outre, les extensions peuvent interroger les métadonnées de l’instance sur http://169.254.169.254 pour obtenir plus d’informations.

### <a name="why-are-there-gaps-between-my-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Pourquoi y a-t-il des écarts entre les noms de machines virtuelles de mon groupe identique et les ID des machines virtuelles ? Par exemple : 0, 1, 3...

Il existe des écarts parce que la propriété overprovision de votre groupe identique est définie sur la valeur par défaut true. Lorsque la propriété overprovision est définie sur true, le nombre de machines virtuelles créées est supérieur à ce qui est demandé, et les machines virtuelles supplémentaires sont supprimées par la suite. Vous obtenez une fiabilité de déploiement accrue aux dépens d’une affectation de noms contigus et de règles NAT contiguës. Vous pouvez définir cette propriété sur false. Pour les petits groupes identiques, cela ne fait pas beaucoup de différence en matière de fiabilité du déploiement.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-scale-set-vs-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Quelle est la différence entre la suppression d’une machine virtuelle dans un groupe identique et la libération de la machine virtuelle ? Quand choisir une option par rapport à l’autre ?

La principale différence est que la libération ne supprime pas les disques durs virtuels, donc des coûts de stockage sont associés à l’arrêt de la libération. Les raisons de choisir une option plutôt que l’autre incluent :

- Vous souhaitez arrêter de payer Compute mais conserver l’état des disques des machines virtuelles.
- Vous souhaitez démarrer un ensemble de machines virtuelles plus rapidement que la montée en charge d’un groupe identique.
  - En relation avec ce scénario, vous avez créé votre propre moteur de mise à l’échelle automatique et souhaitez obtenir une mise à l’échelle de bout en bout plus rapide.
  - Vous avez un groupe identique qui est distribué inégalement entre les FD/UD (en raison de la suppression sélective des machines virtuelles ou en raison de la suppression de machines virtuelles après le sur-approvisionnement). Pour répartir équitablement les machines virtuelles entre les FD/UD, arrêter la libération, puis démarrer sur le groupe identique.








 
   

