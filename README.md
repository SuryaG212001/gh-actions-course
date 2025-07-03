# gh-actions-course
github action repository wherein i will be walking you through github

gh actions:-> the feature as offered by github includes :
1.workflows
2.jobs
3.steps
it is a CI/CD pipeline solution provided by github
->workflows: edfined at teh repo level. so this has to be defined at the repo level.
->define the trigger which actually start the workflow
-> workflows are composed of one or more jobs inside them jobs 
jobs: 
-> these are defined at the work flow(they cannot be outside the env) and they also define at which os or env they run.
-> jobs may consists of one or more steps. note that if there is no dependency stated between the jobs then they run parallely

steps:
-> they are defined at teh job level
-> they define the actual scripts or the github actions that will be executed.
-> here we can have the github actions taken from other repository or taken it from teh market place.
-> steps will always run sequentially by default. if we want them to run parralley then add them to separate jobs

![alt text](image.png)


basic workflow:

name: my workflow
on: push  -> indicates the trigger

jobs:
    my-first-job:
        runs-on: ubuntu-latest
        steps:
            - run: echo "hello world"
    my-second-job:
        runs-on: ubuntu-latest
        steps:
            - run: echo "bye world"

here we have 2 jobs:my-second-job, my-first-job
note that the jobs will not use the same virtual machoine it may run on different virtual machine

workflow events:
->events can be considered as triggering
-> triggering workflows can be done in many ways in github workflows
1. repository events:
->  like push pull. so this workflow will be triggered on push or pull made to the repository
->  issues: triggered by a variety of events related to issues.
-> pull_request: triggered by a variety of events related to PRs
-> pull_request_review: triggered by a variety of events related to PR reviews(submitting, editting, deleting)
-> fork: triggered when your repository is forked

 the events can be given as an array or an object
 eg: array case
 on:
 - push
 - pull
 eg: object
 on :
     push:
     pull_request: for different branch we can have different events this way

2.manual trigger:
-> triggered via the UI: triggered from the actions tab in github
-> triggered via an API call: triggered via github REST API
-> Triggered from another workflow: Triggered from within another workflow

3. schedule: run the workflow a s a chron job
github events. the minimum interval for which github supports cronjob is 5 mins

refer this to generate a cron job expression
refer for events: https://docs.github.com/en/actions/reference/events-that-trigger-workflows?apiVersion=2022-11-28&versionId=free-pro-team%40latest&category=actions&subcategory=workflows#push


#workflow runners:
->these are the virtual servers that execute jobs from workflows. previously in other sessions we were utilizing only ubuntu runner. there are some other runners too

they include:
1. github-hosted runners
2. self hosted runners


#github-hosted runners:
1.  standard
2.  hosted
standard:
windows and ubuntu:
*   2 cores 
*   7GB 
*   14GB disk

mac runner:
*   3 cores 
*   14GB 
*   14GB disk


-> these are some managed services. ie., the security update ;packet change everything will be taken care by the github front
->  a VM is scoped to a job: steps in the same job will share the VM, but the jobs dont sahre the VMs .
-> so for a new job a new VM will be used

#self-hosted:
-> run workflows on almost any infrastructure of our choice
-> full control over the VM infrastructure
-> it's not managed , meaning we need to take care of OS pathcing, software updates, among other ops tasks
-> jobs do not necessarily have to run on clean instances.

note:
-> keep the VM resources in mind, especially when runnning commands that reply on parallel execution. there may be some stumbling upon the available github hosted resources.
-> we should not use the self hosted runners for a public github repo because on any vlunerability it might directly expose our infra

#github actions:
name: my npm package workflow
on: push
jobs:
    node-20-release:
        runs-on: ubuntu-latest
        steps:
            -uses: actions/setup-node@v3  #note the actions sometimes may include some predefined ones. it is a convention to mention the actions with actions/ as prefix. here we are utilizing some actions to setup node:20 version
             with: 
                node-version: 20
            -run: npm ci
            -run: npmtest
            -run: npm publish

#why to use actions?
-> to avoid repetitive and extensive commands
-> prevents code duplication and reduces the chances of mistakes. if  we want some action to be present in multiple workflows we can have them created as actions and utilize them.
-> the actions can be configured via the "with" key-value pair. so that if we want to change certain values then we can replace it at one place.
-> enables great flexibility and reusability
-> can be combined with other steps. can be used to encapsulate setup tasks before running other commands.
-. we can create our own actions for public or private use . we are not restricted only to the actions available in the maketplace of github

reference:https://github.com/marketplace?type=actions
note certain actions will be verified by github and its always better to use them 
there are certain actions verified and setup done by aws and others as well. so use the aactions that are used by many and also wheckout their repository to audit or their github actions 

# solution:
>>npx create-react-app --template typescript react-app
to create a react application using typescript
>> npm run react-app
run the application

note: 
if we are including some actions from another github repository it should run on certain working directory we can mention that as well.

# event filters:
it specifies under which conditions a specific evernt triggers our workflow

most triggers can be configured to specify when they should run.

eg:
push event
filetrs:
1.branches:specifies which branches mudt match in order for the workflow to execute
2.branches_ignore:specifies which branches must not match in prder for the workflow to execute
3.tags
4.tags_ignore
5.paths
6.paths_ignore

eg:
name:example-workflow
on:
    push:
        branches:
            - main
            - 'releases/**'
        paths-ignore:
            - 'docs/**'
jobs:
    node-16-tests:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/setup-node@v3
              with:
                node-version: 16
            - run: npm ci
            - run: npm test

note:
*   in the above case the workflow  will be triggered onpush to the main and if we make some changes to therelease branch and to the path docs/
*   everything is an and condition. so the push will be triggered only on satisfying all the filter conditions

reference:https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#onpushbranchestagsbranches-ignoretags-ignore

see to the topics of pull request, pull request atrget, branches and branches ignore, push branches,tags, branchwes and branches e=ignore etc.,

# activity types:
- they allow us to specify which type of certain trigger can execute our workflow

- many trigger have multiple activity types we can leverage

- consider a pull_request event, we can have the following activity types:
1. opened: runs  the workflow whenever a PR is opened
2. synchronize: runs the workflows whenever a new commit is pushed to the HEAD ref of the PR
3. closed: runs the workflows when someone closes the PR
4. assigned
5. labeled
6. edited tec.,

- there are manythings that can happen during a pR lifecycle all of them have their own activity type and it allows us to trigger a workflow.

eg:
name: my NPM package workflow
on: 
    pull_request:
        types:[opened. synchronize]
        branches: 
            - main
            - 'release/**'
jobs:
    node-20-tests:
        runs-on: ubuntu-latest
        steps:
        - uses : actions/setup@v3
          with:
            node-version: 20
        - run: npm cli
        - run: npm run test
- in the above exmple the workflow will be triggered when ever there is a PR opened or synchronized to main or release branch  
- note that we can have any number of event trigger and any number of activity type but amongst the event filters its a or condition and among the activity type it is a or condition and between the activity type and the event filter it is an and condition
- by default the github activity type for pull request is opened, synchronized and reopened
-anything other the 3 activity types we have to explicitly specify them

reference:
https://docs.github.com/en/actions/reference/events-that-trigger-workflows#pull_request

topic:



