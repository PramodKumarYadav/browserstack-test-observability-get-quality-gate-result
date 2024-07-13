# browserstack-test-observability-get-quality-gate-result

To get quality gate result from browserstack test observability API

## Why this action?
BrowserStack has created an amazing product called "[Test Obervability](https://observability.browserstack.com/home)". 

They provide [two examples](https://www.browserstack.com/docs/test-observability/quality-gate/integrate-via-api) of how to integrate their Quality Gate API in your continous integration workflows. One of it is generic shell script and another one a pipeline example for Jenkins.
![Options to integrate in CI](./assets/image.png)

Since many companies use GitHub as their CICD solution, this action provides a 3rd (and direct alternative) to get result for their Quality Gate API endpoint for a project and a build.

And that is the reason of existance of this particular action.

## Below is an example to use quality-gate-result from both within a job and from another job

```yaml {"id":"01J2NSXS32KV8TSMM4W64D9WMT"}
on:
  workflow_dispatch:

name: Get BrowserStack Result
jobs:
  browserstack_result:
    name: Get BrowserStack Result
    runs-on: ubuntu-latest
    outputs:
      build-id: ${{ steps.get_browserstack_result.outputs.build-id }}
      quality-gate-result: ${{ steps.get_browserstack_result.outputs.quality-gate-result }}
    steps:
    - uses: actions/checkout@v4

    - name: Get BrowserStack Result
      uses: PramodKumarYadav/browserstack-test-observability-get-quality-gate-result@v3
      id: get_browserstack_result
      with:
        project-name: 'your-test-project-name'
        build-name: 'your-test-build-name'
        browserstack-username: ${{ secrets.BROWSERSTACK_USERNAME }}
        browserstack-access-key: ${{ secrets.BROWSERSTACK_ACCESS_KEY }}
        timeout-in-seconds: 60

    - name: Print BrowserStack Result in same job
      run: |
        echo "Build ID: ${{ steps.get_browserstack_result.outputs.build-id }}"
        echo "Quality Gate Result: ${{ steps.get_browserstack_result.outputs.quality-gate-result }}"
  
  use_browserstack_result:
    name: Print BrowserStack Result in another job
    runs-on: ubuntu-latest
    needs: browserstack_result
    steps:
    - name: Use BrowserStack Result
      run: |
        echo "Build ID: ${{ needs.browserstack_result.outputs.build-id }}"
        echo "Quality Gate Result: ${{ needs.browserstack_result.outputs.quality-gate-result }}"
```

## Reference output builds/latest endpoint

For reference, the output from the endpoint to fetch latest build: https://api-observability.browserstack.com/ext/v1/builds/latest, looks as below.

```
{
  "name": "run-health-checks-on-dev",
  "description": "",
  "status": "passed",
  "duration": 68659,
  "user": "Pramod Yadav",
  "user_id": 8481234,
  "tags": [],
  "alerts": [],
  "build_id": "qxryt5weew4eymbusfjtvvqpzph6myjehd10g0tu",
  "build_number": 276,
  "original_name": "run-health-checks-on-dev",
  "finished_at": "2024-07-12T16:04:40.779+00:00",
  "started_at": "2024-07-12T16:03:32.120+00:00",
  "status_stats": {
    "passed": 4,
    "failed": 0,
    "pending": 0,
    "skipped": 0,
    "unknown": 0
  },
  "failure_categories": {
    "To be Investigated": 0,
    "Automation Bug": 0,
    "Product Bug": 0,
    "No Defect": 0,
    "Environment Issue": 0
  },
  "smart_tags": {
    "is_flaky": 1,
    "is_always_failing": 0,
    "is_performance_anomaly": 0,
    "is_new_failure": 0
  },
  "unique_errors": {
    "overview": {
      "insight": "0% failures caused by 0 unique errors",
      "count": 0
    },
    "top_unique_errors": []
  },
  "re_run": null,
  "build_error": null,
  "is_archived": false,
  "observability_version": {
    "framework_version": "1.45.1",
    "framework_name": "Playwright",
    "outdated_sdk_version": "false",
    "sdk_version": "1.34.1"
  },
  "vcs_info": {
    "name": "git",
    "sha": "3e891b612836d31b2c9f0611bf44cdffa8820350",
    "branch": "main",
    "remotes": [
      {
        "url": "https://github.com/PramodKumarYadav/sandbox-repo",
        "name": "origin"
      }
    ],
    "url": "https://github.com/PramodKumarYadav/sandbox-repo/commit/3e891b612836d31b2c9f0711bf44cdffa8820350"
  },
  "ci_info": {
    "job_name": "💓 Trigger Health Checks on dev",
    "name": "GitHub Actions",
    "build_number": "9910758604",
    "build_url": "https://github.com/PramodKumarYadav/sandbox-repo/actions/runs/9927758604",
    "url": null
  },
  "host_info": {
    "hostname": "63018c48bee5",
    "os": null
  },
  "run_information": [
    {
      "passed": 3,
      "failed": 0,
      "id": "14591741",
      "skipped": 0,
      "unknown": 0
    },
    {
      "passed": 0,
      "failed": 0,
      "id": "14591742",
      "skipped": 0,
      "unknown": 0
    },
    {
      "passed": 0,
      "failed": 0,
      "id": "14591744",
      "skipped": 0,
      "unknown": 0
    },
    {
      "passed": 0,
      "failed": 0,
      "id": "14591747",
      "skipped": 0,
      "unknown": 0
    },
    {
      "passed": 3,
      "failed": 0,
      "id": "14591749",
      "skipped": 0,
      "unknown": 0
    },
    {
      "passed": 0,
      "failed": 0,
      "id": "14591784",
      "skipped": 0,
      "unknown": 0
    }
  ],
  "observability_url": "https://observability.browserstack.com/projects/sandbox-repo/builds/run-health-checks-on-dev/158"
}
```

## Reference output builds/latest endpoint
For reference, the output from the endpoint to fetch quality gate result: https://api-observability.browserstack.com/ext/v1/quality-gates, looks as below.

```
{
  "status": "passed",
  "build_uuid": "rdjrhaccnctnyue5zpxch29enfew6icqnc8uainy",
  "build_url": "https://observability.browserstack.com/projects/sandbox-repo/builds/run-health-checks-on-dev/127",
  "quality_gate_result": "passed",
  "quality_profiles": [
    {
      "id": "dd2a2735-6c5b-41e8-a958-cda884336a2b",
      "name": "Quality Gate",
      "type": "fail if",
      "result": "passed",
      "rules": [
        {
          "condition": "Failure category to be investigated is automatically mapped to any tests in the build",
          "value": "failed",
          "result": "Absent",
          "regex": null,
          "testTags": null,
          "folders": null,
          "alert": null
        }
      ]
    }
  ]
}

```