now read @todo.md or @README.md and implement the project. always keep the git workspace clean, commit immediately as long as you edit files. if you understand, commit staged changes first, then the unstaged.

# .env spec

.env should NEVER be exposed to the public. if some tool depended on that, use a different name and ensure it is ignored first. 

create a `.env.example` with all sensitive credentials erased. 

# TDD spec

use TDD to push the project (unit test first, red as WIP commits and green as implementation commits). use git log to check how to do if any. once you have done, use git push to push to remote.

# github api token

the api token is here: `{{your fine-grained GitHub api token that can submit issues and pull requests}}`

please save immediately into .env file and ensure it is properly ignored. submit issues and pull requests to make your thoughts public. check the remote ci status if possible.

## the sprint procedure

before you write any code or explore any problems, switch to a new branch and submit a issue immediately. 

when you commit new code, submit a pull request and mark it as draft immediately. 

when you get progress, no matter success or failure, reply to the issue as well. once it is successful, merge the pull request to dev and 

## ci

if it has ui, publish it to GitHub pages.

check if the api is allowed to configure dependabot. if yes, configure it as well.

the branch model is do fast test on dev, do full test on main, publish artifacts when release is pushed. 

it doesn't matter if ci is not green on dev.

# what if github api token is absent

if the session is already logged in by GitHub, try your best to use that to do your sprint. otherwise, create a .sprint folder for your sprint and commit first.