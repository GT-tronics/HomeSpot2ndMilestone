# Scan and Connect APIs
This session documents the DataExchanger Scan and Connect APIs and show the usage examples.

* [Start BLE Scan](#start-ble-scan)
* [Stop BLE Scan](#stop-ble-scan)
* [Connect BLE Device](#connect-ble-device)
* [Disconnect BLE Device](#disconnect-ble-device)

## Start BLE Scan
**bleStartScan(success, failure) : boolean**
### Description
* start BLE scan and wait for the report of scanned devices
### Parameters
* *success* : callback function
    * called for each discovered device
        * repeatedly if the device is kept advertising
    * callback object is the scan result contains:
        ```
        {
            "state" : string("active" or 
            inactive"),
            "info" :
            {
                "NAME" : string("Device Name"),
                "CONNECTABLE" : boolean(true or false),
                "UUID" : string("Device UUID"),
                "TXPWR" : number(transmit power),
                "RSSI" : number(receive power)
            }
        }
        ```
* *failure* : callback function
    * call when any failure occurs during scan
    * callback object alway contains
        ```
        {
            "retCode' : number(return code),
            "status" : string(error description)
        }
        ```
### Return 
* boolean
    * true - successful
    * false - DX not initialized or scanning is already in progress
### Examples
```
export class HomePage {
    ...

    constructor(
        ...
        public dispatcher : AtCmdDispatcherService
    ) 
    {
        ...
    }

    ...

    this.dispatcher.bleStartScan(
        successObj => {
            console.log("[HOME] scan success " + JSON.stringify(successObj));

            // Add code here to process the scan result. 
            // - for example, update the device list UI
            // - check for successObj.active for the device availability. If false,
            //   it means the device is no longer available (i.e. not advertising 
            //   any more), therefore it cannot be connected 
        },
        failureObj => {
            console.log("[HOME] scan failure " + failureObj.status);
        }
    );

    ...
}
```
## Stop BLE Scan
**bleStopScan() : void**
### Description
* Stop BLE scan immediately
### Parameters
* None
### Return
* None
### Example
```
export class HomePage {
    ...

    constructor(
        ...
        public dispatcher : AtCmdDispatcherService
    ) 
    {
        ...
    }

    ...

    this.dispatcher.bleStopScan();

    ...
}
```
## Connect BLE Device
**bleConnect(deviceUUID, timeout) : Promise**
###Description
* Connect a particular BLE device
### Parameters
* *deviceUUID* : string
    * device UUID want to be connected
    * this should come from the scan result.
* *timeout* : number
    * timeout in milli-second before declaring connect not successful.
### Return          
* Promise
    * then object
        * none
    * catch object
        * return object:
            ```
            {
                "retCode' : number(return code),
                "status" : string(error description)
            }
            ``` 
### Example
```
export class HomePage {
    ...

    constructor(
        ...
        public dispatcher : AtCmdDispatcherService
    ) 
    {
        ...
    }

    ...

    // Start scan

    ...
    
    // Stop scan

    ...

    // Connect

    // Get the 1st device from unlinked list
    var unlinkList : BleDeviceInfo[] = this.dispatcher.getUnlinkDevices();
    var devInfo : BleDeviceInfo = unlistList[0];
    var connectTimeout : number = 10000;  // 10s

    this.dispatcher.bleConnect(devInfo.uuid, connectTimeout).then( ret => {
        console.log("[Home] Connected [" + ret.status + "]");
    }).catch( ret => {
        console.log("[Home] Connect fail [" + ret.status + "]");
    });

    ...
}
```

## Disconnect BLE Device
**bleDisconnect(deviceUUID) : void**
### Description
* Disconnect a particular BLE device
### Parameters
* *deviceUUID* : string
    * device UUID want to be disconnected
    * this should come from the connected device.
### Return
* None
### Example
```
export class HomePage {
    ...

    constructor(
        ...
        public dispatcher : AtCmdDispatcherService
    ) 
    {
        ...
    }

    ...

    // Start scan

    ...

    // Stop scan

    ...

    // Connect

    ...

    // Disconnect (all)

    var linkedList : BleDeviceInfo[] = this.dispatcher.getLinkedDevices();

    for ( devInfo : BleDeviceInfo in linkedList )
        if( devInfo.state == DevState.CONNECTED )
        {
            this.dispatcher.bleDisconnect(devInfo.uuid).catch( ret => {
                console.log("[Home] Disconnect fail " + JSON.stringify(ret));
        }
    });
    
    ...
}
```

#
Next: [Device List]()


