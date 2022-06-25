# pulumi-azure-minecraft-server

Pulumi automation to deploy a Minecraft Server on Azure

This setup uses [pnpm](https://pnpm.io/) but feel free to use npm or yarn

# Prerequisites

- An Azure subscription
- A service principal with [Contributor role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) at the subscription level.
- A Pulumi account and logged in via `pulumi login`

The following automation uses a `Standard_B2s` virtual machine. See more information [here](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-b-series-burstable)

# Setup

1. Initialize a new stack in pulumi.

   ```bash
   pulumi stack init main --cwd ./src
   ```

2. Set up the pulumi configuration. Replace the temporary values with your configuration for Azure

   ```bash
   pulumi config set azure-native:location $LOCATION
   pulumi config set azure-native:subscriptionId $SUBSCRIPTION_ID
   pulumi config set azure-native:tenantId $TENANT_ID
   pulumi config set azure-native:clientId $CLIENT_ID
   pulumi config set --secret azure-native:clientSecret $CLIENT_SECRET
   pulumi config set adminUsername $VM_USER_NAME
   pulumi config set --secret adminPassword $VM_PASSWORD
   ```

3. Run the pulumi automation

   ```bash
   pulumi up --yes --skip-preview
   ```

4. Connect to the virtual machine via ssh

   > When the pulumi automation has finished you should see the `connection` variable as an output. This is the command you need to run in order to connect to your virtual machine. When prompted for your password enter the password you used for your configuration variable `adminPassword`

   ```bash
   ssh $USER@$IP_ADDRESS
   ```

5. Run the setup script to configure your virtual machine and run a 1.19 minecraft server

   ```bash
   sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/Jamess-Lucass/pulumi-azure-minecraft-server/bin/setup.sh)"
   ```

6. Connect to your minecraft server

You may disable SSH Port in your NSG if you wish to not allow ssh connections to your virtual machine. Note that if you will need to turn this back on if you ever wish to ssh into your machine

# Useful commands

- Save your minecraft server in it's current state

  ```bash
  screen -S minecraft-server -X stuff 'save-all'$'\n' ;
  ```

- Stop your minecraft server

  ```bash
  screen -S minecraft-server -X stuff 'stop'$'\n' ;
  ```
