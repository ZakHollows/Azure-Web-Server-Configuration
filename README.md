# Azure-Web-Server-Configuration
Creating and configuring a web Server using Azure CLI and Bash commands to install Ngnix and change firewall settings

Using the following commands created a VM Web Server with Azure:
* az vm create --resource-group "IntroAzureRG" --name my-vm --size Standard_D2s_v5 --public-ip-sku Standard --image Ubuntu2204 --admin-username azureuser --generate-ssh-keys
<img width="1687" height="419" alt="No 1" src="https://github.com/user-attachments/assets/f9594a26-e00f-4c6d-a19e-3b8f9c2dd7e4" />

The next change creates a pathway to install Ngnix on the VM:
* az vm extension set --resource-group "IntroAzureRG" --vm-name my-vm --name customScript --publisher Microsoft.Azure.Extensions --version 2.1 --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}' --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'
<img width="1898" height="381" alt="No 2" src="https://github.com/user-attachments/assets/783d3731-4b4f-463d-b399-d43296710ce8" />

To verify functionality and available access the following command was used to see the IP address:
* az vm list-ip-addresses
<img width="1297" height="456" alt="No 3" src="https://github.com/user-attachments/assets/54d879b4-bce8-4c60-95f7-73571b6666a4" />

Atempts to access the web server using its IP address failed with the resulting image
<img width="830" height="670" alt="No 4" src="https://github.com/user-attachments/assets/9dc1630c-0674-4d3c-a8ea-666c9453219d" />

Suspected networking issue with security and firewall, the following command was used to see the current NSG's that are associated with the VM. Followed by the next command to see the current set rules:
* az network nsg list --resource-group "IntroAzureRG" --query '[].name' --output tsv
* az network nsg rule list --resource-group "IntroAzureRG" --nsg-name my-vmNSG --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' --output table
<img width="1514" height="114" alt="No 5" src="https://github.com/user-attachments/assets/ea710c2a-47e9-41fc-b6d3-d2ef732ff6ec" />

Once able to see that port 22 was only allowing a SSH connection, the next command was run to add in a new rule and allow access over HTTP:
* az network nsg rule create --resource-group "IntroAzureRG" --nsg-name my-vmNSG --name allow-http --protocol tcp --priority 100 --destination-port-range 80 --access Allow
<img width="1447" height="398" alt="No 6" src="https://github.com/user-attachments/assets/0572413e-d357-4b61-86f3-4f3de6a9210e" />

Running the command for the output table again showed the rule had been implemented, and allowed for access to the Web Server IP address.
<img width="1521" height="91" alt="No 7" src="https://github.com/user-attachments/assets/40836e01-b566-43c3-b4af-be2a0e0f0284" />
<img width="414" height="83" alt="No 8" src="https://github.com/user-attachments/assets/00beea30-1636-4dd3-b899-d2e0c5f705ab" />

This exercise made me more familair with Bash commands, how azure compiles data and allows access through ports, and how to configure it give access.
