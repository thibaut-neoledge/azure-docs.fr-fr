### <a name="tag-cmdlet-changes-in-latest-powershell-version"></a>Modifications d’applet de commande de balise dans la dernière version de PowerShell
La version d’août 2016 d’Azure PowerShell 2.0 inclut des modifications importantes pour l’utilisation des balises. Avant de continuer, vérifiez la version de votre module AzureRm.Resources.

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Si vous avez mis à jour Azure PowerShell pour la dernière fois avant août 2016, la version indiquée dans les résultats doit être inférieure à 3.0.

```powershell
Version
-------
2.0.2
```

Si vous avez mis à jour Azure PowerShell après août 2016, les résultats doivent indiquer la version 3.0.

```powershell
Version
-------
3.0.1
```

Si votre version du module est 3.0.1 ou ultérieur, vous disposez des applets de commande les plus récentes pour utiliser des balises. Cette version du module de ressources Azure s’installe automatiquement lorsque vous installez ou mettez à niveau Azure PowerShell à l’aide de PowerShell Gallery, PowerShellGet ou Web Platform Installer.  Si votre version est antérieure à 3.0.1, vous pouvez continuer à utiliser cette version, mais vous pouvez envisager une mise à jour vers la version la plus récente. La dernière version inclut des modifications qui facilitent l’utilisation des balises. Les deux approches sont présentées dans cette rubrique.

### <a name="updating-your-script-for-changes-in-latest-version"></a>Mise à jour de votre script pour prendre en compte les modifications apportées dans la version la plus récente
Dans la dernière version, le nom de paramètre **Tags** est devenu **Tag**, tandis que le nom de type `Hashtable[]` est devenu `Hashtable`. Vous n’avez plus besoin de fournir **Name** et **Value** pour chaque entrée. Au lieu de cela, vous indiquez des paires clé-valeur selon le format **Clé = "Valeur"**.

Pour mettre à jour un script existant, remplacez le paramètre **Tags** par **Tag**, puis modifiez le format de balise, comme illustré dans l’exemple suivant :

```powershell
# Old
New-AzureRmResourceGroup -Tags @{ Name = "testtag"; Value = "testval" } -Name $resourceGroupName -Location $location

# New
New-AzureRmResourceGroup -Tag @{ testtag = "testval" } -Name $resourceGroupName -Location $location 
```

Notez cependant que les groupes de ressources et les ressources retournent toujours une propriété **Tags** dans leurs métadonnées. Cette propriété n’est pas modifiée.

### <a name="version-301-or-later"></a>Version 3.0.1 ou ultérieure
Les balises se trouvent directement dans les ressources et les groupes de ressources. Pour visualiser les balises existantes, affichez une ressource avec **Get-AzureRmResource** ou un groupe de ressources avec **Get-AzureRmResourceGroup**. 

Commençons par un groupe de ressources.

```powershell
Get-AzureRmResourceGroup -Name testrg1
```

Cette cmdlet renvoie plusieurs octets de métadonnées sur le groupe de ressources, notamment les balises déjà appliquées, le cas échéant.

```powershell
ResourceGroupName : testrg1
Location          : westus
ProvisioningState : Succeeded
Tags              :
                Name         Value
                ===========  ==========
                Dept         Finance
                Environment  Production
```

Pour récupérer les métadonnées de ressources incluant des balises, utilisez l’exemple suivant :

```powershell
Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1
```

Les noms des balises apparaissent dans les résultats.

```powershell
Name              : tfsqlserver
ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
ResourceName      : tfsqlserver
ResourceType      : Microsoft.Sql/servers
Kind              : v12.0
ResourceGroupName : testrg1
Location          : westus
SubscriptionId    : {guid}
Tags              : {Dept, Environment}
```

Utilisez la propriété **Tags** pour obtenir les noms et les valeurs des balises.

```powershell
(Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1).Tags
```

Cette dernière renvoie les résultats suivants :

```powershell
Name                   Value
----                   -----
Dept                   Finance
Environment            Production
```

Plutôt que d’afficher les balises d’une ressource ou d’un groupe de ressources en particulier, vous souhaitez sans doute récupérer toutes les ressources ou tous les groupes de ressources qui ont une valeur et une balise spécifiques. Pour récupérer des groupes de ressources avec une balise spécifique, utilisez l’applet de commande **Find-AzureRmResourceGroup** avec le paramètre **-Tag**.

Pour récupérer des groupes de ressources comportant une valeur de balise, utilisez le format suivant :

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Pour obtenir toutes les ressources contenant une balise et une valeur spécifiques, utilisez l’applet de commande **Find-AzureRmResource** .

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Pour ajouter une balise à un groupe de ressources qui n’en comporte pas, utilisez la commande **Set-AzureRmResourceGroup** , puis spécifiez l’objet de la balise.

```powershell
Set-AzureRmResourceGroup -Name test-group -Tag @{ Dept="IT"; Environment="Test" }
```

Ce qui renvoie le groupe de ressources avec ses nouvelles valeurs de balise.

```powershell
ResourceGroupName : test-group
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
                Name          Value
                =======       =====
                Dept          IT
                Environment   Test
```

Vous pouvez ajouter des balises à une ressource qui n’en comporte pas à l’aide de la commande **Set-AzureRmResource** 

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp
```

Les balises sont mises à jour en tant qu'ensemble. Pour ajouter une balise à une ressource comportant d’autres balises, utilisez un tableau avec toutes les balises que vous souhaitez conserver. Sélectionnez d'abord les balises existantes, ajoutez une balise à cet ensemble puis réappliquez toutes les balises.

```powershell
$tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
$tags += @{Status="approved"}
Set-AzureRmResourceGroup -Name test-group -Tag $tags
```

Pour supprimer une ou plusieurs balises, enregistrez le tableau sans les balises que vous souhaitez supprimer.

Le processus est le même pour les ressources, à l’exception du fait que vous utilisez les applets de commande **Get-AzureRmResource** et **Set-AzureRmResource**. 

Pour obtenir une liste de toutes les balises d’un abonnement à l’aide de PowerShell, utilisez l’applet de commande **Get-AzureRmTag** .

```powershell
Get-AzureRmTag
```

Cette dernière retourne des noms de balise et le nombre de ressources et de groupes de ressources avec la balise.

```powershell
Name                      Count
----                      ------
Dept                       8
Environment                8
```

Vous pouvez consulter les balises commençant par « masqué-» et « lien: ». Il s'agit de balises internes, que vous devez ignorer et éviter de modifier.

Pour ajouter des balises à la taxonomie, utilisez l’applet de commande **New-AzureRmTag** . Ces balises seront incluses dans la saisie semi-automatique, même si elles n'ont pas encore été appliquées à des ressources ou des groupes de ressources. Pour supprimer un nom ou une valeur de balise, commencez par supprimer la balise sur toutes les ressources où elle est appliquée, puis utilisez l’applet de commande **Remove-AzureRmTag** pour la supprimer de la taxonomie.

### <a name="versions-earlier-than-301"></a>Versions antérieures à 3.0.1
Les balises se trouvent directement dans les ressources et les groupes de ressources. Pour visualiser les balises existantes, affichez une ressource avec **Get-AzureRmResource** ou un groupe de ressources avec **Get-AzureRmResourceGroup**. 

Commençons par un groupe de ressources.

```powershell
Get-AzureRmResourceGroup -Name testrg1
```

Cette cmdlet renvoie plusieurs octets de métadonnées sur le groupe de ressources, notamment les balises déjà appliquées, le cas échéant.

```powershell
ResourceGroupName : testrg1
Location          : westus
ProvisioningState : Succeeded
Tags              :
                Name         Value
                ===========  ==========
                Dept         Finance
                Environment  Production
```

Pour récupérer les métadonnées de ressources, utilisez l’exemple suivant. Les métadonnées de ressources n’affichent pas directement les balises. 

```powershell
Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1
```

Les résultats indiquent que les balises s’affichent uniquement en tant qu’objet de table de hachage.

```powershell
Name              : tfsqlserver
ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
ResourceName      : tfsqlserver
ResourceType      : Microsoft.Sql/servers
Kind              : v12.0
ResourceGroupName : tag-demo-group
Location          : westus
SubscriptionId    : {guid}
Tags              : {System.Collections.Hashtable}
```

Vous pouvez afficher les balises réelles en récupérant la propriété **Tags** .

```powershell
(Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
```

Ce qui renvoie les résultats mis en forme :

```powershell
Dept: Finance
Environment: Production
```

Plutôt que d’afficher les balises d’une ressource ou d’un groupe de ressources en particulier, vous souhaitez sans doute récupérer toutes les ressources ou tous les groupes de ressources qui ont une valeur et une balise spécifiques. Pour récupérer des groupes de ressources avec une balise spécifique, utilisez l’applet de commande **Find-AzureRmResourceGroup** avec le paramètre **-Tag**.

Pour récupérer des groupes de ressources comportant une valeur de balise, utilisez le format suivant :

```powershell
Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
```

Pour obtenir toutes les ressources contenant une balise et une valeur spécifiques, utilisez l’applet de commande Find-AzureRmResource.

```powershell
Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
```

Pour ajouter une balise à un groupe de ressources qui n’en comporte pas, utilisez la commande Set-AzureRmResourceGroup, puis spécifiez un objet de balise.

```powershell
Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )
```

Ce qui renvoie le groupe de ressources avec ses nouvelles valeurs de balise.

```powershell
ResourceGroupName : test-group
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
            Name          Value
            =======       =====
            Dept          IT
            Environment   Test
```

Vous pouvez ajouter des balises à une ressource qui n’en comporte pas à l’aide de la commande Set-AzureRmResource.

```powershell
Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp
```

Les balises sont mises à jour en tant qu'ensemble. Pour ajouter une balise à une ressource comportant d’autres balises, utilisez un tableau avec toutes les balises que vous souhaitez conserver. Sélectionnez d'abord les balises existantes, ajoutez une balise à cet ensemble puis réappliquez toutes les balises.

```powershell
$tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
$tags += @{Name="status";Value="approved"}
Set-AzureRmResourceGroup -Name test-group -Tag $tags
```

Pour supprimer une ou plusieurs balises, enregistrez le tableau sans les balises que vous souhaitez supprimer.

Le processus est le même pour les ressources, sauf que vous utilisez les applets de commande Get-AzureRmResource et Set-AzureRmResource. 

Pour obtenir une liste de toutes les balises d’un abonnement à l’aide de PowerShell, utilisez l’applet de commande **Get-AzureRmTag** .

```powershell
Get-AzureRmTag
```

Cette dernière retourne des noms de balise et le nombre de ressources et de groupes de ressources avec la balise.

```powershell
Name                      Count
----                      ------
Dept                       8
Environment                8
```

Vous pouvez consulter les balises commençant par « masqué-» et « lien: ». Il s'agit de balises internes, que vous devez ignorer et éviter de modifier.

Pour ajouter des balises à la taxonomie, utilisez l’applet de commande **New-AzureRmTag** . Ces balises seront incluses dans la saisie semi-automatique, même si elles n'ont pas encore été appliquées à des ressources ou des groupes de ressources. Pour supprimer un nom ou une valeur de balise, commencez par supprimer la balise sur toutes les ressources où elle est appliquée, puis utilisez l’applet de commande **Remove-AzureRmTag** pour la supprimer de la taxonomie.



<!--HONumber=Jan17_HO2-->


