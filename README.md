# Workshop Guidebook: Automating Application Security Testing in GitHub Actions

This workshop is designed to help you get started with the HawkScan in GitHub Actions. Participants get hands-on experience with:

* GitHub Enrollment
* GitHub Actions workflows
* StackHawk dynamic application security test (DAST) scanning

You can find the slide deck for this workshop [here](https://docs.google.com/presentation/d/1zG8fjB9G7TFHzVWEUpCy184B2TdYV1Jjvtc_j2anJwc/edit?usp=sharing).

## Prerequisites

To get the most out of this workshop, make sure you have the following prerequisites before getting started.

* Join us in the `#gtm-scan-workshop` channel on Slack
* A web browser

## Step 1: Get a GitHub Account

Go to the [GitHub Signup Page](https://github.com/signup) and sign up for an account!


## Step 2: Continuous Integration Workflows in GitHub Actions

Fork the [`vuln_node_express`](https://github.com/kaakaww/vuln_node_express) app:

<https://github.com/kaakaww/vuln_node_express>

Go to the **Code** section of your newly forked repository in GitHub. Create a new file using the **Add file --> Create new file** button. Name the file `.github/workflows/build-and-test.yml`, and add the following contents:

```yaml
# .github/workflows/build-and-test.yml
name: Build and Test
on:
  push: 
    branches:
      - main
  pull_request:
jobs:
  build-and-test:
    name: Build and test
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Install Node.js 14.x
        uses: actions/setup-node@v2
        with:
          node-version: 14.x
      - name: Install dependencies
        run: npm clean-install
```

Commit the change.

Go to the **Actions** section of your repository, and you should see the new workflow running.

## Step 3: Dynamic App Scanning with StackHawk 🦅

[Sign up](https://app.stackhawk.com) for a StackHawk Developer account. Follow the Get Started flow to create your StackHawk API key and first application.

### Stash the API Key

Stash your StackHawk API key in GitHub Secrets. In your repo, navigate to the **Settings** section, and find **Secrets** in the left pane.

Add a secret named `HAWK_API_KEY`, and add your StackHawk API key as the value.

### Commit the `stackhawk.yml` Configuration File

Download the `stackhawk.yml` file that you created in the Get Started flow in the StackHawk platform. Copy the contents into a new file at the base of your repo named `stackhawk.yml`. Commit the file.

### Add a StackHawk Scan to your Build and Test Workflow

Update your Build and Test workflow. Add a step to start the `vuln_node_express`, and a step to run HawkScan using the StackHawk Action at the end:

```yaml
# .github/workflows/build-and-test.yml
name: Build and Test
on:
  push: 
    branches:
      - main
  pull_request:
jobs:
  build-and-test:
    name: Build and test
    runs-on: ubuntu-20.04
    steps:
      - name: Check Out Code
        uses: actions/checkout@v2
      - name: Install Node.js 14.x
        uses: actions/setup-node@v2
        with:
          node-version: 14.x
      - name: Install Dependencies
        run: npm clean-install
      - name: Daemonize our Node API service
        run: nohup npm run start &
      - name: Run HawkScan
        uses: stackhawk/hawkscan-action@v1.3.1
        with:
          apiKey: ${{ secrets.HAWK_API_KEY }}
```

Commit this change.

### Check your Scan Results

Go to the **Actions** section of your repo, and watch your updated Build and Test workflow run. Examine the **Run HawkScan** step console logs.

[Check your scan results](https://app.stackhawk.com/scans) on the StackHawk platform.

## Workshop Complete

You just automated StackHawk DAST scanning with GitHub Actions!

Read more about [GitHub Actions](https://docs.github.com/en/actions), and check out the [GitHub Actions Marketplace](https://github.com/marketplace?type=actions), where you can find other Actions to build out your pipeline.

Finally, here are some additional resources for tuning StackHawk to scan *your* applications.

* [HawkDocs](https://docs.stackhawk.com), where you can read all the details on how to configure and run HawkScan in your environment.
* [Authenticated Scanning](https://docs.stackhawk.com/hawkscan/authenticated-scanning.html) - Guides for authenticating HawkScan to your application for deeper scans.
* [Continuous Integration](https://docs.stackhawk.com/continuous-integration/), where you can see our guides for integrating HawkScan with the most popular CI/CD systems.
* [StackHawk Blog](https://www.stackhawk.com/blog), with technical tips, tricks, and walkthroughs to help you secure and test your applications.
