# Project maintainer that I used to maintain project for any harness.

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/fufurobot/project-maintainer)

This is aimed to be installed by `npx skills`, yet it is still installable by `pip install <wheel name in release>`.

As a python package this is basically a language independent project initer, with a nice cli if option specified and 

it will scan the PATH and find proper project dependency manager, OS info and *-smi manager for GPU/NPU support. 

# Subagents

I mainly work on deepseek harnesses, which has a strict sandbox on windows and linux. and I use MSYS2 to work on windows. 

Subagents can be powerful, but can also be costful.

This skill takes an compromised approach: use local models as subagents.

This skill will search on huggingface to find proper local models to do the repetitive task or task that involves lots of trial and errors. 

This skill will probe whether there are ollama instances on the local machine. If yes, it would use it to import GGUF models for inference. otherwise, it uses its own shipped `vllm` or `vllm-omni` to deploy subagents. 

Before this skill run subagent, it will probe the CPU and available RAM/VRAM to find the proper sized model to deploy. 

# Finetuning

The subagent can also be finetuned using huggingface toolchain and gymnasium for task modeling and reforinced finetuning on the task. `Harbor` is used if the task requires executing code.

It is strictlly forbidden that the master model use itself as a LLM as a judge. 

If judge is required, find existing huggingface dataset and do SFT/DPO instead. 

the finetuning only happens when there is an GPU backend (*-smi) available, in order to not occupy all cores on user's machine and make user can not do anything else. 

# todos

- [ ] merge the multiple skill content markdown file into a single SKILL.md file
- [ ] add a entrypoint to install pre commit and post commit hooks (linting and type check for pre commit, smoke test for post commit)
- [ ] add a entrypoint to help users for boilerplates
- [ ] add a default model and ship the gguf inside the python wheel (qwen3.5:0.8b) so that the package can booststrap itself
- [ ] finetune qwen3.5:0.8b for the TDD dispiline by reinforincement learning (dog fooding).
