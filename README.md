# Readme

This is a fork of the F5 Ansible collection `f5networks.f5_modules 1.26.0` with the fix for ssl_key_cert module.

# Issue1
## The Issue

The module fails when trying to import a cert and key that was previously imported via the GUI onto the BIG-IP. 
The error message is: `The error was: KeyError: 'sourcePath'`

## The fix

The module makes an API call in the function `read_current_from_device`.
The API call is to the endpoint `GET /mgmt/tm/sys/file/ssl-key/~Common~<key_name>`.

The response for the error is that the response to the API does not have a key `SourcePath` if the certificate and key was initially imported
via the GUI.

Edit the function `read_current_from_device` in the module definition to use the `fullPath` if `sourcePath` is not present in the response.

Sample response when imported via the GUI

``` json
{
	"kind": "tm:sys:file:ssl-key:ssl-keystate",
	"name": "key_apps_test_com.key",
	"fullPath": "key_apps_test_com.key",
	"generation": 1707,
	"selfLink": "https://localhost/mgmt/tm/sys/file/ssl-key/key_apps_test_com.key?ver=16.1.3.4",
	"checksum": "SHA1:1704:f88e7557eeea533dddb01ff7b4acfcbe7ea777af",
	"createTime": "2023-09-08T04:46:14Z",
	"createdBy": "sudarshanv",
	"curveName": "none",
	"keySize": 2048,
	"keyType": "rsa-private",
	"lastUpdateTime": "2023-09-08T04:46:14Z",
	"mode": 33184,
	"revision": 1,
	"securityType": "normal",
	"size": 1704,
	"updatedBy": "sudarshanv",
	"certOrderManagerReference": {
		"link": "https://localhost/mgmt/tm/sys/file/ssl-key/~Common~key_apps_test_com.key/cert-order-manager?ver=16.1.3.4",
		"isSubcollection": true
	}
}
```

Sample response when imported via the API

``` json
{
	"kind": "tm:sys:file:ssl-key:ssl-keystate",
	"name": "key_apps_test_com.key",
	"fullPath": "key_apps_test_com.key",
	"generation": 1635,
	"selfLink": "https://localhost/mgmt/tm/sys/file/ssl-key/key_apps_test_com.key?ver=16.1.3.4",
	"checksum": "SHA1:1703:21da6be0b2f62ee84fa01b889b42dd37fdf54fe1",
	"createTime": "2023-09-08T04:38:36Z",
	"createdBy": "9770us-f5-automation",
	"curveName": "none",
	"keySize": 2048,
	"keyType": "rsa-private",
	"lastUpdateTime": "2023-09-08T04:38:36Z",
	"mode": 33184,
	"revision": 1,
	"securityType": "normal",
	"size": 1703,
	"sourcePath": "file:///var/config/rest/downloads/key_apps_test_com.key_key",
	"updatedBy": "9770us-f5-automation",
	"certOrderManagerReference": {
		"link": "https://localhost/mgmt/tm/sys/file/ssl-key/~Common~key_apps_test_com.key/cert-order-manager?ver=16.1.3.4",
		"isSubcollection": true
	}
}
```

# Issue 2

## The Issue

The `bigip_profile_client_ssl` options does not have the ability to set `no-dtlsv1.1` and `no-dtlsv1.2` options.

## The Fix

Added the two options to the module source code.

# Author

Sudarshan Vijaya Kumar
