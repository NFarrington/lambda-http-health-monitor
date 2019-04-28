# AWS Lambda HTTP Health Monitor

An AWS Lambda function to check the health of an HTTP endpoint.

## Inputs

All inputs are either defined as environment variables or as part of event data. Event data takes priority over
environment variables.

* `ENDPOINT` - The url to be checked
* `METHOD` - HTTP method to use, defaults to `GET`
* `PAYLOAD` - HTTP payload, if `POST` or `PUT` used as method
* `TIMEOUT` - Timeout to use for HTTP requests, defaults to 120s
* `HEADERS` - List of headers to send to target server, defaults to empty list
* `REPORT_RESPONSE_BODY` - Set to 1 if you wish to report on response body, 0 otherwise, defaults to 0
* `REPORT_AS_CW_METRICS` - Set to 1 if you wish to store reported data as CW custom metrics, 0 otherwise, defaults to 1
* `CW_METRICS_NAMESPACE` - If CW custom metrics are being reported, this will determine their namespace, defaults to
'HttpCheck'
* `BODY_REGEX_MATCH` - If CW custom metrics are being reported, this will enable `ResponseBodyRegexMatch` metric to be
published as well, with value determined by success of matching response body against regular expression contained
within this option
* `STATUS_CODE_MATCH` - Report whether the HTTP status code is equal to given status code or not. If this option is not
present, it won't be reported upon. Defaults to empty
* `FAIL_ON_STATUS_CODE_MISMATCH` - If checking for status code match treat mismatch as failure, i.e. report
`Available: 0`

## Outputs

By default, following properties will be rendered in output JSON:

* `Reason` - Reason
* `Available` - 0 or 1
* `TimeTaken` - Time (ms) it took to get response from remote server
* `StatusCode` - HTTP status code
* `ResponseBody` - Optional, by default this won't be reported
* `ResponseBodyRegexMatch` - Optional, if `BODY_REGEX_MATCH` option is provided
* `StatusCodeMatch` - Optional, if `STATUS_CODE_MATCH` options is provided

## CloudWatch Metrics

In order to get some metrics which you can alert on, the `REPORT_AS_CW_METRICS` and `CW_METRICS_NAMESPACE` environment
variables are used. The following metrics will be reported:

* `Available` - 0 or 1, whether response was received in timely manner, indicating problems with network, DNS lookup or
server timeout
* `TimeTaken` - Time taken to fetch response, reported in milliseconds
* `StatusCode` - HTTP Status code received from server
* `ResponseBodyRegexMatch` - Present when `BODY_REGEX_MATCH` option is specified. 1 is reported if the response body
matches the regex provided, or 0 otherwise
* `StatusCodeMatch` - Present when `STATUS_CODE_MATCH` option is specified. 1 is reported if the response status code
matches the code provided, or 0 otherwise

## Configuration & Deployment

This project is designed to be deployed using [Serverless](https://serverless.com/).

Before deploying, modify `serverless.yml` with your own schedules and endpoints. Then run: 

```
serverless deploy
```

## Debugging

If you wish to see debug output for http request, set `HTTP_DEBUG` environment variable to '1'. This can't be controlled
through event payload. 

## License

Licensed under the [MIT license](https://opensource.org/licenses/MIT).
