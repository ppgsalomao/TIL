# Configuring GIT on Windows with SSH
In order to configure GIT on Windows to be used in Power Shell, I used the Chocolatey package manager:

```choco install git```

Once GIT was installed, I generated my SSH key by using the command:

```ssh-keygen -t rsa```

And answering nothing to all questions asked (using defaults and no-password).

With that done, I got my SSH key from `C:\Users\username\.ssh\id_rsa.pub` and registered on GitHub to use it.

It was as simple as that.