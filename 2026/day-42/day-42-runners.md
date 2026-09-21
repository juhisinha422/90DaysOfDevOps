# Day 42 – Runners: GitHub-Hosted & Self-Hosted

## Task 1: GitHub-Hosted Runners

### Create a workflow with 3 jobs, each on a different OS 

Create a workflow with three jobs, each running on a different operating system: ubuntu-latest, windows-latest, and macos-latest.

```yaml

name: Multi-OS Workflow
on:
  push

jobs:
  linux-job:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout the code
        uses: actions/checkout@v7
      - name: Run linux command
        run: echo "This job runs on Ubuntu"
  windows-job:
      runs-on: windows-latest
      steps:
        - name: Checkout the code
          uses: actions/checkout@v7
        - name: Run windows command
          run: Write-Output "This job runs on Windows"
  macos-jobs:
      runs-on: macos-latest
      steps:
        - name: Checkout the code 
          uses: actions/checkout@v7
        - name: Run macos command
          run: echo "This job runs on macOS"
```


<img width="1365" height="726" alt="image" src="https://github.com/user-attachments/assets/0de7420e-762d-4a33-aaf0-0123620fbe26" />

### In each job, print the OS name, the runner’s hostname, and the current user running the job.

```yaml

name: Multi-OS Workflow
on:
  push

jobs:
  linux-job:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout the code
        uses: actions/checkout@v7
      - name: Run linux command
        run: echo "This job runs on Ubuntu"
      - name: Print OS Name via Environment Variable
        run: echo "The Operating system is ${{ runner.os }}"
      - name: Get Linux Hostname
        run: hostname
      - name: Get Linux Current User
        run: |
         whoami
         echo "Home Directory: $HOME"
  windows-job:
      runs-on: windows-latest
      steps:
        - name: Checkout the code
          uses: actions/checkout@v7
        - name: Run windows command
          run: Write-Output "This job runs on Windows"
        - name: Print OS Name via Environment Variable
          run: echo "The Operating system is ${{ runner.os }}"
        - name: Get windows hostname
          run: hostname
        - name: Get windows current User
          run: |
           whoami
           Write-Output "Home Directory: $env:USERPROFILE"
          
  macos-job:
      runs-on: macos-latest
      steps:
        - name: Checkout the code 
          uses: actions/checkout@v7
        - name: Run macos command
          run: echo "This job runs on macOS"
        - name: Print OS Name via Environmet Varibale
          run: echo "The Operating system is ${{ runner.os }}"
        - name: Get macos hostname
          run: hostname
        - name: Get macos current User
          run: |
           whoami
           echo "Home Directory: $HOME"

```

### Watch all 3 run in parallel

Yes. ✅ These three jobs are configured to run in parallel because they are separate jobs and there is no needs: dependency between them.

<img width="1056" height="268" alt="image" src="https://github.com/user-attachments/assets/2c4cf968-f233-48e4-b53d-a9b98b75c05b" />

### Write in your notes: What is a GitHub-hosted runner? Who manages it?

A GitHub-hosted runner is a virtual machine provided by GitHub to run GitHub Actions workflows. The runner is managed and maintained by GitHub.

## Task 2: Explore What's Pre-installed

### On the ubuntu-latest runner, run a step that prints the Docker, Python, Node.js, and Git versions, and look up the GitHub documentation for the complete list of pre-installed software available on ubuntu-latest

```yaml

name: Multi-OS Workflow
on:
  push

jobs:
  linux-job:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout the code
        uses: actions/checkout@v7
      - name: Run linux command
        run: echo "This job runs on Ubuntu"
      - name: Print OS Name via Environment Variable
        run: echo "The Operating system is ${{ runner.os }}"
      - name: Get Linux Hostname
        run: hostname
      - name: Get Linux Current User
        run: |
         whoami
         echo "Home Directory: $HOME"
      - name: Print Docker Version
        run: |
          docker --version
           docker version
      - name: Print Python Version
        run: |
         python3 --version
         pip3 --version
      - name: Print Node and npm Versions
        run: |
          node --version
          npm --version
      - name: Print Git Version
        run: git --version
      - name: List All Installed Apt Packages
        run: apt list --installed

```

### Write in your notes: Why does it matter that runners come with tools pre-installed?

It matters because pre-installed tools save setup time, make workflows faster, and ensure commonly used development tools are readily available without installing them manually in every workflow run.

## Task 3: Set Up a Self-Hosted Runner

### Go to your GitHub repository’s Settings → Actions → Runners → New self-hosted runner, choose Linux, follow the instructions to download and configure the runner on your local machine or a cloud VM such as EC2, Utho, or any VPS, and start the runner to verify that it shows as Idle in GitHub.

<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/88629cc7-d941-46da-bb33-d668105c2682" />


### Verify: Your runner appears in the Runners list with a green dot
<img width="1361" height="727" alt="image" src="https://github.com/user-attachments/assets/317542c2-22ca-4ab3-b780-04db9047bafa" />

## Task 4: Use Your Self-Hosted Runner

### Create .github/workflows/self-hosted.yml, set runs-on: self-hosted, add steps to print the machine hostname and working directory, create a file and verify that it exists on your machine after the run, then trigger the workflow and confirm that it runs on your own hardware and that the file is present on your machine.

```yaml
name: Self-hosted runner practice
on:
  push

jobs:
  own-machine:
   runs-on: self-hosted
   steps:
     - name: Checkout the code
       uses: actions/checkout@v4
     - name: Print the hostname of the machine
       run: hostname
     - name: Print Linux Working Directory
       run: pwd
     - name: 2. Create the file
       run: |
            echo "Hello from GitHub Actions!" > my-test-file.txt
            echo "Timestamp: $(date)" >> my-test-file.txt
      # Immediate verification using shell tools in the workflow log
     - name: 3. Verify file exists in logs
       run: |
          if [ -f my-test-file.txt ]; then
            echo "✅ File successfully found!"
            echo "--- File Content ---"
            cat my-test-file.txt
          else
            echo "❌ File not found!"
            exit 1
          fi

```

<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/b8cf365a-790f-420b-b3d7-1cbd24d79507" />

<img width="1360" height="729" alt="image" src="https://github.com/user-attachments/assets/50c02365-ceff-4d31-a5f8-05b23942d939" />


## Task 5: Labels

### Add a custom label such as my-linux-runner to your self-hosted runner, update the workflow to use runs-on: [self-hosted, my-linux-runner], and trigger the workflow to verify that the job is picked up by the runner.


```yaml
name: Self-hosted runner practice

on:
  push:

jobs:
  own-machine:
    runs-on: [self-hosted, Linux]

    steps:
      - name: Checkout the code
        uses: actions/checkout@v4

      - name: Print hostname
        run: hostname

      - name: Print working directory
        run: pwd

      - name: Create the file
        run: |
          echo "Hello from GitHub Actions!" > my-test-file.txt
          echo "Timestamp: $(date)" >> my-test-file.txt

      - name: Verify file exists
        run: |
          if [ -f my-test-file.txt ]; then
            echo "File successfully found!"
            cat my-test-file.txt
          else
            echo "File not found!"
            exit 1
          fi
```


## Task 6: GitHub-Hosted vs Self-Hosted

## GitHub-Hosted vs Self-Hosted Runners

| | GitHub-Hosted | Self-Hosted |
|---|---|---|
| **Who manages it?** | GitHub | You / Your organization |
| **Cost** | Pay-as-you-go / Included minutes depending on plan | You pay for and maintain the machine/infrastructure |
| **Pre-installed tools** | Many common tools are pre-installed | You install and maintain the required tools |
| **Good for** | Quick setup, CI/CD, testing, and standard workloads | Custom environments, private infrastructure, and specialized requirements |
| **Security concern** | GitHub manages the runner environment; secrets and permissions still need careful handling | You are responsible for securing, patching, and maintaining the runner machine |


