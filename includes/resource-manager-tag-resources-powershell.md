Les exemples de cet article nécessitent la version 3.0 ou ultérieure d’Azure PowerShell. Si vous n’avez pas la version 3.0, vous devez [mettre à jour votre version](/powershell/azureps-cmdlets-docs/) à l’aide de PowerShell Gallery ou de Web Platform Installer.

Pour afficher les balises existantes pour un *groupe de ressources*, utilisez :

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Le script retourne les informations au format suivant :

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Pour afficher les balises existantes pour une *ressource dont l’ID de ressource est spécifié*, utilisez :

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Alternativement, pour afficher les balises existantes pour une *ressource dont le nom et le groupe de ressources sont spécifiés*, utilisez :

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Pour obtenir *les groupes de ressources contenant une balise spécifique*, utilisez :

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

Pour obtenir *les ressources contenant une balise spécifique*, utilisez :

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Chaque fois que vous appliquez des balises à une ressource ou un groupe de ressources, vous remplacez les balises existantes de cette ressource ou de ce groupe de ressources. Par conséquent, vous devez utiliser une approche différente selon que la ressource ou le groupe de ressources a des balises existantes.

Pour ajouter des balises à un *groupe de ressources ne contenant pas de balises existantes*, utilisez :

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Pour ajouter des balises à un *groupe de ressources avec des balises existantes*, récupérez les balises existantes, ajoutez la nouvelle balise et réappliquez les balises :

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Pour ajouter des balises à une *ressource ne contenant pas de balises existantes*, utilisez :

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Pour ajouter des balises à une *ressource avec des balises existantes*, utilisez :

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Pour appliquer toutes les balises d’un groupe de ressources à ses ressources *sans conserver les balises existantes*, utilisez le script suivant :

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Pour appliquer toutes les balises d’un groupe de ressources à ses ressources et *conserver les balises existantes sur les ressources qui ne sont pas des doublons*, utilisez le script suivant :

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Pour supprimer toutes les balises, utilisez une table de hachage vide :

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```
