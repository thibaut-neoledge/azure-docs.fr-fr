La version 3.0 du module AzureRm.Resources inclus des modifications importantes par rapport à l’utilisation des balises. Avant de poursuivre, vérifiez votre version :

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Si vous avez la version 3.0 ou une version ultérieure, les exemples de cette rubrique fonctionnent avec votre environnement. Si vous n’avez pas la version 3.0, vous devez [mettre à jour votre version](/powershell/azureps-cmdlets-docs/) à l’aide de PowerShell Gallery ou de Web Platform Installer avant de consulter cette rubrique.

```powershell
Version
-------
3.5.0
```

Chaque fois que vous appliquez des balises à une ressource ou un groupe de ressources, vous remplacez les balises existantes de cette ressource ou de ce groupe de ressources. Par conséquent, vous devez utiliser une approche différente selon que la ressource ou le groupe de ressources a des balises existantes que vous souhaitez conserver. Pour ajouter des balises à :

* un groupe de ressources sans les balises existantes.

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* un groupe de ressources avec les balises existantes.

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* une ressource sans les balises existantes.

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* une ressource avec les balises existantes.

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

Pour appliquer toutes les balises d’un groupe de ressources aux ressources du groupe de ressources **sans conserver les balises existantes**, utilisez le script suivant :

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Pour supprimer toutes les balises, utilisez une table de hachage vide.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

Pour obtenir les groupes de ressources avec une balise spécifique, utilisez l’applet de commande `Find-AzureRmResourceGroup`.

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Pour obtenir toutes les ressources contenant une balise et une valeur spécifiques, utilisez l’applet de commande `Find-AzureRmResource`.

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```



<!--HONumber=Feb17_HO1-->


