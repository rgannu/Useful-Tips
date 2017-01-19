# Export/Import GIT repository across machines

Before you start, make sure you know both clone URLs for the repos you are trying to export/import.

For example, to import it on GitHub, make sure that the repository you’re trying to push to already exist on GitHub and is empty.

## Step-By-step

Make a “bare” clone of the repository (i.e. a full copy of the data, but without a working directory for editing files) using the external clone URL. This ensures a clean, fresh export of all the old data.
Push the local cloned repository to GitHub using the “mirror” option, which ensures that all references (i.e. branches, tags, etc.) are copied to the imported repository.

In this example, we use an external account named extuser and
a GitHub account named ghuser to transfer repo.git

Make a bare clone of the external repository to a local directory
```bash
git clone --bare https://githost.org/extuser/repo.git
```
Push mirror to new GitHub repository
```bash
cd repo.git
git push --mirror https://github.com/ghuser/repo.git
```

In case you get the error **"SSL certificate problem: unable to get local issuer certificate"**, follow the procedure as described in
 
[Microsoft Blog](https://blogs.msdn.microsoft.com/phkelley/2014/01/20/adding-a-corporate-or-self-signed-certificate-authority-to-git-exes-store/)

Remove temporary local repository
```bash
cd ..
rm -rf repo.git
```
Your newly imported repository should be ready to go on GitHub!