# AvenirLabs AI

This documentation aims to provide an overview of setting up and using AvenirLabs' documentation generator for Solidity. The way it's envisioned to work is to add the specific GitHub action scripts from this repository to your own and use them there.

## Prerequisites

1. Correct repository access rights

Make sure you have at least admin access rights to a GitHub repository (if dealing with an organization-level repository), or that you are the repository owner (if on a personal GitHub plan). You'll need these to set up Action Secrets later.

2. OpenAI API key

This product is using OpenAI to analyze Solidity code. You'll need to generate an API key on their website and you can learn more about it [here](https://platform.openai.com/docs/quickstart/account-setup).

## Action Scripts

Next, we'll discuss the GitHub action scripts that we have in the repository and how they might be of use to you. 

### Set up

Copy the GitHub actions you wish to use to your repository's `.github/workflows` directory. Next, in your repository's settings go to `Settings -> Secrets and variables -> Actions`. You'll see a `Repository secrets` section. Add a new repository secret named `OPENAI_API_KEY` and put your OpenAI API key in there.

After the key has been set, copy the following action scripts to your repository.

### GitHub Actions/Workflows

#### Environment set up

Workflow name: `environment_runner.yml`

Usage instructions:

1. Copy the `environment_runner.yml` script to your repository's `.github/workflows` directory.
2. Commit and push the change to your `main` branch. And make sure it is the `main` branch––pushing to any other branch won't list the workflow in your repository's GitHub actions list due to the way GitHub actions work.
3. Run the workflow once with a `setup` action specified in the launch window input field. In case you're new to GitHub actions, check this [official guide](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow) on how to run a workflow.
4. Wait for the workflow to finish––it should have a green checkmark next to it on the left side indicating a successful run.
5. Click on the finished workflow to see its details.
6. In the left sidebar select the `set-up-environment` job (under `Jobs` section), and on the right side of the screen click on the `Run environment setup script` to reveal the details.
7. You should see a line saying `Created assistant with ID: asst_xxxxx.....yyyy`. Copy that assistant ID, and create a new repository secret (as described above) called `OPENAI_ASSISTANT_ID`. Paste the generated assistant ID as the secret's value and save it.

#### Documentation generator

Workflow name: `documentation_generator.yml`

Usage instructions:

1. Copy the `documentation_generator.yml` script to your repository's `.github/workflows` directory.
2. Commit and push the change to your `main` branch.
3. You can now run the workflow either on the whole branch, or on a specific PR you wish to generate the documentation for. You can find more information on one versus the other in the branch and PR-specific run sections below.
4. The generated documentation will be attached to the specific workflow run as an Artifact which you can download and extract upon the run's end. You can access it by selecting a specific run of the workflow, selecting the `Summary` in the left sidebar and then looking for it on the main screen area at the very bottom, under the `Artifacts` section. You can find out more about artifacts in the official GitHub's documentation [here](https://docs.github.com/en/actions/managing-workflow-runs/downloading-workflow-artifacts).

**_Important:_** Documentation runs can take some time, e.g. a couple of minutes per module, depending on the size and code complexity. It's important to be patient while the documentation is generated.

##### Input fields overview

* documentation_scope: there are three possible values to choose from here. `contract_only` will generate documentation on a contract-base, meaning it will document contracts and their functions, modifiers and events. `code_breakdown` option will generate a breakdown (an overview) of the whole codebase without generating a modifier or function specific documentation–it will simply provide you with a product-level documentation. `both` will generate both of the documentation types mentioned here,
* code_folders: a comma-separated list of folders you wish to traverse e.g. `src,lib`,
* pr_number: an optional PR number if you wish to document PR-specific files only,
* file_types_to_include: a comma-separated list of extensions you wish to include while searching for contracts to document e.g. `.sol`,
* file_names_to_include: a comma-separated list of specific file names you wish to include while searching for contracts e.g. `SomeERC721Factory.sol`,
* folders_to_exclude: a comma-separated list of specific folders you wish to exclude from the traversal,
* file_types_to_exclude: a comma-separated list of extensions you wish to exclude from search e.g. `.txt,.json`,
* file_names_to_exclude: a comma-separated list of file names you wish to exclude from search e.g. `README.md,LICENSE`.

##### Inclusion and exclusion functionality

Following are the rules on how the inclusion and exclusion rules work:

1. Exclusions are always checked **before** inclusions. This is true for both folder, filename and file type exclusions.
2. Exclusions rules are **OR** based, meaning that we check whether a specific filename satisfies the `isExcludedByName || isExcludedByExtension` expression.
3. Inclusion rules are **and** based, meaning that we check whether a specific filename satisfies the `isIncludedByName && isIncludedByExtension` expression.

##### Branch-specific run

1. Select the `documentation_generator.yml` workflow and specify a branch you wish to run it on.
2. Run the workflow.

##### PR-specific run

1. Select the `documentation_generator.yml` workflow and specify `main` as the branch.
2. In the `pr_number` input filed, specify the number of the PR you wish to run the workflow on. You can find the number of your PR either by clicking on it and looking at it's title (in this case it should be followed by the PR number e.g. `#1`), or by looking at the PR's URL e.g. `https://github.com/{owner}/{repo}/pull/{pr_number}`. Make sure to enter a PR number without the `#` sign in the input field–just a plain integer.
3. Run the workflow.
