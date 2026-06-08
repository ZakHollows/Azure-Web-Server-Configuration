# Azure-Web-Server-Configuration
Creating and configuring a web Server using Azure CLI and Bash commands to install Ngnix and change firewall settings

Using the following commands created a VM Web Server with Azure:
* az vm create --resource-group "IntroAzureRG" --name my-vm --size Standard_D2s_v5 --public-ip-sku Standard --image Ubuntu2204 --admin-username azureuser --generate-ssh-keys

The next change creates a pathway to install Ngnix on the VM:
* az vm extension set --resource-group "IntroAzureRG" --vm-name my-vm --name customScript --publisher Microsoft.Azure.Extensions --version 2.1 --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}' --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'


To verify functionality and available access the following command was used to see the IP address:
* az vm list-ip-addresses

Atempts to access the web server using its IP address failed with the resulting image

Suspected networking issue with security and firewall, the following command was used to see the current NSG's that are associated with the VM. Followed by the next command to see the current set rules:
* az network nsg list --resource-group "IntroAzureRG" --query '[].name' --output tsv
* az network nsg rule list --resource-group "IntroAzureRG" --nsg-name my-vmNSG --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' --output table

Once able to see that port 22 was only allowing a SSH connection, the next command was run to add in a new rule and allow access over HTTP:
* az network nsg rule create --resource-group "IntroAzureRG" --nsg-name my-vmNSG --name allow-http --protocol tcp --priority 100 --destination-port-range 80 --access Allow

Running the command for the output table again showed the rule had been implemented, and allowed for access to the Web Server IP address.
