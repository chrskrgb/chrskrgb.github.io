---
title: Scripts PowerShell - Administration & Reporting
description: Scripts PowerShell modulaires pour Microsoft Graph, Active Directory et rapports d'audit
---

# Scripts PowerShell pour l'Administration

Une sélection de scripts réutilisables pour auditer et gérer les environnements Microsoft.

---

## 1. Audit des comptes inactifs sous Microsoft Entra ID

Ce script interroge l'API Microsoft Graph pour extraire la liste des utilisateurs ne s'étant pas connectés depuis plus de 90 jours.

```powershell title="Audit-InactiveUsers.ps1"
[CmdletBinding()]
param (
    [int]$InactiveDays = 90,
    [string]$ExportPath = "./inactive_users_report.csv"
)

try {
    # Vérification de la connexion active
    $Context = Get-MgContext
    if (-not $Context) {
        Write-Warning "Aucune session active. Connexion à Microsoft Graph..."
        Connect-MgGraph -Scopes "User.Read.All", "AuditLog.Read.All"
    }

    $CutoffDate = (Get-Date).AddDays(-$InactiveDays).ToString("yyyy-MM-ddTHH:mm:ssZ")
    Write-Host "Recherche des comptes inactifs depuis le $CutoffDate..." -ForegroundColor Cyan

    $Filter = "signInActivity/lastSignInDateTime le $CutoffDate and accountEnabled eq true"
    $Users = Get-MgUser -Filter $Filter -Property "DisplayName,UserPrincipalName,SignInActivity,AccountEnabled" -All

    $Report = foreach ($User in $Users) {
        [PSCustomObject]@{
            NomComplet     = $User.DisplayName
            UPN            = $User.UserPrincipalName
            DernierSignIn  = $User.SignInActivity.LastSignInDateTime
            Actif          = $User.AccountEnabled
        }
    }

    $Report | Export-Csv -Path $ExportPath -NoTypeInformation -Encoding utf8
    Write-Host "Rapport exporté avec succès : $ExportPath ($($Report.Count) utilisateurs)" -ForegroundColor Green
}
catch {
    Write-Error "Erreur lors de l'exécution : $_"
}
```
