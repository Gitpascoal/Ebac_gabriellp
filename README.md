PS C: > git clone https: //github.com/JovanneSousa/teste.git
Cloning into 'teste'
warning: You appear to have cloned an empty repository.
PS C: > cd teste
PS C: teste> git branch -M teste
PS C:\teste> echo 'teste' › teste.txt
PS C: teste> git add
PS C: \teste> git commit -m 'initial commit' 
  [teste (root-commit) 504c11c] initial commit
  1 file changed, 0 insertions+), 0 deletions-) create mode 100644 teste.txt
PS C: teste> git push
fatal: The upstream branch of your current branch does not match the name of your current branch.
To push to the upstream branch
on the remote, use
  git push origin HEAD: main
To push to the branch of the same name on the remote, use
git push origin HEAD
To choose either option permanently, see push default in 'git help config'.
To avoid automatically configuring an upstream branch when its name won't match the local branch, see option 'simple' of branch. autoSetupMerge in 'git help config'.
PS C: teste>
