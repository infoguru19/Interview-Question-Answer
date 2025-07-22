### Write python script to fetch all public storage account from azure.
   Here's a Python script that uses the Azure SDK `(azure-mgmt-storage)` to fetch all public Azure Storage Accounts (i.e., storage accounts with public network access enabled) across your Azure subscription(s).
   
   **Requirements**
 - Azure CLI authenticated (az login)
 - azure-identity, azure-mgmt-storage libraries installed:
 - `pip install azure-identity azure-mgmt-storage`
   
**fetch_public_storage_accounts.py**
```
from azure.identity import AzureCliCredential
from azure.mgmt.storage import StorageManagementClient
from azure.mgmt.resource.subscriptions import SubscriptionClient

def get_public_storage_accounts():
    # Authenticate using Azure CLI
    credential = AzureCliCredential()

    # Get all subscriptions
    sub_client = SubscriptionClient(credential)
    subscriptions = list(sub_client.subscriptions.list())

    for sub in subscriptions:
        print(f"\n🔎 Subscription: {sub.display_name} ({sub.subscription_id})")
        storage_client = StorageManagementClient(credential, sub.subscription_id)

        # List all storage accounts in the subscription
        for account in storage_client.storage_accounts.list():
            account_details = storage_client.storage_accounts.get_properties(
                resource_group_name=account.id.split("/")[4],
                account_name=account.name
            )

            # Check if public access is allowed
            if account_details.network_rule_set and account_details.network_rule_set.default_action == "Allow":
                print(f"✅ Public Storage Account: {account.name}")
                print(f"   Location: {account.location}")
                print(f"   Resource Group: {account.id.split('/')[4]}")
                print(f"   Kind: {account.kind}")
                print(f"   Sku: {account.sku.name}\n")

if __name__ == "__main__":
    get_public_storage_accounts()
```

