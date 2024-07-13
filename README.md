# browserstack-test-observability-get-quality-gate-result

To get quality gate result from browserstack test observability API

## Below is an example to use quality-gate-result from both within a job and from another job

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
      uses: actions/browserstack-test-observability-get-quality-gate-result@v1
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
  