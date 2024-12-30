Certainly! Below is an updated PowerShell script that loops through all Azure subscriptions associated with your account, retrieves all Role-Based Access Control (RBAC) assignments, and exports the data with user principal names to a CSV file.

Install the Azure PowerShell module (if not already installed):

Install-Module -Name Az -AllowClobber -Scope CurrentUser
Authenticate to Azure:

Connect-AzAccount
Script to download RBAC assignments for all subscriptions:

PowerShell
# Authenticate to Azure
Connect-AzAccount

# Get all subscriptions
$subscriptions = Get-AzSubscription

# Initialize an array to store the RBAC assignments
$rbacAssignments = @()

foreach ($subscription in $subscriptions) {
    # Set the subscription context
    Set-AzContext -SubscriptionId $subscription.Id

    # Get all RBAC assignments for the subscription
    $assignments = Get-AzRoleAssignment

    foreach ($assignment in $assignments) {
        # Get user principal name if the principal type is User
        if ($assignment.PrincipalType -eq 'User') {
            $user = Get-AzADUser -ObjectId $assignment.PrincipalId
            $userPrincipalName = $user.UserPrincipalName
        } else {
            $userPrincipalName = ""
        }

        # Add the assignment to the array with user principal name
        $rbacAssignments += [pscustomobject]@{
            SubscriptionId   = $subscription.Id
            RoleDefinitionName = $assignment.RoleDefinitionName
            PrincipalName    = $assignment.DisplayName
            PrincipalType    = $assignment.PrincipalType
            UserPrincipalName = $userPrincipalName
            Scope            = $assignment.Scope
        }
    }
}

# Export the RBAC assignments to a CSV file
$rbacAssignments | Export-Csv -Path "RBAC_Assignments.csv" -NoTypeInformation

Write-Host "RBAC assignments have been exported to RBAC_Assignments.csv"
Explanation:
Authenticate to Azure: The script starts by authenticating to Azure using Connect-AzAccount.

Get All Subscriptions: It retrieves all subscriptions associated with your account using Get-AzSubscription.

Set Subscription Context: For each subscription, the script sets the context to that subscription using Set-AzContext.

Get Role Assignments: It retrieves all RBAC assignments for the subscription using Get-AzRoleAssignment.

User Principal Name: If the principal type is a user, it fetches the user principal name using Get-AzADUser.

Store Assignments: The script stores the RBAC assignments, including the user principal name, in an array.

Export to CSV: Finally, it exports the collected RBAC assignments to a CSV file named RBAC_Assignments.csv.

This script ensures that all subscriptions associated with your account are processed, and RBAC assignments are exported with user principal names.

