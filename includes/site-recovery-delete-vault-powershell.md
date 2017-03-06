## <a name="delete-a-recovery-services-vault-powershell"></a>Supprimer un coffre Recovery Services (PowerShell)

1. Récupérer le coffre Recovery Services

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. Supprimer le coffre

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> Utilisez la commande ci-dessus avec la plus grande précaution, car, si vous supprimez par erreur un coffre, vous perdrez toutes les données. Il s’agit d’une action permanente et il n’est pas possible de l’annuler.  


