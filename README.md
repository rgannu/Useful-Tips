**Export/Import GIT repository across machines**

Before you start, make sure you know both clone URLs for the repos you are trying to export/import.

For example, to import it on GitHub, make sure that the repository you’re trying to push to already exist on GitHub and is empty.

**Step-By-step**

Make a “bare” clone of the repository (i.e. a full copy of the data, but without a working directory for editing files) 
using the external clone URL. This ensures a clean, fresh export of all the old data.

Push the local cloned repository to GitHub using the “mirror” option, which ensures that all references 
(i.e. branches, tags, etc.) are copied to the imported repository.
Here are all the commands written out:

------
# In this example, we use an external account named extuser and
# a GitHub account named ghuser to transfer repo.git

# Make a bare clone of the external repository to a local directory
_git clone --bare https://githost.org/extuser/repo.git_

# Push mirror to new GitHub repository
_cd repo.git_
_git push --mirror https://github.com/ghuser/repo.git_

# Remove temporary local repository
_cd .._
_rm -rf repo.git_
-----

Your newly imported repository should be ready to go on GitHub!