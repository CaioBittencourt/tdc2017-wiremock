# Extending WireMock - HPE OneView Resource ID Transformer

This is a simple Java project that demonstrates how to develop and enable WireMock extensions.

Below you will find a short description of the components that are mentioned in the use case scenario, as well as the main requirement that led us to develop this extension.

## Use Case
Simulate several HPE OneView appliances to test the HPE OneView Global Dashboard reports module.

### HPE OneView
The [HPE OneView](https://www.hpe.com/us/en/integrated-systems/software.html) is an HPE software that manages your infrastructure using a software-defined approach. To identify each resource available in the infrastructure, OneView generates an unique resource identifier (UUID) and assigns it to the resource.

### HPE OneView Global Dashboard (HPE OVGD)
The [HPE OneView Global Dashboard](https://www.hpe.com/us/en/product-catalog/detail/pip.1009187269.html) allows you to have a single view of your managed infrastructure, even when it is distributed across several data centers.

### Requirement
Each simulated HPE OneView instance **must** provide a different set of resource identifiers for its managed resources.

## WireMock Extension Mechanism
WireMock allows you to define extensions to its default request/response handling mechanism. To learn how to use the WireMock extension capabilities, please refer to the Extending WireMock [page](http://wiremock.org/docs/extending-wiremock/).

To meet our use case, we developed the following extensions:
* `ResourceIdResponseTransformer`: handles the resource identifier transformation at the response time. It generates new resource identifiers to replace the original ones.
* `ResourceIdRequestMatcher`: matches the request and replaces the received resource identifier by the original value.

## How to enable the extension

1. Build the extension project with [Maven](https://maven.apache.org/):
```bash
mvn clean compile
```

2. Execute WireMock enabling the extension (`--extensions`):
```bash
java -cp "wiremock-standalone-2.10.1.jar:<path>/tdc2017-wiremock/samples/id-transformer/target/classes" \
  com.github.tomakehurst.wiremock.standalone.WireMockServerRunner \
  --extensions com.hpe.wiremock.ResourceIdRequestMatcher,com.hpe.wiremock.ResourceIdResponseTransformer \
  --root-dir <path>/tdc2017-wiremock/samples/id-transformer/src/test/resources/oneview
```
> Remember to replace the `<path>` part of the command by the path to the folder where the repo has been cloned.

Once the WireMock server is running, you can issue the following REST request:
```bash
curl localhost:8080/rest/server-hardware
```
In the WireMock console, you should see all the resource identifiers that were found in the original response, and the generated UUIDs that will replace the original ones in the response body.