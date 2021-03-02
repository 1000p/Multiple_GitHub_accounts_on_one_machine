
#This is a how to on how to have multiple GitHub accounts on one machine.

### 1. Create a SSH key/s.
 - If ~/.ssh/ does not exist - create one
- `ssh-keygen -t rsa -C "your-email-address"`
 - Be careful to not override your existing keys
  * E.G Use something like the following ~/.ssh/id_rsa_YOUR_NEW_KEY
 
### 2. Attach the key you have just created to GitHub.
 - Go to "Your profile" -> "Settings" -> "SSH and GPG keys" -> "New SSH key"
 - Open your ~/.ssh/id_rsa_YOUR_NEW_KEY.pub and copy the entire string to the GitHubs key text field and save the key.
### 3. Start your SSH agent
 - Type `eval `ssh-agent`` in your git bash
### 4. Add your SSH key
 - Type `ssh-add ~/.ssh/id_rsa_YOUR_NEW_KEY`
### 5. Create SSH config
 - In git bach `touch ~/.ssh/config`
 - Open it with editor and paste:
`
          Host github.com
             HostName github.com
             User git
             PreferredAuthentications publickey
             IdentityFile ~/.ssh/id_rsa #Here you have to enter your default SSH key that you are going to use for github
             AddKeysToAgent yes
           # Copy the following lines as many times you like, make sure you change the key and the host if needed
           Host github-YOUR_ACCOUNT_NAME
             HostName github.com
             User git
             PreferredAuthentications publickey
             IdentityFile ~/.ssh/id_rsa_YOUR_NEW_KEY #The key you have just created
             AddKeysToAgent yes
             `
 - Be sure to remove the comments from the above
### 5. To add remote or clone a repository use:
 - `git remote add origin git@github-YOUR_ACCOUNT_NAME:Git_Hub_Account/repository.git` *Be careful!* `git@github.com:Git_Hub_Account/repository.git` is the default address given by git when you create your repository. You have to change that with the host you have provided e.g : `git@github-YOUR_ACCOUNT_NAME:Git_Hub_Account/repository.git`
 - `git clone git@github-YOUR_ACCOUNT_NAME:Git_Hub_Account/repository.git`

## If you don't want to be prompted everytime you try to reach the remote for a SSH passphrase you can do the following:
### 1. Create a .bashrc if you do not have one
 - in git bash `nano ~/.bashrc`
 - Write and save the following:
        `
        # Set up ssh-agent
        SSH_ENV="$HOME/.ssh/environment"
        
        function start_agent {
            echo "Initializing new SSH agent..."
            touch $SSH_ENV
            chmod 600 "${SSH_ENV}"
            /usr/bin/ssh-agent | sed 's/^echo/#echo/' >> "${SSH_ENV}"
            . "${SSH_ENV}" > /dev/null
            /usr/bin/ssh-add ~/.ssh/id_rsa_YOUR_NEW_KEY
            /usr/bin/ssh-add ~/.ssh/id_rsa
        }
        # Source SSH settings, if applicable
        if [ -f "${SSH_ENV}" ]; then
            . "${SSH_ENV}" > /dev/null
            kill -0 $SSH_AGENT_PID 2>/dev/null || {
                start_agent
            }
        else
            start_agent
        fi
`
