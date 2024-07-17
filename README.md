# browserstack-test-observability-get-quality-gate-result

To get quality gate result from browserstack test observability API

## Why this action?

BrowserStack has created an amazing product called "[Test Obervability](https://observability.browserstack.com/home)".

They provide [two examples](https://www.browserstack.com/docs/test-observability/quality-gate/integrate-via-api) of how to integrate their Quality Gate API in your continous integration workflows. One of it is generic shell script and another one a pipeline example for Jenkins.

<img src="./docs/image.png" alt="Available options to integrate in CI at the time of writing this action" width="400" height="200" />

Since many companies use GitHub as their CICD solution, this action provides a 3rd (and direct alternative) to get result for their Quality Gate API endpoint for a project and a build.

And that is the reason of existance of this particular action.

## Inputs

```yaml {"id":"01J2XFHJFST5N0A1651KZ5JCAT"}
inputs:
  project-name:  
    description: 'name of the project'
    required: true
  build-name: 
    description: 'name of the build'
    required: true
  # Recommended to set custom tag with github run_number while running the test command as shown here: CUSTOM_TAG_1="${{ github.run_number }}" npx browserstack-node-sdk playwright test. You can then pass ${{ github.run_number }} to build-tags input to always target the right build.
  build-tags: 
    description: 'name of the custom build tags'
    required: true
  browserstack-username:  
    description: 'BrowserStack username'
    required: true
  browserstack-access-key:  
    description: 'BrowserStack API value'
    required: true
  timeout-in-seconds:  
    description: 'timeout duration in seconds after which pooling for result stops'
    required: false
    default: 120
```

## Outputs

```yaml {"id":"01J2XFHJFST5N0A1651MMCD9FR"}
outputs:
  build-id:
    description: "Build ID"
    value: ${{ steps.get_build_id.outputs.BUILD_ID }}
  quality-gate-result:
    description: "Quality Gate Result"
    value: ${{ steps.get_quality_gate_result.outputs.QUALITY_GATE_RESULT }}

```

## Example usage

Below is an example that shows how you can use quality-gate-result both

- From within a job
- And from another job

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
      uses: PramodKumarYadav/browserstack-test-observability-get-quality-gate-result@v5
      id: get_browserstack_result
      with:
        project-name: 'your-test-project-name'
        build-name: 'your-test-build-name'
        build-tags: 'your-custom-build-tag-name' 
        browserstack-username: ${{ secrets.BROWSERSTACK_USERNAME }}
        browserstack-access-key: ${{ secrets.BROWSERSTACK_ACCESS_KEY }}
        timeout-in-seconds: 60

    - name: Print BrowserStack Result in same job (and fail if quality gate failed)
      run: |
          echo "Quality Gate Result: ${{ steps.get_browserstack_result.outputs.quality-gate-result }}"
          if [[ "${{ steps.get_browserstack_result.outputs.quality-gate-result }}" == "failed" ]]; then
            exit 1
          fi
  
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

## Published artifacts

Two artifacts will be published on GitHub run that contains full response from get latest build api and quality gates api. Sample artifacts are below:

1. [latest-build-run-details.json](./docs/latest-build-run-details.json)
2. [quality-gate-result.json](./docs/quality-gate-result.json)

> [!NOTE]
>
> - Get Latest build uuid API endpoint used: GET "https://api-observability.browserstack.com/ext/v1/builds/latest?project_name=your-test-project-name&build_name=your-test-build-name"
> - Quality gate API endpoint used: GET https://api-observability.browserstack.com/ext/v1/quality-gates/{build_uuid}

## Reference

To create and push new tags:

```sh {"id":"01J2XFHJFT1K765K3D5J6BDSSC"}
pramodyadav@Pramods-Laptop browserstack-test-observability-get-quality-gate-result % git tag -a -m "add your message here" v7                   
pramodyadav@Pramods-Laptop browserstack-test-observability-get-quality-gate-result % git push --follow-tags   
```