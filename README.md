# Assurity DevOps Assessment
For this assessment I had the following brief;
Using automation tooling of your choice (with the exception of Vagrant):

* provision an Ubuntu 16.04 server
* carry out any configuration steps needed to allow login over ssh, securely and disable password based login
* Setting up the firewall to only allow ingress on the ssh port and
* only to allow password-less (certificate) based login
* then display the MOTD including the text "Hello Assurity DevOps”.

## Getting Started
I chose to have an automated deployment using VSTS Release manager deploying to Microsoft Azure. With the aim to have relase manager deploying a the VM with the required onfiguration in the brief and then have a secondary taskto copy a modt file to the /etc directory on the server and then reboot the server. 
### Prerequisites
To replicate this installation you will need to have the following:
Access to Git, VSTS(permissions to setup a new Release Definition ) and Azure(permissions to create a new service endpoint or use an existing one to deploy to a Active Azure Subscription).

### Deploying the solution
Clone the repository containing my solution from here (https://github.com/Dries-Venter/AssuTest)
1. In VSTS create a new release
    Using the Azure Resource Group Deployment task create a new deployment specifying the following:
        * Display name : Azure Deployment:Create Or Update Resource Group action on Assu-DEV-RG01
        * Azure Details
        * Azure subscription : Specify your azure service endpoint or create one
        * Resource group : Assu-DEV-RG01
        * Location : Southeast Asia
        * Template location : $(System.DefaultWorkingDirectory)/_AssuSolution/AssuSolution/azuredeploy.json
        * Template parameters : $(System.DefaultWorkingDirectory)/_AssuSolution/AssuSolution/azuredeploy.parameters.json
        * Override template parameters:
        * -adminUsername "xadmin" -adminPassword <enter_a_secure_password_here> -vmName "Assu-DEV-VM01" -adminPublicKey "ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAq6jBOcRGwzu/Z48nFg7GH1O3zI1FAR4xJlqhKXKeSNiJMzazqDdOBNO8Z3VVMdKI12bWw4GyQZkHu2iqlLtWNSZ85deEQlpxDCQHe4RTQJMxCr7r9jTj7UKDselM83UlnU2YHbbhTtmqxDUiexIFabkNDXngdJjfXIUmN5DZDyaHn9PLFOvuqXxCiJolTcAoIOHjFPxX6C0KK9xswkP498JtPd1dK7DJXXGE1V3XlstC3rvYRr1vBoHjG5GoehseMF4UPEL2wnhJWhK4+TruhxHh5lPR7AttC+opdWq5zuyQ77XcZqFEd5sd6HMg3Gel4x1CrDzTqBc85zK74wd8hw==" -dnsNameForPublicIP assudevvm01 -ubuntuOSVersion "16.04-LTS" -networkSecurityGroupName "Assu-DEV-VM01-nsg" -mySourceIPAddress "<enter_your_public_IP_here(Use CIDR format)>" -nicName "Assu-DEV-VMNic" -vmSize "Standard_B1s" -virtualNetworkName "Assu-DEV-VNET" -diskSizeGB "30"
        * Deployment mode: Complete
        * Advanced deployment options for virtual machines
        * Enable prerequisites : Configure with WinRM agent
        * VM details for WinRM :$(ResourceGroupName)
        * Outputs
        * Deployment outputs: $(DeploymentOutput)
        * Control Options: Enabled
        * Continue on error: Timeout

2. Next trigger a release to deploy the VM

## Running the tests
After the VM has been deployed login to the VM using any ssh client. In my case I used Putty. you cand find the connection string to the VM when you login to portal.azure.com with an account that has access to the subsription that you deployed to.
Please note be cause you are authenticating with a private and public key pair you need to confugure Putty to authenticate using the private key file. These files are stored in the repo in the following directory \AssuTest\AssuSolution\Files.
The following site has a section to guide you though the authentication setup in putty if you are unfamilliar with the process (https://www.linode.com/docs/security/authentication/use-public-key-authentication-with-ssh/)


### BUGS
####Automation of motd file
    Because I am not really familiar with linux I had some dificulty automating the copying the motd file from the release agent to the VM. I was able to do so using putty and winscp. The problem was to get the hosted vsts agent to successfully authenticate to the VM to run a script to copy the files in place 
    I have copied a screenshot of the terminal view. \AssuTest\AssuSolution\Files\Screenshot_of_motd_displayed_in_terminal.jpg
    The motd file is also in the same file location.

## References used in the project
    * https://www.linode.com/docs/security/authentication/use-public-key-authentication-with-ssh/
    * http://the.earth.li/~sgtatham/putty/0.53b/htmldoc/Chapter8.html
    * https://4sysops.com/archives/powershell-remoting-with-ssh-public-key-authentication/
    * https://cmatskas.com/run-ssh-with-powershell/
    * https://support.rackspace.com/how-to/logging-in-with-an-ssh-private-key-on-linuxmac/
    *https://superuser.com/questions/601942/use-bash-script-to-copy-select-txt-files-from-remote-server-to-my-local-machine/601943

 

## License
No Licence
## Acknowledgments
* The opensource and linux comunities for all the how to        guides. :)

