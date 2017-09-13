# purity-fb
Client for Purity//FB REST API (1.0 - 1.1), developed by [Pure Storage, Inc](http://www.purestorage.com/). Documentations can be found at [purity-fb.readthedocs.io](http://purity-fb.readthedocs.io/).

This Python package is automatically generated by the [Swagger Codegen](https://github.com/swagger-api/swagger-codegen) project:

- API version: 1.1
- Package version: 1.1
- Build package: io.swagger.codegen.languages.PythonClientCodegen
For more information, please visit [http://www.purestorage.com](http://www.purestorage.com)

## Requirements.

Python 2.7 and 3.4+

## Installation & Usage
### pip install

There are two ways to use pip to install.

The first is the easiest, i.e., using pypi:

```sh
pip install purity_fb
```

The second is to install from Github:



```sh
pip install git+https://github.com/purestorage/purity_fb_python_client.git
```
(you may need to run `pip` with root permission: `sudo pip install git+https://github.com/purestorage/purity_fb_python_client.git`)

Then import the package:
```python
import purity_fb 
```

### Setuptools

Install via [Setuptools](http://pypi.python.org/pypi/setuptools).

```sh
python setup.py install --user
```
(or `sudo python setup.py install` to install the package for all users)

Then import the package:
```python
import purity_fb
```

## Getting Started

Please follow the [installation procedure](#installation--usage) and then run the following:

```python
from purity_fb import PurityFb, FileSystem, FileSystemSnapshot, SnapshotSuffix, rest

# create PurityFb object for a certain array
fb = PurityFb("10.255.8.20")
fb.disable_verify_ssl() # this is required because Purity//FB 2.1 only supports self-signed certificate
try:
    fb.login("T-e7e551be-fe5d-4669-baf5-670cd8ea0560")

    ## manage file systems
    fs_obj = FileSystem(name="myfs", provisioned=50000)
    # create a file system named myfs
    fb.file_systems.create_file_systems(fs_obj)
    # list all existing file systems
    fb.file_systems.list_file_systems()
    # destroy the file system myfs
    fb.file_systems.update_file_systems(name="myfs", attributes=FileSystem(destroyed=True))
    # eradicate the file system myfs
    fb.file_systems.delete_file_systems(name="myfs")

    ## manage file system snapshots
    fb.file_system_snapshots.create_file_system_snapshots(sources=["myfs"], suffix=SnapshotSuffix("mysnap"))
    fb.file_system_snapshots.list_file_system_snapshots()
    # destroy the file system snapshot myfs.mysnap
    fb.file_system_snapshots.update_file_system_snapshots(name="myfs.mysnap", attributes=FileSystemSnapshot(destroyed=True))
    # eradicate the file system snapshot myfs.mysnap
    fb.file_system_snapshots.delete_file_system_snapshots(name="myfs.mysnap")

    fb.logout()
except rest.ApiException as e:
    print("Exception: %s\n" % e)
```

## Documentation for PurityFb

A *PurityFb* object represents a FlashBlade device.

```class purity_fb.PurityFb(host, version=None, api_token=None)```

### *host*
The argument *host* is required, which is the IP address or host name of the device.
### *version*
The optional argument *version* is the REST version number that the client wants to target at.
If absent, the latest version is chosen.
### *api_token*
The optional argument *api_token* will be used to log into the array if specified.


### Methods


Two methods are provided for logging into and out from a device.

Method | Parameters | Description
------------- | ------------- | -------------
*disable_verify_ssl* | none | Disable certificate verification for SSL. **This method must be executed before any other calls.**|
*list_availabe_versions* | none | List all available REST versions of the array. Public, authentication not required. |
*login* | *api_token* | Login to the REST server with a specified *api-token*. **This method must be executed successfully before making any function calls to any APIs.** | 
*logout* | n/a | Logout from the REST server |


Note: Purity//FB 2.1 doesn't support configuring certificates. It uses
a self signed certificate. Using a self-signed certificate will result in the following error:

```ssl.SSLError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed (_ssl.c:749)```

Therefore, it is required to call *disable_verify_ssl* before making any other function calls.
Once SSL verification is disabled, there will be warnings like the following:


```python
/opt/local/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/urllib3-1.20-py3.6.egg/urllib3/connectionpool.py:852: InsecureRequestWarning: Unverified HTTPS request is being made. Adding certificate verification is strongly advised. See: https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
  InsecureRequestWarning)
```
These warnings could be disabled using [urllib3.disable_warnings](http://urllib3.readthedocs.io/en/latest/reference/index.html#urllib3.disable_warnings).


### **Endpoint Properties**

Once *login* succeeds, different endpoints could be accessed through the following read-only properties of *PurityFb* objects.


#### **Non-version Endpoints**

First, there are general endpoints that are not specific to versions, including *api_version*
and *authentication*.

Property | Type | Descripstion
------------ | ------------- | -------------
*api_version* | [**VersionApi**](docs/VersionApi.md)  | Public APIs for querying supported REST versions. |
*authentication* | [**AuthenticationApi**](docs/AuthenticationApi.md)  | Login/logout APIs. |


#### **Versioned Endpoints**

Second, there are versioned endpoints that are secured by authentication and could be different from version to version.
One can specify the *version* parameter when creating a *PurityFb* object to choose APIs for a specific REST version.
If *version* is not specified, by default, the most recent REST version available on the server is chosen.

Version | Property | Type | Descripstion
------------ | ------------ | ------------- | -------------
1.1 | *file_systems* | [**FileSystems1dot1Api**](docs/FileSystems1dot1Api.md) | APIs for the file system endpoint (**create**, **list**, **update** and **delete**) |
1.1 | *file_system_snapshots* | [**FileSystemSnapshots1dot1Api**](docs/FileSystemSnapshots1dot1Api.md) | API for the file system snapshots endpoint (**create**, **list**, **update** and **delete**) |
1.0 | *file_systems* | [**FileSystems1dot0Api**](docs/FileSystems1dot0Api.md) | APIs for the file system endpoint (**create**, **list** and **update**) |
1.0 | *file_system_snapshots* | [**FileSystemSnapshots1dot0Api**](docs/FileSystemSnapshots1dot0Api.md) | API for the file system snapshots endpoint (**create** and **list**) |


#### **Beta Endpoints**

Third, there are beta APIs that are not guaranteed to work.

Property | Type | Descripstion
------------ | ------------- | -------------
*file_system_beta* | [**FileSystemsBetaApi**](docs/FileSystemsBetaApi.md) | API for the beta file system endpoint  (**delete** only) |
*file_system_snapshot_beta* | [**FileSystemSnapshotsBetaApi**](docs/FileSystemSnapshotsBetaApi.md) | API for the beta file system snapshots endpoint (**delete** only) |


#### Call with HTTP information
Every method of each endpoint returns an object representing the body of the response from the server.
If other information such as response status code or response header is needed, the corresponding method 
```XXX_with_http_info()``` can be used. 
  
For example,

```python
fb = purity_fb.PurityFb("10.255.3.20")
fb.disable_verify_ssl()
fb.login("T-1eeb15b4-1288-49b2-b0cc-5a7c9e5d524f")
data = fb.file_systems.create_file_systems(FileSystem(name="myfs"))
```

Here *response_data* has type [FileSystemResponse](docs/FileSystemResponse.md). 
And if call with HTTP information, both the response status and header will be returned as well.

```(data, status, header) = fb.file_systems.create_file_systems_with_http_info(FileSystem(name="myfs"))```

### Request Timeout Configuration


#### Global Request Timeout

The property *PurityFb.request_timeout* allows one to get or update the global request timeout used by default for
all API calls. The default *request_timeout* is 10 seconds for connection, and 30 seconds for read.


Property | Type | Descripstion
------------ | ------------- | -------------
*request_timeout* | [**urllib3.Timeout**](http://urllib3.readthedocs.io/en/latest/reference/urllib3.util.html) | Set or get the global request timeout for accessing the device. |


For example, ```fb.request_timeout = urllib3.Timeout(connect=8.0, read=20.0)```. This updates all API call to have 8 seconds connection
timeout and 20 seconds read timeout by default.


#### One-time-only Request Timeout


All APIs support the keyword parameter *_request_timeout* 
(of type [**urllib3.Timeout**](http://urllib3.readthedocs.io/en/latest/reference/urllib3.util.html) ) 
to specify request timeout for a specific API call.

For example, 

```fb.file_systems.create_file_systems(FileSystem(name="myfs"), _request_timeout=urllib3.Timeout(connect=5.0, read=15.0))}} ```


### Retries

The property *PurityFb.request_retry* allows one to get or update the global retry setting used by default for
all API calls. The default *request_retry* is 5 retries per call.


Property | Type | Descripstion
------------ | ------------- | -------------
*request_retry* | [**urllib3.Retry**](http://urllib3.readthedocs.io/en/latest/reference/urllib3.util.html) | Set or get the global request timeout for accessing the device. |


For example, ```fb.request_retry = urllib3.Retry(total=20, connect=15, read=15)```. This updates all API call to have 
at most 20 retries in total, among which at most 15 connection retries and at most 15 read retries.


## Documentation For Models

 - [ErrorResponse](docs/ErrorResponse.md)
 - [FileSystem](docs/FileSystem.md)
 - [FileSystemResponse](docs/FileSystemResponse.md)
 - [FileSystemSnapshot](docs/FileSystemSnapshot.md)
 - [FileSystemSnapshotResponse](docs/FileSystemSnapshotResponse.md)
 - [LoginResponse](docs/LoginResponse.md)
 - [NfsRule](docs/NfsRule.md)
 - [ObjectResponse](docs/ObjectResponse.md)
 - [PaginationInfo](docs/PaginationInfo.md)
 - [ProtocolRule](docs/ProtocolRule.md)
 - [PureError](docs/PureError.md)
 - [PureObject](docs/PureObject.md)
 - [SnapshotSuffix](docs/SnapshotSuffix.md)
 - [Space](docs/Space.md)
 - [VersionResponse](docs/VersionResponse.md)


## Documentation For Authorization


## ApiTokenQueryParam

- **Type**: API key
- **API key parameter name**: api-token
- **Location**: URL query string

## AuthTokenHeader

- **Type**: API key
- **API key parameter name**: x-auth-token
- **Location**: HTTP header


## Author

info@purestorage.com

