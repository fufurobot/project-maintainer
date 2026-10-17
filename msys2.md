now read @todo.md and implement the project. always keep the git workspace clean, commit immediately as long as you edit files. if you understand, commit staged changes first, then the unstaged.

# .env spec

.env should NEVER be exposed to the public. if some tool depended on that, use a different name and ensure it is ignored first. 

create a `.env.example` with all sensitive credentials erased. 

# TDD spec

use TDD to push the project (unit test first, red as WIP commits and green as implementation commits). use git log to check how to do if any. once you have done, use git push to push to remote.

# msys2 specific clarifications

 `uv`, `git`, `ollama`, `npm`, `bun` and `podman-compose` are  at `C:\Users\fufu\Downloads\msys64\clang64\bin`, feel free to use. use C:\Users\fufu\Downloads\msys64\msys2_shell.cmd to mimic msys2 environments (you need escalation to do that, so keep these call minimal). `bun` is installed by `npm` provided by `mingw-w64-nodejs` package which combability might be not as good as `npm`. if they are not available, just escalate to install them. 

## ssh issues on windows msys2

ssh may be blocked not because of the sandbox but the msys2 ssh binary depends on the msys2 dll which is often outside the sandbox. change to windows native ssh client to do git push, and check the ssh key matches under msys2 environment and copy keys if they don't match (use msys2 home directory as the source of truth)

## deepseek harness sandbox issues on windows and linux

deepseek will actively block `curl.exe` to not accidentally visit internet. on Linux the sandbox is even stricter: `/dev` is complete virtual and jailed.

but python code accessing the internet is not restricted on windows and Linux. 

you need to escalate (or prompt me to switch to dangerous full access mode, but i prefer escalate) to bypass the sandbox. 

remember i have NO TIME to play with your trial and error game for multiple escalations. if you have to access to internet, write reusable python scripts instead of the fancy `curl` call. 

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

## what if api token is absent

 if the api token is 
not given now, means neither in `.env` or the prompt, ask three questions: first ask whether i want push GitHub ci blindly, then permission layers for the fine-grained api token for this repository (repository admin for everything, only necessary for ci and publish thought, only necessary for publishing thoughts, no api token), then whether the deepseek should register its own GitHub account if user decide not to give the api token (by a domain name you manage to receive mails, by a email you provided, or by deepseek itself). you may use or install playwright under the uv managed python env to do what you want, and install `pyautogui` to capture `playwright codegen` windows and screenshots, both as dev dependency.

If there is a token, do not ask anything.

## what do i mean by deepseek itself:

use following procedure:

register a Microsoft account. use pokemail.net as recovery email and select and untick scramble on the interface of https://grr.la. this way you get a outlook email with proper password as secret. then you register GitHub.com use that outlook email. then you get a full functional GitHub account that can fork existing repositories or publish it if the remote is not GitHub.com.