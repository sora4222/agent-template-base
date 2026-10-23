---
name: create-makefile
description: Use for creating, updating, or better reading a Makefile. Also use for altering README.md of a repository.
---

# Create/Update make file

Use this skill for creating or updating a makefile for a monorepository or a repository with multiple projects.
A user may have separated projects into multiple repositories, update the multiple repositories if the user has given this information.



## Retrieving or recording purpose

The purpose of each part of a repository should be derivable from any one of: 
 - an agents task/context
 - the repository root README.md
 - README.md file contained in the directories or multiple repositories

Otherwise create a summary and confirm with the user that the summary is accurate.

If the purpose is not derivable from the README.md file(s) a short edit to the README.md(s) should be added directly under the title. To make the edit: 

 1. Give a clear indication of what is being made in the repository or subdirectory, the dependencies, and any frameworks that are used. This information should give an understanding of the software's lifecycle, as well as development stages.
Note: A short statement "a Tauri application...", or "a React Native program..." when a programming language specific framework is used can be substituted for part of the description to give more context about the software lifecycle
 2. Environment variables that are required should be listed in a "Environment Variables required" subheading.
 3. A short edit to `CONTRIBUTING.md` in a `Software Lifecycle` heading may give more information about what needs to happen to setup, create, see edits, build, update(databases whilst keeping existing records, or application as a whole), keep it short.
 4. If the application is spread among multiple repositories the `README.md` of each will need to link to the other repositories. Keep these in a short heading `## Related repositories` with a list of the repository and it's responsibility to the application.

## Creating the commands and their scripts

With the context of the directories, repository and software lifecycle, create the commands for Makefile scripts.

Follow rules for creating commands:

 - The "Base commands" must follow the desctiptions, these are the standard commands used for reading and writing to the Makefile, not following these is poor UX for developers, creating other commands is possible but these commands must follow the descriptions
 - Each of the Makefile commands names should be short
 - Do not duplicate commands, if required then make commands dependencies to make an alias
 - Commands by default should minimise output of scripts to reduce agent cost with a `--human` argument to display more for a more human readable output. This should be a minimal change to the script, do not add considerable changes unless instructed to
 - Use standard Makefile coding principles such as declaring `.PHONY`, using dependencies for a script, use patterning and any other Makefile "code foo" to allow the Makefile to better write the scripts
 - Allow independent targets and scripts to run in parallel with `make -j`. The command lists below define coverage, not execution order. Order only commands with real dependencies or shared writable files, and keep that ordering scoped to the affected work.
 - Prefer writing Bash scripts, putting large scripts (more than 15 lines) in `scripts/` directory
 - Do not only make edits to the file for the current goal, refactor using Makefile techniques to create better commands and scripts.
 - Scripts must be written assuming the application is being run by multiple agents on a single computer in different worktrees, they need to not interact with each other, separate ports and containers need to be setup for this
 - Never expose information in the commands or scripts called unless instructed in an original task to do so, when you do so always give that information in your response.
 - Do not rely on `sudo` for scripts other than scripts output for a user deploying applications, privledged access should be avoided.
 - Try to allow parallel and fast execution of scripts/commands, do not use NOPARALLEL unless required.

## Base commands/script

Repositories may have multiple parts, but a Makefile with predictable commands creates an easy to use interface for developers and agents.
The following commands are expected to be declared adhering to the requirements of their descriptions.
Term "application" refers to the software being developed and the components required to run it.

`setup` - Sets up the repository environment to install/upgrade packages, create environments for the projects and subdirectories and download anything that is necessary to be able to run the application that a developer would not be expected to have
`setup-env` - Creates a copy of the .env.example to `.env` and can generate new values if needed.
`setup-env-worktree` - Create only if needed to copy environment files, docker volume, generate separate environment variables for each worktree upon creation of the worktree.
`dev` - Developer usage only. Will run the application in development mode with changes to the software seen by the developer quickly (often referred to as watching), this command must start the application as a whole, any dependencies should also be started, on keyboard interrupt, the script should try to stop all parts of the application.
`build` - Only declared when a part of the writable software needs to output compiled/built software. Create individual scripts that this script is dependendent on with name `build-<component>` that will build each individual part, component name should be the name of the directory containing the files or a suitable replacement. The main command moves outputs to a `build/<component>` directory at the root of the repository, clear existing directories before moving new output. Do not include this for software that does not need to be "built".
`install-server` - Create to setup the built application on a Unix server securely, preferably as a service that will start on the server. Store options given, or created, so repeat executions run with this information. Databases should use a restricted user to keep database tables secure, do not install baremetal dependencies through this script without permission.
`uninstall-server` - Removes the application from a server, use options storage. Do not remove dependencies other applications may use without user approval
`test` - Create to run all unit tests of the written software of the repository. Use dependencies to run more focussed test commands which can be called independently to test different aspects of a component when multple testing frameworks or approaches (unit and integratioin) are used, name `test-<component>(-<method>?)` e.g. `test-frontend-playwright`, or `test-backend`
`dev-stop` - Only declare this when there are parts of the `dev` command that when the developer keyboard interrupts may require the developer to stop background threads, docker containers or other components to the application that would not have responded to the keyboard interrupt. The developer should be able to run `dev` and work with the application again.
`check` - runs `format`, `lint`, `test` and `type-<component>`, for formatting, linting, all tests and static type checking, the order listed is not a required order executed.
`format` - runs all `format-<component>` scripts checking and automatically fixing any formatting issues found by the formater disclosed by the developer, ask if no formatter was disclosed.
`lint` - runs all `lint-<component>` scripts, each script run all lint packages approved by the user, if none have been disclosed ask the developer, by default use the packages to fix all errors found.
`reset-db` - Only create when the application relies on any database. Will allow developer to reset and reinitialise the database, rely on Alembic for initialisation.
`update-db` - Only create when the application relies on a database and can be updated with scripts like Python's alembic. When no parameters are given try to update the database without deleting the tables/rows.
`create-application-<component>` - Potentially an alias for a build command. These will package/compile a desktop application ready for usage on current operating system.
`test-e2e` and `test-integration` - Optional, only add when the developer has specified that they want integration and end to end tests.
`commit-rules` - Optional, only when rules for commits have been specified.
Each of these commands can have more dependencies to better target specific components of the application, an example `build-frontend` could build the frontend of the application.

An `all` target should be setup, running `check`, `test`, `build`.
