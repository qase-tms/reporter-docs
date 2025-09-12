---
title: qasectl - Qase CLI tool
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
`qasectl`  is Qase's command line app build in Go Language, which can be used to start and complete test runs, and it can publish results to Qase in several formats.

View source code on GitHub here: `https://github.com/qase-tms/qasectl`

<br />

## How to install the app?

<br />

### Install via go install

The easiest way to install Qase CLI is using go install:

```
go install github.com/qase-tms/qasectl@latest
```

Make sure to add `$GOPATH/bin` to your `$PATH` environment variable to be able to run the `qasectl` command. 

The binary is named `qasectl`, if you'd like, you can create an alias as `qli` to keep it short :)

<br />

### Build from source

**Clone the repository**

```
git clone <https://github.com/qase-tms/qasectl.git> && cd qasectl
```

**Build the binary**

```
make build
```

You will find the binary in the `build/` directory.

Try creating a test run by executing the binary - 

```
./build/qasectl testops run create --project QD --token <your-token> --title "Run created from Qase-cli" --description "Hello, from qase-cli" --environment <env-slug> --verbose
```

<br />

### Use a Docker image

**Pull the Docker image**

```
docker pull ghcr.io/qase-tms/qase-cli:latest
```

**Run the Docker container**

```
docker run --rm ghcr.io/qase-tms/qase-cli:latest <add-your-command-here>
```

<br />

## Using Qase CLI

You can generate an API token from [here](https://app.qase.io/user/api/token) 🔗  and refer to [this guide](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code) 🔗 to find your project code, and run\_id in Qase. 

## Create a test run

You can create a test run by using the `create` command. The `create` command is used to create a new test run in the specified project and save a test run ID to a file.

You can specify the file path using the `--output` option.

If the file path is not specified, the test run ID will be saved to `qase.env` in the current directory.

The file will contain the test run ID in the following format:

`QASE_TESTOPS_RUN_ID=123`

You can use the test run ID in subsequent steps to upload test results for the test run.

For extracting the test run ID from file, use the following command:

```
cat qase.env | grep QASE_TESTOPS_RUN_ID | cut -d'=' -f2
```

Example usage -

```
qasectl testops run create
```

* `-p`  `<project_code>`
* `-t`  `<token>`
* `--title`  `<title>`
* `-d`  `<description>`
* `-e`  `<environment>`
* `-m`  `<milestone>`
* `-o`  `<plan>`
* `-v`

<br />

#### Supported options

**Required:**

* `--project`, `-p`: The project code where the test run will be created.
* `--token`, `-t`: The API token to authenticate with the Qase API.
* `--title`: The name of the test run.

**Optional:**

* `--description`, `-d`: The description of the test run.
* `--environment`, `-e`: The environment where the test run will be executed.
* `--milestone`, `-m`: The milestone of the test run.
* `--plan`: The test plan of the test run.
* `--output`, `-o`: Output path to save the run ID (default is `qase.env` in working directory).
* `--verbose`, `-v`: Enable verbose mode.

The following example shows how to create a test run in the project with the code `DEMO`:

```
qasectl testops run create  
--project DEMO  
--token <token>  
--title "Example test run created using Qase CLI"  
--verbose
```

<br />

## Complete a test run

You can complete a test run by using the `complete` command. The `complete` command is used to complete a test run in the specified project.

Example usage -

```
qasectl testops run complete  
--project <project_code>  
--token <token>  
--id <run_id>  
--verbose
```

Here, the `--id` flag takes the ID of the Qase test run that is to be marked complete.

The following example shows how to complete a test run with the ID `1` in the project with the code `DEMO`:

```
qasectl testops run complete --project DEMO --token <token> --id 1 --verbose
```

<br />

## Upload test results

You can upload test results by using the `upload` command. The `upload` command is used to upload test results for a test run in the specified project.

Example usage -

```
qasectl testops result upload  
--project <project_code>  
--token <token>  
--id <run_id>  
--format <format>  
--path <results_file>  
--batch <batch>  
--verbose
```

#### Supported options

**Required:**

* `--project`, `-p`: The project code where the test results will be uploaded.
* `--token`, `-t`: The API token to authenticate with the Qase API.
* `--id`: The ID of the test run to upload results for. Required if title is not set.
* `--path`: The path to the test results file or folder.
* `--format`: The format of the test results file. Allowed values: `junit`, `qase`, `allure`, `xctest`.

**Optional:**

* `--description`, `-d`: The description of the test results.
* `--steps`: The mode of upload steps for XCTest. Allowed values: `all`, `user`.
* `--batch`: The batch number of the test results. Default value is `200`.
* `--suite`, `-s`: Define the root suite for all the test results.
* `--replace-statuses`, `-r`: The statuses to replace. Optional. Pass like `{\"Passed\": \"Failed\"}` to replace all passed results with failed. **Note**: Use slugs of statuses.
* `--verbose`, `-v`: Enable verbose mode.

The following example shows how to upload test results in the `JUnit` format for a test run with the ID `1` in the project with the code `DEMO`:

```
qasectl testops result upload  
--project DEMO  
--token <token>  
--id 1  
--format junit  
--path /path/to/results.xml  
--verbose
```

Similarly, use the following paths for other result types:

* **Qase format:** `--path /path/to/build/qase-results/results.json`
  * For results generated from` qase-pytest` , use the path: `/path/to/build/qase-results/results/`

* **Allure format:** `--path /path/to/allure-results`

* **XCTest format:** `--path /path/to/xctest-results`

<br />

## Create Environments and Milestones

Similar to creating test runs, you can use the `create` command to create a new environment or milestone in the specified project and save a value to a file.

The path for saving the `qase.env` file can be specified using the `--output` option.

If the file path is not specified, `qase.env` will be saved to the working directory.

The `qase.env` file will contain:

* `QASE_ENVIRONMENT=<slug>`
* or
* `QASE_MILESTONE=<id>`

You can use the environment slug or milestone ID in subsequent steps when creating a new test run.

For extracting the values from the file, use the following commands:

```
cat qase.env | grep QASE_ENVIRONMENT | cut -d'=' -f2
```
```
cat qase.env | grep QASE_MILESTONE | cut -d'=' -f2
```

<br />

### Create an Environment

Example usage -

```
qasectl testops env create  
--project <project_code>  
--token <token>  
--title <title>  
--slug <slug>  
--description <description>  
--host <host>  
--verbose
```

<br />

#### Supported options for env create

**Required:**

* `--project`, `-p`: The project code where the environment will be created.
* `--token`, `-t`: The API token to authenticate with the Qase API.
* `--title`: The title of the environment.
* `--slug`: The slug value of the environment.

**Optional:**

* `--description`, `-d`: The description of the environment.
* `--output`, `-o`: Output path to save `qase.env`.
* `--verbose`, `-v`: Enable verbose mode.

<br />

### Create a Milestone

Example usage -

```
qasectl testops milestone create  
--project <project_code>  
--token <token>  
--title <title>  
--description <description>  
--status <status>  
--due-date <due_date>  
--verbose
```

#### Supported options for milestone create

**Required:**

* `--project`, `-p`: The project code where the milestone will be created.
* `--token`, `-t`: The API token to authenticate with the Qase API.
* `--title`: The name of the milestone.

**Optional:**

* `--description`, `-d`: The description of the milestone.
* `--status`, `-s`: The status of the milestone. Allowed values: `active`, `completed`.
* `--due-date`, `-d`: The due date of the milestone. Format: `YYYY-MM-DD`.
* `--output`, `-o`: Output path to save `qase.env`.
* `--verbose`, `-v`: Enable verbose mode.
